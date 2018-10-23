---
title: probe和module_init
tags:
  - 设备模型
categories: '内核'
comments: true
copyright: true
date: 2018-10-23 16:58:42
---


　　module_init(test_init)的作用是将test_init放在.initcall6.init段中。
　　内核启动时，在start_kernel中会创建一个叫kernel_init的内核线程，这个线程会调用到do_initcalls，do_initcalls函数依调用我们通过xxx_initcall注册的各种函数，优先级高的先执行。包括我们通过module_init注册的函数，在kernel启动的时候会被顺序执行。

　　一般module_init注册的函数test_init中会调到注册到总线的函数如：
platform_driver_register
跟踪该注册函数，会跟踪到bus总线匹配device和driver从而执行probe的过程。依次调用如下：

		platform_driver_register

		__platform_driver_register

		driver_register

		bus_add_driver

		driver_attach

		bus_for_each_dev  (有如下调用：fn(dev, data);指的就是__driver_attach)

		__driver_attach

		driver_match_device ，如果match上了，会继续向下走到driver_probe_device    match：return drv->bus->match ? drv->bus->match(dev, drv) : 1;走到总线的match函数；

		really_probe

		drv->probe   （回调）调用了driver的probe函数，同时将上述匹配到的device作为参数传给probe
另外，module_init是怎么调用的呢：

https://blog.csdn.net/u013216061/article/details/72511653

https://blog.csdn.net/richard_liujh/article/details/46758073
