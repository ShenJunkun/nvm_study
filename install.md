## 笔记

### ndctl使用
- ndctl list
```bash
sjk@HM1:~$ ndctl list -DRNi
{
  "dimms":[
    {
      "dev":"nmem1",
      "id":"8089-a2-1941-0000062a",
      "handle":16,
      "phys_id":18,
      "security":"disabled"
    },
    {
      "dev":"nmem3",
      "id":"8089-a2-1941-00000673",
      "handle":272,
      "phys_id":22,
      "security":"disabled"
    },
    {
      "dev":"nmem0",
      "id":"8089-a2-1941-00000119",
      "handle":1,
      "phys_id":17,
      "security":"disabled"
    },
    {
      "dev":"nmem2",
      "id":"8089-a2-1941-0000020f",
      "handle":257,
      "phys_id":21,
      "security":"disabled"
    }
  ],
  "regions":[
    {
      "dev":"region0",
      "size":541165879296,
      "available_size":0,
      "max_available_extent":0,
      "type":"pmem",
      "iset_id":1008740143054817416,
      "mappings":[
        {
          "dimm":"nmem3",
          "offset":268435456,
          "length":135291469824,
          "position":3
        },
        {
          "dimm":"nmem2",
          "offset":268435456,
          "length":135291469824,
          "position":1
        },
        {
          "dimm":"nmem1",
          "offset":268435456,
          "length":135291469824,
          "position":2
        },
        {
          "dimm":"nmem0",
          "offset":268435456,
          "length":135291469824,
          "position":0
        }
      ],
      "persistence_domain":"memory_controller",
      "namespaces":[
        {
          "dev":"namespace0.1",
          "mode":"raw",
          "size":0,
          "uuid":"00000000-0000-0000-0000-000000000000",
          "sector_size":512,
          "state":"disabled"
        },
        {
          "dev":"namespace0.0",
          "mode":"fsdax",
          "map":"dev",
          "size":532708065280,
          "uuid":"69bcea8f-09f2-4adb-ac2b-0877346e2f54",
          "sector_size":512,
          "align":2097152,
          "blockdev":"pmem0"
        }
      ]
    }
  ]
}
```


- ndctl list --regions
```bash
sjk@HM1:~$ ndctl list --regions
[
  {
    "dev":"region0",
    "size":541165879296,
    "available_size":0,
    "max_available_extent":0,
    "type":"pmem",
    "iset_id":1008740143054817416,
    "persistence_domain":"memory_controller"
  }
]
```

- ndctl list -N
```bash
sjk@HM1:~$ ndctl list -N

[
  {
    "dev":"namespace0.0",
    "mode":"fsdax",
    "map":"dev",
    "size":532708065280,
    "uuid":"69bcea8f-09f2-4adb-ac2b-0877346e2f54",
    "sector_size":512,
    "align":2097152,
    "blockdev":"pmem0"
  }
]
```






### ipmctl使用


- 查看regions
```bash
ipmctl show -region

 SocketID | ISetID             | PersistentMemoryType | Capacity    | FreeCapacity | HealthState
=================================================================================================
 0x0000   | 0x0dffc3d0c9758888 | AppDirect            | 504.000 GiB | 0.000 GiB    | Healthy
```

- 查看内存资源情况[link](https://docs.pmem.io/ipmctl-user-guide/module-discovery/show-memory-resources)
```bash
sjk@HM1:~$ sudo ipmctl show -memoryresources

 MemoryType   | DDR         | DCPMM       | Total
========================================================
 Volatile     | 128.000 GiB | 0.000 GiB   | 128.000 GiB
 AppDirect    | -           | 504.000 GiB | 504.000 GiB
 Cache        | 0.000 GiB   | -           | 0.000 GiB
 Inaccessible | -           | 1.689 GiB   | 1.689 GiB
 Physical     | 128.000 GiB | 505.689 GiB | 633.689 GiB
```
- 查看dimm分布[show device](https://docs.pmem.io/ipmctl-user-guide/module-discovery/show-device)
```bash
sjk@HM1:~$ sudo ipmctl show -dimm

 DimmID | Capacity    | LockState | HealthState | FWVersion
===============================================================
 0x0001 | 126.422 GiB | Disabled  | Healthy     | 01.02.00.5355
 0x0010 | 126.422 GiB | Disabled  | Healthy     | 01.02.00.5355
 0x0101 | 126.422 GiB | Disabled  | Healthy     | 01.02.00.5355
 0x0110 | 126.422 GiB | Disabled  | Healthy     | 01.02.00.5355

```

- System Capabilities:返回值什么意思请参考[link](https://docs.pmem.io/ipmctl-user-guide/module-discovery/show-system-capabilities)
```bash
sjk@HM1:~$ sudo ipmctl show -system -capabilities

PlatformConfigSupported=1
Alignment=1.000 GiB
AllowedVolatileMode=Memory Mode
CurrentVolatileMode=1LM
AllowedAppDirectMode=App Direct
```

- show socket [link](https://docs.pmem.io/ipmctl-user-guide/module-discovery/show-socket)
```bash
sjk@HM1:~$ sudo ipmctl show -socket
[sudo] password for sjk:
 SocketID | MappedMemoryLimit | TotalMappedMemory
==================================================
 0x0000   | 1024.000 GiB      | 632.000 GiB
```


- show topology [link](https://docs.pmem.io/ipmctl-user-guide/module-discovery/show-topology)

```bash
sjk@HM1:~$ sudo ipmctl show -topology
 DimmID | MemoryType                  | Capacity    | PhysicalID| DeviceLocator
================================================================================
 0x0001 | Logical Non-Volatile Device | 126.375 GiB | 0x0011    | P1-DIMMA2
 0x0010 | Logical Non-Volatile Device | 126.375 GiB | 0x0012    | P1-DIMMB1
 0x0101 | Logical Non-Volatile Device | 126.375 GiB | 0x0015    | P1-DIMMD2
 0x0110 | Logical Non-Volatile Device | 126.375 GiB | 0x0016    | P1-DIMME1
 N/A    | DDR4                        | 32.000 GiB  | 0x0010    | P1-DIMMA1
 N/A    | DDR4                        | 32.000 GiB  | 0x0013    | P1-DIMMC1
 N/A    | DDR4                        | 32.000 GiB  | 0x0014    | P1-DIMMD1
 N/A    | DDR4                        | 32.000 GiB  | 0x0017    | P1-DIMMF1
```








## Reference
- https://blog.csdn.net/Z_Stand/article/details/120614595