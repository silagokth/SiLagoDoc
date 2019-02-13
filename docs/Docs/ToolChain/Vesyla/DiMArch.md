# DiMArch Reading and Writing

To read from DiMArch cell or write to DiMArch cell, we need several collaborating instructions. They are ````ROUTE```` instruction, ````REFI```` instruction and ````SRAM_R````/````SRAM_W```` instruction.

## Read (DiMArch -> Register file)

When reading from DiMArch to Register file, a path should be routed first by ````ROUTE```` instruction. ````ROUTE```` instruction has 2 critical timestamps: **issue** and **end**. ````ROUTE```` instruction will occupy the DiMArch NoC resource of all cells on the routed path until it's **end** timestamp reached.

During the time when the DiMArch NoC path is guaranteed by ````ROUTE```` instruction, a ````SRAM_R```` instruction is applied to configure the AGU on DiMArch side. ````SRAM_R```` instruction has 4 critical timestamps: **issue**, **arrive**, **active** and **end**. **Issue** time represents the cycle when sequencer issue the instruction, **arrive** time represents the instruction reaches the destination DiMArch cell, **active** time represents the time point when the instruction starts outputing readed data, and **end** time indicates the instruction stops reading data.

At the same time, a ````REFI```` instruction should be activated in order to store the data from DiMArch to register file entries. ````REFI```` instruction has 3 critical timestamps: **issue**, **active** and **end**.

The timing relationship of those 3 instructions are shown in figure below:

{% dot sram_read_dep.png
    digraph G {
        subgraph cluster_route{
          label="ROUTE"
          route_issue [label="issue"];
          route_end [label="end"];
          route_issue -> route_end [label=">0"]
        }
        subgraph cluster_sram{
          label="SRAM_R"
          sram_issue [label="issue"];
          sram_arrive [label="arrive"];
          sram_active [label="active"];
          sram_end [label="end"];
          sram_issue -> sram_arrive [label="=(#hop+1)"];
          sram_arrive -> sram_active [label="=0"];
          sram_active -> sram_end [label="=#element"];
        }
        subgraph cluster_refi{
          label="REFI"
          refi_issue [label="issue"];
          refi_active [label="active"];
          refi_end [label="end"];
          refi_issue -> refi_active [label=">=0"];
          refi_active -> refi_end [label="=#element"];
        }
        route_issue -> sram_issue [label=">0"];
        route_issue -> refi_issue [label=">0"];
        sram_end -> route_end [label=">=0"];
        refi_end -> route_end [label=">=0"];
        sram_active -> refi_active[label="=#hop"]
    }
%}


## Write (Register file -> DiMArch)
Writing data from Register file to DiMArch share the same set of instruction with the same timestamps as reading operation. The only difference comes from the dependency arrows.


The timing relationship of those 3 instructions are shown in figure below:

{% dot sram_write_dep.png
    digraph G {
        subgraph cluster_route{
          label="ROUTE"
          route_issue [label="issue"];
          route_end [label="end"];
          route_issue -> route_end [label=">0"]
        }
        subgraph cluster_sram{
          label="SRAM_R"
          sram_issue [label="issue"];
          sram_arrive [label="arrive"];
          sram_active [label="active"];
          sram_end [label="end"];
          sram_issue -> sram_arrive [label="=(#hop+1)"];
          sram_arrive -> sram_active [label="=0"];
          sram_active -> sram_end [label="=#element"];
        }
        subgraph cluster_refi{
          label="REFI"
          refi_issue [label="issue"];
          refi_active [label="active"];
          refi_end [label="end"];
          refi_issue -> refi_active [label=">=0"];
          refi_active -> refi_end [label="=#element"];
        }
        route_issue -> sram_issue [label=">0"];
        route_issue -> refi_issue [label=">0"];
        sram_end -> route_end [label=">=0"];
        refi_end -> route_end [label=">=0"];
        refi_active -> sram_active[label="=#hop"]
    }
%}
