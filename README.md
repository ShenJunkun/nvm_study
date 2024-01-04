# 资料目录
## NVM学习相关网站和资料
- [pmem.io/Persistent Memory Development Kit](https://pmem.io/pmdk/)
- https://docs.pmem.io/ndctl-user-guide/v/master/getting-started-guide
- [intel pmem资料](https://www.intel.com/content/www/us/en/developer/topic-technology/persistent-memory/overview.html)
- [Google pmem论坛](https://groups.google.com/g/pmem)
- https://nvdimm.wiki.kernel.org/
- https://zhuanlan.zhihu.com/p/391107604
- https://polardb-x-tianchi-race-2022.oss-cn-hangzhou.aliyuncs.com/documents/%E6%8C%81%E4%B9%85%E5%86%85%E5%AD%98%E5%BF%AB%E9%80%9F%E7%BC%96%E7%A8%8B%E6%89%8B%E5%86%8C.pdf
- [pmem笔记](https://zhuanlan.zhihu.com/p/598568392)
## Code
- https://github.com/pmem/pmdk/tree/master/src/examples
- [pmem hackathon](https://github.com/orgs/pmemhackathon/repositories?type=all)
- https://github.com/pmem
- https://github.com/pmemhackathon/hackathon


## python nvm
- https://github.com/hpcaitech/TensorNVMe
- https://github.com/pmem/pynvm
- https://github.com/IBM/pymm


# 笔记目录
## pmem相关指令

### CLWB
`CLWB`（Cache Line Write Back）是一条指令。用于PMEM设备的数据持久化，即把CPU cache中数据持久化PMEM的介质中。该指令用于只支持ADR模型的设备，对于支持eADR的设备，可以不使用eADR指令从而达到更好的性能。

与`CLFLUSH`和`CLFLUSHOPT`指令不同，`CLWB`在把脏数据写到pmem的介质中后，CPU cache line中的数据仍然有效。

和`CLFLUSHOPT`相似，`CLWB`没有包含隐含的fence, 所以使用该指令后，一般会使用`SFENCE`指令。

### CLUSH
`CLUSH`（Cache Line Flush）把CPU中cache中的数据刷新到介质中，`CLUSH`指令包含`FENCE`指令，所以如果需要FLUSH多条cache line，`CLUSH`和`FENCE`指令是穿行的。

`CLUSH`指令后，对应的cache line失效。

### CLFLUSHOPT
`CLFLUSHOPT`是对`CLUSH`指令的优化版本，实现了一定程序上的并行。

### NT Store
NT Store(Non-Temporal Store): 对于持久内存，NT存储非常有用，因为它们绕过CPU缓存，因此存储变得持久，而不需要像CLWB这样的额外刷新指令。值得注意的是，英特尔的NT存储是写合并，而不是像普通内存存储那样回写。这意味着，在发出像SFENCE这样的fence指令之前，存储对其他线程不一定是全局可见的。
像PMDK这样的库大量使用NT存储。例如，libpmem库使用启发式方法来确定使用哪种方法更好地复制内存范围

(以上内容参考:https://pmem.io/glossary/#clflushopt)

