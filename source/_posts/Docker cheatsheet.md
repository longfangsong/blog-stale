---
title: Docker cheatsheet
date: 2018-06-02 00:00:00
tags: [cheatsheet, Docker]
mathjax: true
comments: true
summary: Docker常用命令cheatsheet
---
<p>（我lfs就是饿死，死外边，也不会背一句命令！！！）</p>
    <h2 id="查看所有镜像"><a class="headerlink" href="#查看所有镜像" title="查看所有镜像"></a>查看所有镜像</h2><figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker images</span><br/></pre></td></tr></table></figure>
<h2 id="删除镜像"><a class="headerlink" href="#删除镜像" title="删除镜像"></a>删除镜像</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker image rm 镜像ID</span><br/></pre></td></tr></table></figure>
<h2 id="用当前目录下Dockerfile构建容器"><a class="headerlink" href="#用当前目录下Dockerfile构建容器" title="用当前目录下Dockerfile构建容器"></a>用当前目录下Dockerfile构建容器</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker build .</span><br/></pre></td></tr></table></figure>
<h2 id="用镜像启动容器"><a class="headerlink" href="#用镜像启动容器" title="用镜像启动容器"></a>用镜像启动容器</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker run 镜像名</span><br/></pre></td></tr></table></figure>
<h3 id="在镜像中运行shell"><a class="headerlink" href="#在镜像中运行shell" title="在镜像中运行shell"></a>在镜像中运行shell</h3><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker run -it 镜像名</span><br/></pre></td></tr></table></figure>
<h2 id="查看在运行的容器"><a class="headerlink" href="#查看在运行的容器" title="查看在运行的容器"></a>查看在运行的容器</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker ps</span><br/></pre></td></tr></table></figure>
<h2 id="关闭容器"><a class="headerlink" href="#关闭容器" title="关闭容器"></a>关闭容器</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker kill 容器ID</span><br/></pre></td></tr></table></figure>
<h3 id="全部关闭"><a class="headerlink" href="#全部关闭" title="全部关闭"></a>全部关闭</h3><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker kill $(docker ps -q)</span><br/></pre></td></tr></table></figure>
<h2 id="删除容器"><a class="headerlink" href="#删除容器" title="删除容器"></a>删除容器</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker rm 容器ID</span><br/></pre></td></tr></table></figure>
<h3 id="删除所有"><a class="headerlink" href="#删除所有" title="删除所有"></a>删除所有</h3><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker rm $(docker ps -a -q)</span><br/></pre></td></tr></table></figure>
<h2 id="将容器烧录成镜像"><a class="headerlink" href="#将容器烧录成镜像" title="将容器烧录成镜像"></a>将容器烧录成镜像</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker commit -m “commit message” -a “author” 容器ID 用户名/镜像名:镜像版本</span><br/></pre></td></tr></table></figure>
<h2 id="为镜像打tag"><a class="headerlink" href="#为镜像打tag" title="为镜像打tag"></a>为镜像打tag</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker tag 镜像ID 用户名/镜像名:镜像版本</span><br/></pre></td></tr></table></figure>
<h2 id="Docker-Hub"><a class="headerlink" href="#Docker-Hub" title="Docker Hub"></a>Docker Hub</h2><h3 id="登录"><a class="headerlink" href="#登录" title="登录"></a>登录</h3><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker login</span><br/></pre></td></tr></table></figure>
<h3 id="拉取镜像"><a class="headerlink" href="#拉取镜像" title="拉取镜像"></a>拉取镜像</h3><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker pull 镜像名</span><br/></pre></td></tr></table></figure>
<h3 id="上传镜像"><a class="headerlink" href="#上传镜像" title="上传镜像"></a>上传镜像</h3><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">docker push 镜像名（要求镜像名格式为 用户名/镜像名）</span><br/></pre></td></tr></table></figure>

