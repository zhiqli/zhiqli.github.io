---
title: 火焰图分析go程序性能
categories:
  - golang 
  tags:
  - golang 
---

> 最近线上有个服务非常吃内存，为了定位问题，生成了火焰图，以下是笔记

## pprof
首先需要通过pprof来将程序的性能采用获取回来，这个非常简单，直接采用`net/http/pprof`这个package，监听一个端口即可。
```
import _ "net/http/pprof"

go func() {
    http.ListenAndServe("0.0.0.0:9090", nil)
}()
```

## go tool
通过go tool pprof可以看到采样信息，如下图所示，采样完成，输入top 10，就可以看到消耗时间最到的10个函数。
![](%E7%81%AB%E7%84%B0%E5%9B%BE%E5%88%86%E6%9E%90%E6%80%A7%E8%83%BD/EB3F6809-D518-441B-ADBB-B24E429E9BFC.png)
如果想要看得更直观一点，输入web，会生成一个拓扑图。图中每一个方块是一个函数调用栈，箭头上的数字是上一个函数调用下一个函数累计消耗cpu时间。
![](%E7%81%AB%E7%84%B0%E5%9B%BE%E5%88%86%E6%9E%90%E6%80%A7%E8%83%BD/BF9939C2-ABE3-473A-84C1-62849CB856C8.png)
## 火焰图
使用go-torch还能看到更直观的火焰图，
#### FlameGraph
首先需要安装FlameGraph，直接git clone把代码包下载下来
```
git clone https://github.com/brendangregg/FlameGraph.git
```
把`flamegraph.pl`拷贝到PATH目录
`cp flamegraph.pl /usr/local/bin`
#### go-torch
直接使用go get安装`go get -v github.com/uber/go-torch`
#### 生成火焰图
通过`go-torch -u http://localhost:9090 -t 30`命令生成采样报告
![](%E7%81%AB%E7%84%B0%E5%9B%BE%E5%88%86%E6%9E%90%E6%80%A7%E8%83%BD/39734D7B-F39F-4D46-A2A1-01928895CB95.png)
在浏览器打开torch.svg
![](%E7%81%AB%E7%84%B0%E5%9B%BE%E5%88%86%E6%9E%90%E6%80%A7%E8%83%BD/8F96215F-9534-4A73-96AB-455547916287.png)

## 参考
本文是查性能问题时学习生成火焰图的笔记，参考了以下文章，更具体详细的内容可以参考以下文章。
[Go代码调优利器-火焰图 - domac的菜园子](https://lihaoquan.me/2017/1/1/Profiling-and-Optimizing-Go-using-go-torch.html)
[Golang FlameGraph（火焰图） - 城寒的个人空间 - 开源中国](https://my.oschina.net/xcxt/blog/1559487)
[golang 内存分析/动态追踪 — 源代码](https://lrita.github.io/2017/05/26/golang-memory-pprof/#go-torch)
#golang
