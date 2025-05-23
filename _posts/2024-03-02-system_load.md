---
title: 系统负载
date: 2024-03-02 20:30:00 +0800
categories: [blog, 技术]
tags: [load, linux]     # TAG names should always be lowercase
---


注：这是一篇老文章，写于2018年6月6日

<div class="ez-toc-v2_0_66_1 counter-hierarchy ez-toc-counter ez-toc-grey ez-toc-container-direction" id="ez-toc-container"><div class="ez-toc-title-container">目录

<span class="ez-toc-title-toggle">[<span class="ez-toc-js-icon-con"><span class=""><span class="eztoc-hide" style="display:none;">Toggle</span><span class="ez-toc-icon-toggle-span"><svg class="list-377408" fill="none" height="20px" style="fill: #999;color:#999" viewbox="0 0 24 24" width="20px" xmlns="http://www.w3.org/2000/svg"><path d="M6 6H4v2h2V6zm14 0H8v2h12V6zM4 11h2v2H4v-2zm16 0H8v2h12v-2zM4 16h2v2H4v-2zm16 0H8v2h12v-2z" fill="currentColor"></path></svg><svg baseprofile="tiny" class="arrow-unsorted-368013" height="10px" style="fill: #999;color:#999" version="1.2" viewbox="0 0 24 24" width="10px" xmlns="http://www.w3.org/2000/svg"><path d="M18.2 9.3l-6.2-6.3-6.2 6.3c-.2.2-.3.4-.3.7s.1.5.3.7c.2.2.4.3.7.3h11c.3 0 .5-.1.7-.3.2-.2.3-.5.3-.7s-.1-.5-.3-.7zM5.8 14.7l6.2 6.3 6.2-6.3c.2-.2.3-.5.3-.7s-.1-.5-.3-.7c-.2-.2-.4-.3-.7-.3h-11c-.3 0-.5.1-.7.3-.2.2-.3.5-.3.7s.1.5.3.7z"></path></svg></span></span></span>](#)</span></div><nav>- [如何确定生产环境中发生的瓶颈](http://thinknotes.cn/2024/03/02/system_load/#%E5%A6%82%E4%BD%95%E7%A1%AE%E5%AE%9A%E7%94%9F%E4%BA%A7%E7%8E%AF%E5%A2%83%E4%B8%AD%E5%8F%91%E7%94%9F%E7%9A%84%E7%93%B6%E9%A2%88 "如何确定生产环境中发生的瓶颈")
- [观察load average](http://thinknotes.cn/2024/03/02/system_load/#%E8%A7%82%E5%AF%9Fload_average "观察load average")
- [观察CPU、I/O是否存在瓶颈](http://thinknotes.cn/2024/03/02/system_load/#%E8%A7%82%E5%AF%9FCPU%E3%80%81IO%E6%98%AF%E5%90%A6%E5%AD%98%E5%9C%A8%E7%93%B6%E9%A2%88 "观察CPU、I/O是否存在瓶颈")
  - [当CPU负载较高时](http://thinknotes.cn/2024/03/02/system_load/#%E5%BD%93CPU%E8%B4%9F%E8%BD%BD%E8%BE%83%E9%AB%98%E6%97%B6 "当CPU负载较高时")
  - [当I/O负载较高时](http://thinknotes.cn/2024/03/02/system_load/#%E5%BD%93IO%E8%B4%9F%E8%BD%BD%E8%BE%83%E9%AB%98%E6%97%B6 "当I/O负载较高时")

</nav></div>## <span class="ez-toc-section" id="%E5%A6%82%E4%BD%95%E7%A1%AE%E5%AE%9A%E7%94%9F%E4%BA%A7%E7%8E%AF%E5%A2%83%E4%B8%AD%E5%8F%91%E7%94%9F%E7%9A%84%E7%93%B6%E9%A2%88"></span>如何确定生产环境中发生的瓶颈<span class="ez-toc-section-end"></span>

1. 观察load average（平均负载）
2. 观察CPU、I/O是否存在瓶颈

## <span class="ez-toc-section" id="%E8%A7%82%E5%AF%9Fload_average"></span>观察load average<span class="ez-toc-section-end"></span>

1. 作为重要的负载评估指标，需要通过top和uptime等命令查看load average。load average 可以衡量整个系统的负载情况。虽说仅通过load average还不能判断出系统出现瓶颈的原因在哪里，但可从load average着手进行系统瓶颈的调查。
2. 有时在load average较低时，系统的吞吐量有可能还是达不到要求。此时就需要查看软件的配置是否不合理，网络、远程主机方面是否存在问题等。

## <span class="ez-toc-section" id="%E8%A7%82%E5%AF%9FCPU%E3%80%81IO%E6%98%AF%E5%90%A6%E5%AD%98%E5%9C%A8%E7%93%B6%E9%A2%88"></span>观察CPU、I/O是否存在瓶颈<span class="ez-toc-section-end"></span>

1. 当load average较高时，需要确认CPU和I/O哪个存在问题。通过使用sar或vmstat等工具，可以确认一段时间内的CPU使用率和I/O等待的情况。确认后即可进入下一阶段。  
  ### <span class="ez-toc-section" id="%E5%BD%93CPU%E8%B4%9F%E8%BD%BD%E8%BE%83%E9%AB%98%E6%97%B6"></span>当CPU负载较高时<span class="ez-toc-section-end"></span>
  
  CPU负载较高时，可以按照下面的流程来排查问题所在：
  
  
  - 通过top和sar等工具来判断是用户程序（Web应用程序）的原因，还是系统程序（操作系统）的原因
  - 一边通过ps命令来查看进程状态以及CPU使用时间等数据，一边锁定出现问题的进程
  - 如果要进一步了解所锁定的进程的具体行为，可以使用strace命令进行跟踪（Trace），也可以使用oprofile工具在计数器中断处理入口处建立监测点，以缩小监控瓶颈的范围

造成CPU负载过重的原因通常是：

- 磁盘及内存占用尚未超标的情况下（即I/O不存在瓶颈），系统的load average指标依旧很高
- 程序的逻辑部分负载超过CPU的承受能力且不受控制

如果是前者且系统吞吐性能也出现了问题，可以尝试通过增设服务器、改善程序逻辑和算法来解决问题。后者的情况下要注意程序没有预料到的流程，以避免出现程序失控的状况。

### <span class="ez-toc-section" id="%E5%BD%93IO%E8%B4%9F%E8%BD%BD%E8%BE%83%E9%AB%98%E6%97%B6"></span>当I/O负载较高时<span class="ez-toc-section-end"></span>

I/O负载较高基本上有两种情况：

1. 一种是来自程序本身的磁盘读写较为频繁，造成该程序输入输出负载比较高；
2. 另一种是因使用了Linux swap交换区而造成磁盘的频繁访问。通过使用sar及vmstat命令确认SWAP交换区的状况，即可分辨出到底是哪一种情况。
3. 如果确认后发现发生了频繁访问SWAP交换区的情况，可通过以下几个步骤来查出问题所在： 
  - 通过ps命令确认是否有特定进程消耗了大量内存
  - 由于程序不受控制而造成内存占用过大时，可以修改相应的程序逻辑来解决问题
  - 系统存在不足时增加内存。无法增加内存的情况下需要考虑进行负载分流
4. 如果没有频繁访问SWAP交换区，且磁盘上依然频繁发生输入输出，该情况下可以认为是缓存所需的内存不足。可以根据服务器装在的磁盘容量以及可以扩充的内存容量，分以下几种情况来考虑： 
  - 通过扩展内存缓存以实现扩展缓存容量时，优先扩充内存容量
  - 无法通过扩充内存容量来解决问题时，需要考虑数据分流和增设缓存服务器等方案。当然也可以考虑改善程序以减轻I/O频率