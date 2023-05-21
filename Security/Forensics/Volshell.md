---
title: Volshell
author: i4
date: M12-23-2022
---

## Start

* Windows: `-w`
* Linux: `-l`
* Mac: `-m`

```
volshell.py -f {dump} [-w,-m,-l]
```

### Process

- Pointers do not need to be dereferenced.

```
(layer_name) >>> i = ps() # ps() returns a list of running processes (_EPROCESS struct)
(layer_name) >>> dt(i[0])
symbol_table_name1!_EPROCESS (608 bytes)
   0x0 :   Pcb                              symbol_table_name1!_KPROCESS                           0x823c89c8
  0x6c :   ProcessLock                      symbol_table_name1!_EX_PUSH_LOCK                       0x823c8a34
  0x70 :   CreateTime                       symbol_table_name1!_LARGE_INTEGER                      0x823c8a38
  0x78 :   ExitTime                         symbol_table_name1!_LARGE_INTEGER                      0x823c8a40
  0x80 :   RundownProtect                   symbol_table_name1!_EX_RUNDOWN_REF                     0x823c8a48
  0x84 :   UniqueProcessId                  symbol_table_name1!pointer                             4
  0x88 :   ActiveProcessLinks               symbol_table_name1!_LIST_ENTRY                         0x823c8a50
  0x90 :   QuotaUsage                       symbol_table_name1!array                               ['0', '0', '0']
  0x9c :   QuotaPeak                        symbol_table_name1!array                               ['0', '0', '0']
  0xa8 :   CommitCharge                     symbol_table_name1!unsigned long                       7
  0xac :   PeakVirtualSize                  symbol_table_name1!unsigned long                       2719744
  0xb0 :   VirtualSize                      symbol_table_name1!unsigned long                       1916928
  0xb4 :   SessionProcessLinks              symbol_table_name1!_LIST_ENTRY                         0x823c8a7c
  0xbc :   DebugPort                        symbol_table_name1!pointer                             0
  0xc0 :   ExceptionPort                    symbol_table_name1!pointer                             0
  0xc4 :   ObjectTable                      symbol_table_name1!pointer                             3774876960
  0xc8 :   Token                            symbol_table_name1!_EX_FAST_REF                        0x823c8a90
  0xcc :   WorkingSetLock                   symbol_table_name1!_FAST_MUTEX                         0x823c8a94
  0xec :   WorkingSetPage                   symbol_table_name1!unsigned long                       0
  0xf0 :   AddressCreationLock              symbol_table_name1!_FAST_MUTEX                         0x823c8ab8
 0x110 :   HyperSpaceLock                   symbol_table_name1!unsigned long                       0
 0x114 :   ForkInProgress                   symbol_table_name1!pointer                             0
 0x118 :   HardwareTrigger                  symbol_table_name1!unsigned long                       0
 0x11c :   VadRoot                          symbol_table_name1!pointer                             2185154680
 0x120 :   VadHint                          symbol_table_name1!pointer                             2185154680
 0x124 :   CloneRoot                        symbol_table_name1!pointer                             0
 0x128 :   NumberOfPrivatePages             symbol_table_name1!unsigned long                       3
 0x12c :   NumberOfLockedPages              symbol_table_name1!unsigned long                       0
 0x130 :   Win32Process                     symbol_table_name1!pointer                             0
 0x134 :   Job                              symbol_table_name1!pointer                             0
 0x138 :   SectionObject                    symbol_table_name1!pointer                             0
 0x13c :   SectionBaseAddress               symbol_table_name1!pointer                             0
 0x140 :   QuotaBlock                       symbol_table_name1!pointer                             2153095680
 0x144 :   WorkingSetWatch                  symbol_table_name1!pointer                             0
 0x148 :   Win32WindowStation               symbol_table_name1!pointer                             0
 0x14c :   InheritedFromUniqueProcessId     symbol_table_name1!pointer                             0
 0x150 :   LdtInformation                   symbol_table_name1!pointer                             0
 0x154 :   VadFreeHint                      symbol_table_name1!pointer                             0
 0x158 :   VdmObjects                       symbol_table_name1!pointer                             0
 0x15c :   DeviceMap                        symbol_table_name1!pointer                             3774908352
 0x160 :   PhysicalVadList                  symbol_table_name1!_LIST_ENTRY                         0x823c8b28
 0x168 :   Filler                           symbol_table_name1!unsigned long long                  0
 0x168 :   PageDirectoryPte                 symbol_table_name1!_HARDWARE_PTE                       0x823c8b30
 0x170 :   Session                          symbol_table_name1!pointer                             0
 0x174 :   ImageFileName                    symbol_table_name1!array                               ['83', '121', '115', '116', '101', '109', '0', '0', '0', '0', '0', '0', '0', '0', '0', '0']
 0x184 :   JobLinks                         symbol_table_name1!_LIST_ENTRY                         0x823c8b4c
 0x18c :   LockedPagesList                  symbol_table_name1!pointer                             0
 0x190 :   ThreadListHead                   symbol_table_name1!_LIST_ENTRY                         0x823c8b58
 0x198 :   SecurityPort                     symbol_table_name1!pointer                             3783179352
 0x19c :   PaeTop                           symbol_table_name1!pointer                             0
 0x1a0 :   ActiveThreads                    symbol_table_name1!unsigned long                       53
 0x1a4 :   GrantedAccess                    symbol_table_name1!unsigned long                       2035711
 0x1a8 :   DefaultHardErrorProcessing       symbol_table_name1!unsigned long                       1
 0x1ac :   LastThreadExitStatus             symbol_table_name1!long                                0
 0x1b0 :   Peb                              symbol_table_name1!pointer                             0
 0x1b4 :   PrefetchTrace                    symbol_table_name1!_EX_FAST_REF                        0x823c8b7c
 0x1b8 :   ReadOperationCount               symbol_table_name1!_LARGE_INTEGER                      0x823c8b80
 0x1c0 :   WriteOperationCount              symbol_table_name1!_LARGE_INTEGER                      0x823c8b88
 0x1c8 :   OtherOperationCount              symbol_table_name1!_LARGE_INTEGER                      0x823c8b90
 0x1d0 :   ReadTransferCount                symbol_table_name1!_LARGE_INTEGER                      0x823c8b98
 0x1d8 :   WriteTransferCount               symbol_table_name1!_LARGE_INTEGER                      0x823c8ba0
 0x1e0 :   OtherTransferCount               symbol_table_name1!_LARGE_INTEGER                      0x823c8ba8
 0x1e8 :   CommitChargeLimit                symbol_table_name1!unsigned long                       0
 0x1ec :   CommitChargePeak                 symbol_table_name1!unsigned long                       461
 0x1f0 :   AweInfo                          symbol_table_name1!pointer                             0
 0x1f4 :   SeAuditProcessCreationInfo       symbol_table_name1!_SE_AUDIT_PROCESS_CREATION_INFO     0x823c8bbc
 0x1f8 :   Vm                               symbol_table_name1!_MMSUPPORT                          0x823c8bc0
 0x238 :   LastFaultCount                   symbol_table_name1!unsigned long                       0
 0x23c :   ModifiedPageCount                symbol_table_name1!unsigned long                       1634
 0x240 :   NumberOfVads                     symbol_table_name1!unsigned long                       4
 0x244 :   JobStatus                        symbol_table_name1!unsigned long                       0
 0x248 :   AddressSpaceInitialized          symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   BreakOnTermination               symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   CreateReported                   symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Flags                            symbol_table_name1!unsigned long                       262144
 0x248 :   ForkFailed                       symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   HasAddressSpace                  symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   HasPhysicalVad                   symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   InjectInpageErrors               symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   LaunchPrefetched                 symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   NoDebugInherit                   symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   OutswapEnabled                   symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Outswapped                       symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   OverrideAddressSpace             symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   ProcessDelete                    symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   ProcessExiting                   symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   ProcessInSession                 symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   SessionCreationUnderway          symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   SetTimerResolution               symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Unused                           symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Unused1                          symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Unused2                          symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Unused3                          symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Unused4                          symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   VdmAllowed                       symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   VmDeleted                        symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   VmTopDown                        symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   Wow64SplitPages                  symbol_table_name1!bitfield                            0x823c8c10
 0x248 :   WriteWatch                       symbol_table_name1!bitfield                            0x823c8c10
 0x24c :   ExitStatus                       symbol_table_name1!long                                259
 0x250 :   NextPageColor                    symbol_table_name1!unsigned short                      14651
 0x252 :   SubSystemMinorVersion            symbol_table_name1!unsigned char                       0
 0x252 :   SubSystemVersion                 symbol_table_name1!unsigned short                      0
 0x253 :   SubSystemMajorVersion            symbol_table_name1!unsigned char                       0
 0x254 :   PriorityClass                    symbol_table_name1!unsigned char                       2
 0x255 :   WorkingSetAcquiredUnsafe         symbol_table_name1!unsigned char                       0
 0x258 :   Cookie                           symbol_table_name1!unsigned long                       0
 ```

**Getting the ImageFileName**
```
"".join([chr(i) for i in proc.ImageFileName]).replace("\x00","")
```

### Plugins
```
(layer_name) >>> from volatility3.plugins.windows import pslist
(layer_name) >>> display_plugin_output(pslist.PsList)
Unable to validate the plugin requirements: ['plugins.Volshell.S8PFTQXIC7H2UWLGO36VDJNB4E0RZ9Y5.PsList.kernel.class', 'plugins.Volshell.S8PFTQXIC7H2UWLGO36VDJNB4E0RZ9Y5.PsList.kernel.layer_name', 'plugins.Volshell.S8PFTQXIC7H2UWLGO36VDJNB4E0RZ9Y5.PsList.kernel.symbol_table_name']
```
This error shows us that the requirements are not fullfilled and need to be loaded.

`get_requiremets()` shows us what the plugin expects us to input.
```
(layer_name) >>> pslist.PsList.get_requirements()
[<ModuleRequirement: kernel>, <BooleanRequirement: physical>, <ListRequirement: pid>, <BooleanRequirement: dump>]
(layer_name) >>> dpo(pslist.PsList, kernel=self.config['kernel']) # dpo() = display_plugin_output()

PID     PPID    ImageFileName   Offset(V)       Threads Handles SessionId       Wow64   CreateTime      ExitTime        File output

4       0       System  0x823c89c8      53      240     N/A     False   N/A     N/A     Disabled
368     4       smss.exe        0x822f1020      3       19      N/A     False   2012-07-22 02:42:31.000000      N/A     Disabled
584     368     csrss.exe       0x822a0598      9       326     0       False   2012-07-22 02:42:32.000000      N/A     Disabled
608     368     winlogon.exe    0x82298700      23      519     0       False   2012-07-22 02:42:32.000000      N/A     Disabled
652     608     services.exe    0x81e2ab28      16      243     0       False   2012-07-22 02:42:32.000000      N/A     Disabled
664     608     lsass.exe       0x81e2a3b8      24      330     0       False   2012-07-22 02:42:32.000000      N/A     Disabled
824     652     svchost.exe     0x82311360      20      194     0       False   2012-07-22 02:42:33.000000      N/A     Disabled
908     652     svchost.exe     0x81e29ab8      9       226     0       False   2012-07-22 02:42:33.000000      N/A     Disabled
1004    652     svchost.exe     0x823001d0      64      1118    0       False   2012-07-22 02:42:33.000000      N/A     Disabled
1056    652     svchost.exe     0x821dfda0      5       60      0       False   2012-07-22 02:42:33.000000      N/A     Disabled
1220    652     svchost.exe     0x82295650      15      197     0       False   2012-07-22 02:42:35.000000      N/A     Disabled
1484    1464    explorer.exe    0x821dea70      17      415     0       False   2012-07-22 02:42:36.000000      N/A     Disabled
1512    652     spoolsv.exe     0x81eb17b8      14      113     0       False   2012-07-22 02:42:36.000000      N/A     Disabled
1640    1484    reader_sl.exe   0x81e7bda0      5       39      0       False   2012-07-22 02:42:36.000000      N/A     Disabled
788     652     alg.exe         0x820e8da0      7       104     0       False   2012-07-22 02:43:01.000000      N/A     Disabled
1136    1004    wuauclt.exe     0x821fcda0      8       173     0       False   2012-07-22 02:43:46.000000      N/A     Disabled
1588    1004    wuauclt.exe     0x8205bda0      5       132     0       False   2012-07-22 02:44:01.000000      N/A     Disabled
```

