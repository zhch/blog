## NUMA

多核被划分为多个Node，每个Node有一片自己相对独立的内存空间。Node内的CPU访问自己的内存性能较高，访问其他Node的内存性能低。

查看NUMA的架构：

```shell
$sudo numactl --hardware
available: 1 nodes (0)
node 0 cpus: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95
node 0 size: 523949 MB
node 0 free: 426980 MB
node distances:
node   0 
  0:  10 
```

查看各个Node的内存使用情况如下，几个重要指标：

- nr_active_anon/nr_inactive_anon：活跃和非活跃匿名页数
- nr_active_file/nr_inactive_file：活跃和非活跃的文件页数
- 

```
$cat /proc/zoneinfo 
Node 0, zone      DMA
  pages free     3971
        min      3
        low      3
        high     4
        scanned  0
        spanned  4095
        present  3992
        managed  3971
    nr_free_pages 3971
    nr_alloc_batch 1
    nr_inactive_anon 0
    nr_active_anon 0
    nr_inactive_file 0
    nr_active_file 0
    nr_unevictable 0
    nr_mlock     0
    nr_anon_pages 0
    nr_mapped    0
    nr_file_pages 0
    nr_dirty     0
    nr_writeback 0
    nr_slab_reclaimable 0
    nr_slab_unreclaimable 0
    nr_page_table_pages 0
    nr_kernel_stack 0
    nr_unstable  0
    nr_bounce    0
    nr_vmscan_write 0
    nr_vmscan_immediate_reclaim 0
    nr_writeback_temp 0
    nr_isolated_anon 0
    nr_isolated_file 0
    nr_shmem     0
    nr_dirtied   0
    nr_written   0
    numa_hit     0
    numa_miss    0
    numa_foreign 0
    numa_interleave 0
    numa_local   0
    numa_other   0
    workingset_refault 0
    workingset_activate 0
    workingset_nodereclaim 0
    nr_anon_transparent_hugepages 0
    nr_free_cma  0
        protection: (0, 1424, 515448, 515448)
  pagesets
    cpu: 0
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 1
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 2
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 3
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 4
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 5
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 6
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 7
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 8
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 9
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 10
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 11
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 12
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 13
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 14
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 15
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 16
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 17
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 18
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 19
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 20
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 21
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 22
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 23
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 24
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 25
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 26
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 27
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 28
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 29
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 30
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 31
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 32
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 33
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 34
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 35
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 36
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 37
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 38
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 39
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 40
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 41
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 42
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 43
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 44
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 45
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 46
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 47
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 48
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 49
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 50
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 51
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 52
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 53
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 54
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 55
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 56
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 57
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 58
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 59
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 60
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 61
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 62
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 63
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 64
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 65
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 66
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 67
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 68
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 69
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 70
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 71
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 72
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 73
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 74
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 75
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 76
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 77
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 78
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 79
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 80
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 81
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 82
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 83
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 84
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 85
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 86
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 87
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 88
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 89
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 90
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 91
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 92
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 93
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 94
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
    cpu: 95
              count: 0
              high:  0
              batch: 1
  vm stats threshold: 14
  all_unreclaimable: 0
  start_pfn:         1
  inactive_ratio:    1
Node 0, zone    DMA32
  pages free     364270
        min      276
        low      345
        high     414
        scanned  0
        spanned  1044480
        present  433665
        managed  364636
    nr_free_pages 364270
    nr_alloc_batch 69
    nr_inactive_anon 0
    nr_active_anon 0
    nr_inactive_file 0
    nr_active_file 0
    nr_unevictable 0
    nr_mlock     0
    nr_anon_pages 0
    nr_mapped    2
    nr_file_pages 0
    nr_dirty     0
    nr_writeback 0
    nr_slab_reclaimable 0
    nr_slab_unreclaimable 0
    nr_page_table_pages 0
    nr_kernel_stack 0
    nr_unstable  0
    nr_bounce    0
    nr_vmscan_write 0
    nr_vmscan_immediate_reclaim 0
    nr_writeback_temp 0
    nr_isolated_anon 0
    nr_isolated_file 0
    nr_shmem     0
    nr_dirtied   0
    nr_written   0
    numa_hit     1
    numa_miss    0
    numa_foreign 0
    numa_interleave 0
    numa_local   1
    numa_other   0
    workingset_refault 0
    workingset_activate 0
    workingset_nodereclaim 0
    nr_anon_transparent_hugepages 0
    nr_free_cma  0
        protection: (0, 0, 514023, 514023)
  pagesets
    cpu: 0
              count: 176
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 1
              count: 159
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 2
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 3
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 4
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 5
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 6
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 7
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 8
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 9
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 10
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 11
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 12
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 13
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 14
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 15
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 16
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 17
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 18
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 19
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 20
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 21
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 22
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 23
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 24
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 25
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 26
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 27
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 28
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 29
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 30
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 31
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 32
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 33
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 34
              count: 30
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 35
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 36
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 37
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 38
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 39
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 40
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 41
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 42
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 43
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 44
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 45
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 46
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 47
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 48
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 49
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 50
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 51
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 52
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 53
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 54
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 55
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 56
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 57
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 58
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 59
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 60
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 61
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 62
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 63
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 64
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 65
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 66
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 67
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 68
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 69
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 70
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 71
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 72
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 73
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 74
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 75
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 76
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 77
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 78
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 79
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 80
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 81
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 82
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 83
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 84
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 85
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 86
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 87
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 88
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 89
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 90
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 91
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 92
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 93
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 94
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
    cpu: 95
              count: 0
              high:  186
              batch: 31
  vm stats threshold: 70
  all_unreclaimable: 0
  start_pfn:         4096
  inactive_ratio:    3
Node 0, zone   Normal
  pages free     108938090
        min      99720
        low      124650
        high     149580
        scanned  0
        spanned  133693440
        present  133693440
        managed  131590069
    nr_free_pages 108938090
    nr_alloc_batch 12150
    nr_inactive_anon 321
    nr_active_anon 796256
    nr_inactive_file 10192249
    nr_active_file 9318022
    nr_unevictable 0
    nr_mlock     0
    nr_anon_pages 785110
    nr_mapped    37652
    nr_file_pages 19510759
    nr_dirty     196
    nr_writeback 0
    nr_slab_reclaimable 813589
    nr_slab_unreclaimable 85426
    nr_page_table_pages 4258
    nr_kernel_stack 3119
    nr_unstable  0
    nr_bounce    0
    nr_vmscan_write 0
    nr_vmscan_immediate_reclaim 0
    nr_writeback_temp 0
    nr_isolated_anon 0
    nr_isolated_file 0
    nr_shmem     581
    nr_dirtied   1319245903
    nr_written   1316432895
    numa_hit     2931353744
    numa_miss    0
    numa_foreign 0
    numa_interleave 219529
    numa_local   2931353744
    numa_other   0
    workingset_refault 0
    workingset_activate 0
    workingset_nodereclaim 0
    nr_anon_transparent_hugepages 21
    nr_free_cma  0
        protection: (0, 0, 0, 0)
  pagesets
    cpu: 0
              count: 145
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 1
              count: 165
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 2
              count: 118
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 3
              count: 162
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 4
              count: 154
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 5
              count: 175
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 6
              count: 160
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 7
              count: 155
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 8
              count: 155
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 9
              count: 146
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 10
              count: 145
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 11
              count: 53
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 12
              count: 87
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 13
              count: 160
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 14
              count: 36
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 15
              count: 153
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 16
              count: 161
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 17
              count: 55
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 18
              count: 184
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 19
              count: 156
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 20
              count: 60
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 21
              count: 26
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 22
              count: 63
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 23
              count: 48
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 24
              count: 156
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 25
              count: 157
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 26
              count: 176
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 27
              count: 158
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 28
              count: 166
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 29
              count: 179
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 30
              count: 162
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 31
              count: 168
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 32
              count: 108
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 33
              count: 103
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 34
              count: 45
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 35
              count: 71
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 36
              count: 52
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 37
              count: 100
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 38
              count: 63
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 39
              count: 73
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 40
              count: 157
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 41
              count: 43
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 42
              count: 150
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 43
              count: 160
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 44
              count: 66
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 45
              count: 161
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 46
              count: 158
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 47
              count: 176
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 48
              count: 178
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 49
              count: 145
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 50
              count: 136
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 51
              count: 174
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 52
              count: 151
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 53
              count: 128
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 54
              count: 89
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 55
              count: 157
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 56
              count: 137
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 57
              count: 34
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 58
              count: 149
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 59
              count: 171
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 60
              count: 165
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 61
              count: 161
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 62
              count: 178
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 63
              count: 123
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 64
              count: 81
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 65
              count: 56
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 66
              count: 180
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 67
              count: 84
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 68
              count: 89
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 69
              count: 137
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 70
              count: 160
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 71
              count: 80
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 72
              count: 91
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 73
              count: 66
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 74
              count: 35
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 75
              count: 159
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 76
              count: 111
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 77
              count: 106
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 78
              count: 129
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 79
              count: 149
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 80
              count: 176
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 81
              count: 149
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 82
              count: 120
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 83
              count: 88
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 84
              count: 143
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 85
              count: 164
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 86
              count: 75
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 87
              count: 49
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 88
              count: 102
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 89
              count: 174
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 90
              count: 55
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 91
              count: 135
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 92
              count: 61
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 93
              count: 70
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 94
              count: 79
              high:  186
              batch: 31
  vm stats threshold: 125
    cpu: 95
              count: 133
              high:  186
              batch: 31
  vm stats threshold: 125
  all_unreclaimable: 0
  start_pfn:         1048576
  inactive_ratio:    70

```

