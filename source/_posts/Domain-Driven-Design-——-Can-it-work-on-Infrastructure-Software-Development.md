---
title: Domain Driven Design —— Can it work on Infrastructure Software Development?
mathjax: false
comments: true
date: 2021-01-04 23:57:41
tags: [软件工程, DDD, TiKV]
summary: Discussed whether we can adopt some patterns in Domain Driven Design to TiKV.
---

I used to work on some Web products, and leared a lot about domain driven design(DDD), that's a useful design method, and works well for making concepts clear during the development process. Many patterns introduced in DDD do helped me a lot.

And now I'm working on some database (TiKV, and its trasaction part for specific) development. Though might be less "infrastructure" than compiler and operating system (but might be harder than these two), database is still a kind of infrastructure software. And I found that **some** of DDD's practice remain good here.

Recently, with the development process of async-commit and 1pc, I found the complexity of our software is becoming higher and higher, many compatable problems occurred, so I wish we may take some good practice in DDD when designing our software.

## What's so special about Infrastructure Software?

If you read the DDD book, you may find most examples there are not from infrastructure software, it is reasonable because it is easier to show the benefits to integrate domain knowledge into the model and the software with some non-infrastructure cases. Most developers[^1] of infrastructure software are just the expert of the domain, they have already know the domain well.

In the meanwhile, the performance of infrastructure software is always much import than ordinary softwares, and some patterns introduced in DDD do have costs on performance. For example, we do can make many of our functions side-effect-free, but it will lead to more network or disk access, and make the performance regression, for the same reason, sometimes we cannot seperate a function which seems do different things into some small ones. Performance do force us to do some trade off.

In my opinion, there are some patterns we can learn from DDD, while others won't apply here.

## Patterns can we take from DDD

### Ubiquitous Language

I think this is the most important pattern! And we already kind-of have it! Most concept of our domain are just terminology in database domain, and we are familiar with them. 

There are also some implementation related concepts, which I think the names in the code has become our Ubiquitous Language. This one is the part we always met problem with, for example, even the SQL Team manager don't know what is "DistinctScore" (neither do I), which is only used in PD, and for us who always work on TiKV and seldom touch TiDB, amendSchema, which is always simplified as amend, is some how confusing.

The tricky part is, some of our Ubiquitous Language has ambiguity[^2], I found at least 3 different `Mutation` types in our code (TiKV and client-rust), and they stand for different things, which is confusing when reading the code. I think always specifying the module name is a good practice in these situations, and different names would be even better.

I'm tring to collect some "dictionary" [here](https://github.com/longfangsong/TiDictionary) to give us a place for searching around our Ubiquitous Language, including monitoring items.

### Model-Driven Design

Now I think this one is most important for us: our code is somehow not so tightly related with our model. Most our model is aimed for one feature rather than the whole picture. Some times I think maybe we pay too much attention on "features" (which is reasonable because features do can bring us real benefits and deliverable for costomers) instead of the existing codes and models[^4] (which I think are also important). So, as a result, we have design doc for Pessimistic Lock, for large transaction, for async commit, for 1pc, and for downstream components like cdc, regardless (while sometimes trying to regard, but not very comprehensive) of the fact that these things work together, with a shared (raft) storage, and some even share the same requests/commands. I think we should have a unified model for all of these, or just implement these in separated structures. Maybe we can ident which kind of transaction each request is in, and use it to dispatch these request to different executing routes.

And, I don't know, maybe we stick too hard to the model percolator provide us, so adding new feature become hard and make the code ugly, would it be better if we do some large change, eg. many of our tricks are done by doing some magical encode/decode on keys, but what if we add some more column family, eg. `gc_fence` which temporary lives in the write CF now, into that model?

### Hands-on Modelers

It's lucky we don't really have something like consultants now, everyone can and design the model and write the code.

### Layered Architecture

TiKV and our transaction layer has already layered! We have command & scheduler layer, actions layer and mvcc layer.

### Entities

I think commands are entities. And in fact we have transaction as an entity, distinguished by its `start_ts`, though we cannot make it a clear object.

### Value Objects

Don't find a clear usage now.

### Services

This is an interesting pattern, I think action module provide a lot of services, some of them are too simple to make into a struct/trait, a function is enough. But for huge functions like prewrite, make it a real Service is necessary. I think `Prewriter` might be an attempt of doing so.

### Modules

Thanks to Rust, we can devide modules in a natural way. Meanwhile Golang suffers problems about circular reference among modules, which requires carefully design.

### Aggregates

Don't find a clear usage now.

### Factories

I think `Builder` is similar with this, and it is already a common pattern in TiKV, while not so related to modeling.

Maybe we can add some more factories, eg. factories to construct commands from requests, if a simple `into` method can't handle it, but it is definitly not the case now.

### Repositories

I used to thinking whether it would be better if we have a repository for transactions, and finally found out this approach will result in a centralized transaction coordinator, which is definitely not we want.

But this one could be useful in monitoring, which I'll work on next Sprint, we'll see.

### Specification

This one is interesting! I think we can apply this in a lot of places. Currently our code is full of code to check for rules during the trasaction handling stage, and a cohesive specification struct instead of a bunch of condition judges everywhere might result in better design.

### Intention-Revealing Interfaces

I think there're some not so intention-revealing parts in our code, take the `Request`s as an example, we always use the word "version" or "start version", but in fact, sometimes these value represents the transaction id in these requests, and other times they reprents the start time of the transaction, all these usages are not really a version. But I'm afraid it is too late to do an interface changing which will break our backward compatibility 😭

### Side-Effect-Free Functions

Just as I mentioned, due to some performance issues, we cannot make many our functions side-effect-free. I think the MvccTxn and the reader in it has a lot of read functions which seems should be side-effect-free but in fact not. Many of them are related with putting cursors at a certain place. Hope we can find a good way to optimize this.

### Assertions

This is the pattern I want to put great emphasis on: there are many rules (pre/post-conditions of function call) in our code, and some of them are hidden, we need to check the code carefully to find it out. We should make them clear by using assertions. [Here](https://github.com/longfangsong/tikv/blob/ec2e1362af8bc769bd0a80dd0176eb8bddaf2c17/src/storage/txn/actions/prewrite/mutation.rs#L142) is an example in TiKV. And [here](https://github.com/longfangsong/client-rust/blob/ccea287e2261e2777254ee03ddd1c73832fba4b5/src/transaction/transaction.rs#L618) is an example in client-rust, which do helped me found an bug[^3].

DDD also suggest to check pre/post-conditions during the unit test, which I think we can put more work on.

One thing to consider when using assertions is whether the condition should be checked by using assertion or return a run time error, eg. I don't know whether we should `assert!(for_update_ts>0)` or just return some run time error when the user tries to acquire pessimistic lock in an optimistic transaction.

### Conceptual Contours

I think the forming of `sig`s are kind of division on our developers according to Conceptual Contours.

Maybe we can show Conceptual Contours among concepts after I collect enough words in my dictionary, but AFAIK, knowing things outside the Conceptual Contour you are working on can help diagnosis problems on the whole system, which is an important skill for oncalls.

### Standalone Classes

Don't find a clear usage now.

### Closure of Operations

Don't find a clear usage now.

### Declarative Design

This one is what I really want to have, I think this is really import for make our product to have less bugs.

See [intention-revealing interfaces](#Intention-Revealing Interfaces), [side-effect-free functions](#Side-Effect-Free Functions), and [assertions](#Assertions) for details.

### Bounded Context

I think we somehow has several bounded contexts, things like transaction layer, mvcc layer, raft layer can be regarded as having their own bounded context, though sometimes we have to went out of the context bondary for performance optimization. We always see relational-database related things in TiKV.

### Continuous Integration

We've done a great job at doing Continuous Integration on our produces, CI runs for every pr and TiPocket can check how components other than TiDB/PD/TiKV works with them. But I'm not very sure whether we done a great job at doing Continuous Integration on our concepts.

### Context Map

I hope this one can solve some of our incompatible problems.

We should:

> Describe the points of contact between the models, outlining explicit translation for any communication, highlighting any sharing, isolation mechanisms, and levels of influence.

For different parts in our software.

For example, I don't really know what property of our transaction layer must hold for cdc to work properly, and I cannot find a document for that.

### Shared Kernel

Don't find a clear usage now.

### Customer/Supplier Development

This is another pattern which is important to prevent compatibility issues. We should consider all downstream[^5] components when designing features.

To achieve these, we probably need to:

- Identify all our downstream components, AFAIK, these components are:
  - TiDB (or in a more detail style: DDL and DML)
  - TiSpark
  - TiFlash
  - PD
  - TiKV clients
  - CDC
  - BR
  - Lightning
- Identify how they expect TiKV txn system to work ([this](https://docs.google.com/document/d/1pbtEx49F9VXJzphCt-PzfypOk_DVKCkD0F3BXoioDO4/edit#heading=h.9xq88so7dy1q) is a good example), and **write them down**. Maybe we need to add several related docs in `sig-transaction`.
- Write tests according to these specs, and use them to check our implementation.
- Consider all these spec when trying to implement something new.

### Conformist

Don't find a clear usage now.

### Anticorruption Layer

Don't find a clear usage now.

### Separate Ways

I'm always considering do some clear separation between differnt kinds of transaction. Do we really benefit a lot from sharing codes among different transaction types? What if we completly seperatate the handling process of different kinds of transactions, and extract the **Shared Kernel**? Will it be better if we keep some duplicate code but make the process of handling different kinds of transaction?

### Open-host Service

Can we implement several "hooks" during the whole process of transaction handling, and systems which need to preserve this process can use these hooks to check the status like cdc instead of designing an interface by their own?

### Published Language

I hope there would be a transation DSL just like [CML](https://en.wikipedia.org/wiki/Chemical_Markup_Language) in chemistry. We can do a lot of things with it, like [visualizing the process of a txn](https://github.com/longfangsong/txn-lab), implement a integration test engine with it as an input(which I think the raft layer is already doing), and maybe even some "Domain Specific Proof Assistant".

### Core Domain

Don't find a clear usage now.

### Generic Subdomains

Don't find a clear usage now.

### Domain Vision Statement

Don't find a clear usage now.

### Highlighted Core

Don't find a clear usage now.

### Cohesive Mechanisms

Don't find a clear usage now.

### Segregated Core

Don't find a clear usage now.

### Abstract Core

Don't find a clear usage now.

### Evolving Order

Don't find a clear usage now.

### System Metaphor

Don't find a clear usage now.

### Responsibility Layers

Don't find a clear usage now.

### Knowledge Level

Don't find a clear usage now.

### Pluggable Component Framework

The "Holy Grail" I hope TiKV to have! Imaging what we can do if we can remove/change/extend the max supported isolation level, the transaction model, the consensus algorithm, different kinds of coprocessor, the local storage engine freely with just config change!

But this is just a hope, it is impossible to implement such a great work with acceptable development time, and it is hard to keep the performance, it is also not very useful for our customers.

But for some component it is possible and valuable, I think a pluggable copr layer is WIP now.

## Summary

I have viewed all patterns in DDD now, I think there're something I metioned above for several times, which means we do can put some efforts on:

- Collect some "dictionary" or wiki for the concepts (code structures, config items or even slang we use only during discussion) in TiKV, which can make us find the meaning of a word in our **Ubiquitous Language**.
- Build an all-in-one **model** which cover all aspects in TiKV, at least the txn system, which can help us to understand the code, diagnosising problems and finding possible optimizing.
- Make the hidden **specification** in our code clear, by using **assertions**, unit tests and so on.
- And I really think the Domain Specific Language thing is useful. But it could be some how as hard as creating a complete axiom system. I'll consider it carefully later.

[^1]: Except me, after some work on database and distrubuted system, I think I know nothing about database or distrubuted system 😭……

[^2]: Some database terminologies have ambiguity too, for example, consistency has at least 4 different meanings, see part Ⅱ, chapter 7 in the DDIA book. And in percolator, commit can mean commit of the transaction (prewrite and commit stage) or just the "commit stage". Naming is really hard!

[^3]: I used to set primary key for all `insert`s of mutations, but we should not set it for `Cache` variant, when I run a read-only transaction, this assert helped me to found this bug

[^4]: I'm always tring to build a (visible) model which cover all aspects in TiKV, at least the txn system. Like [the raft site](https://raft.github.io) does. Which is not only good for understanding the code, but also helpful when diagnosising problems and finding possible optimizing. Currently I have [txn-lab](https://github.com/longfangsong/txn-lab), and finally I think this idea, and the data-driven test idea drive me to think about the benefits of a Domain Specific Language on transaction.

[^5]: TiKV is really upstream in our development process! Maybe the only component which is in upstream of TiKV is rocksdb.

