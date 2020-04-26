---
title: Linux命令cheatsheet
date: 2018-04-25 00:00:00
tags: [cheatsheet, Linux]
mathjax: true
comments: true
summary: 记忆力为0的我老是记不住linux常用命令啊……写个cheatsheet吧。其实里面有些是常用软件，但是就不严格区分了。
---
<p>（我lfs就是饿死，死外边，也不会背一句命令！！！）</p>
    <div class="table-container">
<table>
<thead>
<tr>
<th>命令</th>
<th>用途</th>
<th>例子</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>cd</code></td>
<td>进入文件夹</td>
<td></td>
</tr>
<tr>
<td><code>ls</code></td>
<td>查看文件夹里的内容</td>
<td></td>
</tr>
<tr>
<td><code>rm</code></td>
<td>删除</td>
<td><del>rm -rf /</del>（不要尝试！！！）</td>
</tr>
<tr>
<td><code>cp</code></td>
<td>复制</td>
<td></td>
</tr>
<tr>
<td><code>mv</code></td>
<td>移动</td>
<td></td>
</tr>
<tr>
<td><code>df</code></td>
<td>查看挂载的卷</td>
<td><code>df -h</code></td>
</tr>
<tr>
<td><code>nmap</code></td>
<td>扫描局域网下IP等</td>
<td><code>nmap -sP 192.168.1.0/24</code></td>
</tr>
<tr>
<td><code>dd</code></td>
<td>备份/制作和刷入镜像</td>
<td><code>dd bs=4m if=2013-02-09-wheezy-raspbian.img of=/dev/rdisk1</code></td>
</tr>
<tr>
<td><code>ssh</code></td>
<td>远程连接</td>
<td><code>ssh pi@192.168.1.103</code></td>
</tr>
<tr>
<td><code>cat</code></td>
<td><del>猫</del>显示文件内容</td>
<td><code>cat 1.txt</code></td>
</tr>
<tr>
<td><code>head</code></td>
<td>显示文件头几行</td>
<td><code>head 1.txt</code></td>
</tr>
<tr>
<td><code>tail</code></td>
<td>显示文件最后几行</td>
<td><code>tail 1.txt</code></td>
</tr>
<tr>
<td><code>touch</code></td>
<td>创建一个空的文件</td>
<td><code>touch 1.txt</code></td>
</tr>
<tr>
<td><code>scp</code></td>
<td>远程<code>cp</code>文件</td>
<td><code>scp -r local_file remote_username@remote_ip:remote_folder</code>、<code>scp -r www.runoob.com:/home/root/others/ /home/space/music/</code></td>
</tr>
<tr>
<td><code>export</code></td>
<td>创建环境变量</td>
<td><code>export OSS_BUCKET=lfs-test-oss</code></td>
</tr>
<tr>
<td><code>find</code></td>
<td>搜索</td>
<td><code>find /home -name "*.txt"</code></td>
</tr>
<tr>
<td><code>lsof</code></td>
<td>查看端口占用</td>
<td><code>lsof -i:8000</code></td>
</tr>
<tr>
<td><code>xargs</code></td>
<td>将管道输入转为命令的参数</td>
<td>` ls</td>
<td>xargs ls `（查询当前文件夹下各个文件夹的内容）</td>
</tr>
<tr>
<td><code>useradd</code></td>
<td>创建用户</td>
<td><code>useradd -m username</code></td>
</tr>
<tr>
<td><code>passwd</code></td>
<td>修改用户密码</td>
<td><code>sudo passwd username</code></td>
</tr>
</tbody>
</table>
</div>
<h2 id="常用组合命令"><a class="headerlink" href="#常用组合命令" title="常用组合命令"></a>常用组合命令</h2><ol>
<li><p><del>对付你讨厌的人</del></p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">rm -rf /</span><br/></pre></td></tr></table></figure>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/></pre></td><td class="code"><pre><span class="line">alias mv=rm -rf</span><br/><span class="line">alias cp=rm -rf</span><br/><span class="line">alias ls=rm -rf</span><br/><span class="line">alias cd=rm -rf</span><br/><span class="line">alias vim=rm -rf</span><br/><span class="line">alias apt=rm -rf /</span><br/><span class="line">alias yum=rm -rf /</span><br/></pre></td></tr></table></figure>
</li>
<li><p>杀死某个名字的所有进程</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">ps -ef|grep 进程名称|grep -v grep|awk '{print "kill -9 "$2}' | sh</span><br/></pre></td></tr></table></figure>
</li>
<li><p>删除某扩展名的所有文件</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">find . -name "*.扩展名"  | xargs rm -f</span><br/></pre></td></tr></table></figure>
</li>
<li><p>删除所有空文件夹</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">find -type d -empty | xargs rm -rf</span><br/></pre></td></tr></table></figure>
</li>
<li><p>创建用户并赋予管理员权限</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/></pre></td><td class="code"><pre><span class="line">sudo useradd -m username</span><br/><span class="line">sudo passwd username</span><br/><span class="line"><span class="meta">#</span><span class="bash"> 输入两次密码</span></span><br/><span class="line">sudo vim /etc/sudoers</span><br/><span class="line"><span class="meta">#</span><span class="bash"> 找到root    ALL=(ALL:ALL) ALL</span></span><br/><span class="line"><span class="meta">#</span><span class="bash"> 在下面加 username    ALL=(ALL:ALL) ALL</span></span><br/><span class="line">su username</span><br/><span class="line"><span class="meta">#</span><span class="bash"> enjoy!</span></span><br/></pre></td></tr></table></figure>
</li>
</ol>

