# ftrace

Trace a single function:

    cd /sys/kernel/debug/tracing/

    # Stop tracing.
    echo 0 > tracing_on

    # Clear previous trace.
    echo '' > trace

    echo 1 > max_graph_depth

    # List the available tracers, and pick one.
    cat available_tracers
    echo function > current_tracer

    # List all functions that can be traced
    # cat available_filter_functions
    # Choose one.
    echo __kmalloc >set_ftrace_filter
    # Confirm that only __kmalloc is enabled.
    cat enabled_functions

    echo 1 > tracing_on

    # Latest events.
    head trace

    # Observe trace continously, and drain seen events out.
    cat trace_pipe

Sample output:

    # tracer: function
    #
    # entries-in-buffer/entries-written: 97/97   #P:1
    #
    #                              _-----=> irqs-off
    #                             / _----=> need-resched
    #                            | / _---=> hardirq/softirq
    #                            || / _--=> preempt-depth
    #                            ||| /     delay
    #           TASK-PID   CPU#  ||||    TIMESTAMP  FUNCTION
    #              | |       |   ||||       |         |
                head-228   [000] ....   825.534637: __kmalloc <-load_elf_phdrs
                head-228   [000] ....   825.534692: __kmalloc <-load_elf_binary
                head-228   [000] ....   825.534815: __kmalloc <-load_elf_phdrs
                head-228   [000] ....   825.550917: __kmalloc <-__seq_open_private
                head-228   [000] ....   825.550953: __kmalloc <-tracing_open
                head-229   [000] ....   826.756585: __kmalloc <-load_elf_phdrs
                head-229   [000] ....   826.756627: __kmalloc <-load_elf_binary
                head-229   [000] ....   826.756719: __kmalloc <-load_elf_phdrs
                head-229   [000] ....   826.773796: __kmalloc <-__seq_open_private
                head-229   [000] ....   826.773835: __kmalloc <-tracing_open
                head-230   [000] ....   827.174988: __kmalloc <-load_elf_phdrs
                head-230   [000] ....   827.175046: __kmalloc <-load_elf_binary
                head-230   [000] ....   827.175171: __kmalloc <-load_elf_phdrs

Trace all possible functions, and draw a call graph:

    echo 1 > events/enable
    echo function_graph > current_tracer

Sample output:

    # CPU  DURATION                  FUNCTION CALLS
    # |     |   |                     |   |   |   |
     0)   2.173 us    |                  } /* ntp_tick_length */
     0)               |                  timekeeping_update() {
     0)   4.176 us    |                    ntp_get_next_leap();
     0)   5.016 us    |                    update_vsyscall();
     0)               |                    raw_notifier_call_chain() {
     0)   2.241 us    |                      notifier_call_chain();
     0) + 19.879 us   |                    }  
     0)   3.144 us    |                    update_fast_timekeeper();
     0)   2.738 us    |                    update_fast_timekeeper();
     0) ! 117.147 us  |                  }
     0)               |                  _raw_spin_unlock_irqrestore() {
     0)   4.045 us    |                    _raw_write_unlock_irqrestore();
     0) + 22.066 us   |                  }   
     0) ! 265.278 us  |                } /* update_wall_time */

TODO: what do `+` and `!` mean?

Each `enable` under the `events/` tree enables a certain set of functions, the higher the `enable` more functions are enabled.
