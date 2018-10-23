---
title: request_threaded_irq
tags:
  - 中断
categories: '内核'
comments: true
copyright: true
date: 2018-10-23 16:14:48
---


函数原型：
		int request_threaded_irq(unsigned int irq, irq_handler_t handler,   irq_handler_t thread_fn, unsigned long irqflags, const char *devname, void *dev_id)



 | 输入参数 | 描述 |
|--------------|--------|
|   irq     |     中断号              |
|  handler 	|   中断处理函数           |
| thread_fn | 	要在内核线程中调用的函数 |
|  irqflags |	在驱动中是中断触发类型 |


输出参数：0表示成功执行，负数表示各种错误原因。

中断号：在驱动中，一般会由中断的gpio号得到，如 irq_num = gpio_to_irq(bdata->irq_gpio);

handler:中断处理函数，在驱动中一般这个参数是NULL，为NULL时使用默认的处理，这个相当于中断的上半段。

thread_fn：中断发生时，如果handler为NULL，就直接将thread_fn扔到内核线程中去执行。

irqflags：类似于IRQF_ONESHOT | IRQF_TRIGGER_LOW表示中断触发方式为低电平触发。


&emsp;&emsp;这个函数将中断线程化，中断将作为内核线程运行，可被赋予不同的实时优先级。在负载较高时，中断线程可以被挂起，以避免某些更高优先级的实时任务得不到及时响应。
