# 说明

基于gost v2建立echo项目的tcp transparent proxy，主要解决如下问题：

- 在送往glx vpp时，仍能够保留user ts ip这个全网分配的地址，方便实现per user/ per node控制
- 确保tcp copy使用到内核的splice系统调用，以便减少proxy将用户数据从内核和用户态相互拷贝的开销（特别需要考虑跨越ns时能否生效，可以通过syscall确认）
  https://github.com/golang/go/issues/10948

# 关于tcp splice

## gost v3验证

通过strace可以看到大量的splice调用，说明上面issue中合入到go主线的patch已经生效了。理论上v2应该可以直接生效，待开发后验证。

## (TODO) gost v2修改后验证

目前看server.go:copyBuffer调用的CopyBuffer接口，因为两端都是tcp，可以自动适配splice调用，且不需要用到分配的暂存buffer。

故仅需确认即可。
