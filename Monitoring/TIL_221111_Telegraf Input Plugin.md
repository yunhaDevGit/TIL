# Telegraf Input Plugin

- **inputs.cpu**
    
    > `cpu` 플러그인은 시스템 CPU 메트릭을 수집합니다.
    > 
    - percpu
    - totalcpu
    - collect_cpu_time
    - report_active
    - **fields**
        
        ```
        **[fields]**
        	time_user (float)
        	time_system (float)
        	time_idle (float)
        	time_active (float)
        	time_nice (float)
        	time_iowait (float)
        	time_irq (float)
        	time_softirq (float)
        	time_steal (float)
        	time_guest (float)
        	time_guest_nice (float)
        	usage_user (float, percent)
        	usage_system (float, percent)
        	usage_idle (float, percent)
        	usage_active (float)
        	usage_nice (float, percent)
        	usage_iowait (float, percent)
        	usage_irq (float, percent)
        	usage_softirq (float, percent)
        	usage_steal (float, percent)
        	usage_guest (float, percent)
        	usage_guest_nice (float, percent)
        ```
        
- **inputs.disk**
    
    > `disk` 플러그인은 디스크 사용량에 대한 메트릭을 수집합니다.
    > 
    - ignore_fs
    - mount_points
    - **fields**
        
        ```
        **[fields]**
        	free (integer, bytes)
        	total (integer, bytes)
        	used (integer, bytes)
        	used_percent (float, percent)
        	inodes_free (integer, files)
        	inodes_total (integer, files)
        	inodes_used (integer, files)
        ```
        
- **inputs.diskio**
    
    > `diskio` 입력 플러그인은 디스크 트래픽 및 타이밍에 대한 메트릭을 수집합니다.
    > 
    - devices
    - ******fields******
        
        ```
        ************[fields]************
        	reads (integer, counter)
        	writes (integer, counter)
        	read_bytes (integer, counter, bytes)
        	write_bytes (integer, counter, bytes)
        	read_time (integer, counter, milliseconds)
        	write_time (integer, counter, milliseconds)
        	io_time (integer, counter, milliseconds)
        	weighted_io_time (integer, counter, milliseconds)
        	iops_in_progress (integer, gauge)
        	merged_reads (integer, counter)
        	merged_writes (integer, counter)
        ```
        
- **inputs.mem**
    
    > `mem` 플러그인은 시스템 메모리 메트릭을 수집합니다.
    > 
    - ********************fields********************
        
        ```
        ********************[fields]********************
        	active (integer)
        	available (integer)
        	buffered (integer)
        	cached (integer)
        	free (integer)
        	inactive (integer)
        	slab (integer)
        	total (integer)
        	used (integer)
        	available_percent (float)
        	used_percent (float)
        	wired (integer)
        	commit_limit (integer)
        	committed_as (integer)
        	dirty (integer)
        	high_free (integer)
        	high_total (integer)
        	huge_page_size (integer)
        	huge_pages_free (integer)
        	huge_pages_total (integer)
        	low_free (integer)
        	low_total (integer)
        	mapped (integer)
        	page_tables (integer)
        	shared (integer)
        	sreclaimable (integer)
        	sunreclaim (integer)
        	swap_cached (integer)
        	swap_free (integer)
        	swap_total (integer)
        	vmalloc_chunk (integer)
        	vmalloc_total (integer)
        	vmalloc_used (integer)
        	write_back (integer)
        	write_back_tmp (integer)
        ```
        
- **inputs.net**
    
    > `net` 플러그인은 네트워크 인터페이스 및 프로토콜 사용에 대한 메트릭을 수집합니다. (Linux만 제공)
    > 
    - interfaces
    - ignore_protocol_stats
    - ************fields************
        
        ```
        **********************[fields]**********************
        	bytes_sent - The total number of bytes sent by the interface
        	********************************************bytes_recv - The total number of bytes received by the interface
        	packets_sent - The total number of packets sent by the interface
        	packets_recv - The total number of packets received by the interface
        	err_in - The total number of receive errors detected by the interface
        	err_out - The total number of transmit errors detected by the interface
        	drop_in - The total number of received packets dropped by the interface
        	drop_out - The total number of transmitted packets dropped by the interface
        ```
        
- **inputs.processes**
    
    > `processes` 플러그인은 총 프로세스 수에 대한 정보를 수집하고 상태(zombie, sleeping, running 등)별로 그룹화합니다.
    > 
    - ************fields************
        
        ```
        **************[fields]**************
        	blocked (aka disk sleep or uninterruptible sleep)
        	running
        	sleeping
        	stopped
        	total
        	zombie
        	dead
        	wait (freebsd only)
        	idle (bsd and Linux 4+ only)
        	paging (linux only)
        	parked (linux only)
        	total_threads (linux only)
        ```
        
- **inputs.swap**
    
    > `swap` 플러그인은 시스템 swap 메트릭을 수집합니다.
    > 
    - ********fields********
