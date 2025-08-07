#Run an example program

## Create lenet5 example

To create input files necessary to run lenet5 example, you simply need to run execute the following command in the main directory, where `lenet5` is the name of our wanted application in the testcase program.


```
./bin/config --name lenet5 --output config
```

After that, five input constraint files for lenet5 application will be generated in ``config/`` directory, which in fact can be somewhere else by changing the name of ```--output``` argument.

## Run Sylva

We provided a script file (``run.sh``) calling ```./bin/sylva``` to run the program. You can type ```./bin/sylva -h``` to see the arguments required for the program as also provided below.

```
>> ./bin/sylva -h
Arguments to get the configuration files and output directory

Usage: sylva --graph <GRAPH> --constraint <CONSTRAINT_FILE> --library <ALIMP_LIB> --parameter <HYPER_PARAMETER> --technology <TECHNOLOGY_CONSTRAINT> --output <OUTPUT>

Options:
  -g, --graph <GRAPH>                       SDF graph file
  -c, --constraint <CONSTRAINT_FILE>        global constraint file
  -l, --library <ALIMP_LIB>                 alimp library file
  -p, --parameter <HYPER_PARAMETER>         hyper parameter file
  -t, --technology <TECHNOLOGY_CONSTRAINT>  technology constraint file
  -o, --output <OUTPUT>                     output directory
  -h, --help                                Print help
  -V, --version                             Print version
```

Although you can also manually set the locations of each constraint, by defualt, all of them except ``output`` are pointed to ``config/`` with their specified file names if you generated these files using the above command. The output is located to ``out/`` in ```./run.sh``` script. So, simply type the following command to run Sylva tool chain to execute input files in ```config/``` and set the working directory to ```out/```. Please note that the tool chain uses Rust logging, so you are requested to set path variable ```RUST_LOG``` to at least ```info``` level to be able to see the information from the tool chain.

```
>> RUST_LOG=info ./run.sh
```

## lenet5 Application

### Introduction

The application consists of 10 SDF nodes where the first and the last one are loading the data and storing the result, respectively. Other nodes perform their functionalities as described in ``examples/lenet5/`` (You will have to install the tool chain first if you do not see this).

<p align="center">
    <img src="./Tutorial_lenet5/lenet_graph.png" />
</p>

### Main Compilation

The input constraints will go through stages of selection and optimisations as we have binding, placing, routing, noc synthesis, and glic synthesis. Normally, after each step is done, we will be able to vitualise its result in ```{output}\```. A NoC synthesis result, for exmaple, is available in ```{output}\noc\layout_graph.pdf```. Since the example is not small, you are expected to run this process about half an hour up to two hours, depending on your computer specifications.

After this process is done, it writes a binary file called ``{output}/db.bin`` as a result database file.

Note that ```{output}``` is a directory set by sylva executable. It is set to ```out/``` when you run the provided ```./run.sh``` script.

### Simulation Part

This part will be immediately invorked by the program after the main compilation. It uses the executables located at ```examples/lenet5/``` and also some additional data in ``examples/lenet5/data/``. Since lenet is a simple graph that has no splits or merges, we can see here in below log that it starts from the entry node, its corresponding transporter, conv1 node, and so on uptil the exit node.

Because we provide two memory images for simulation: one is the initial image and the other is supposed to be the correct image after running the simulation. It will compare the two images at the end and shows ```Failed to verify the simulation``` after this line ```[INFO  sylva::sim]  Finish: ideal simulation```, if the images do not match. Otherwise, it will not display the line as you can see in the log.

```
[2025-08-07T13:49:32Z INFO  sylva::sim] Start: simulation
[2025-08-07T13:49:32Z INFO  sylva::sim] Stage 1: generate simulation files
[2025-08-07T13:49:32Z INFO  sylva::sim] Stage 2: run simulation

[stdout] Instantiating: fc2
[stdout] Instantiating: fc1
[stdout] Instantiating: store_output
[stdout] Instantiating: pooling2
[stdout] Instantiating: pooling1
[stdout] Instantiating: transporter_edge_load_input_conv1
[stdout] Instantiating: transporter_edge_conv2_pooling2
[stdout] Instantiating: transporter_edge_pooling2_conv3
[stdout] Instantiating: transporter_edge_reshape_fc1
[stdout] Instantiating: conv1
[stdout] Instantiating: transporter_edge_pooling1_conv2
[stdout] Instantiating: conv3
[stdout] Instantiating: conv2
[stdout] Instantiating: transporter_edge_fc1_fc2
[stdout] Instantiating: transporter_edge_conv1_pooling1
[stdout] Instantiating: transporter_edge_conv3_reshape
[stdout] Instantiating: reshape
[stdout] Instantiating: transporter_edge_fc2_store_output
[stdout] Instantiating: load_input
[stdout] Completed initalisation process.
[stdout] 
[stdout] [@0] Triggering: load_input
[stdout] [@0] Starting.
[stdout] [@0] Trigger process module.
[stdout] Main process is preparing to run: examples/lenet5/load-input --addr 0 --size 64 --global-image out/sim/mem/global_mem_image.json --out-mem out/sim/mem/load_input_outMem.json
[stdout] Process output = 
[stdout] [@0] Starting output Addr Translation.
[stdout] [@237] Output Addr Translation done.
[stdout] Finishing: load_input
[stdout] 
[stdout] [@0] Triggering: transporter_edge_load_input_conv1
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from load_input to conv1
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_load_input_conv1
[stdout] 
[stdout] [@61] Triggering: conv1
[stdout] [@61] Starting.
[stdout] [@61] Trigger process module.
[stdout] [@61] Starting input Addr Translation.
[stdout] [@241] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/conv --input_image_channel 1 --input_image_size 32 --kernel_channel 6 --kernel_size 5 --stride 1 --kernel examples/lenet5/data/conv1_kernel.json --bias examples/lenet5/data/conv1_bias.json --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/conv1_inMem.json --out-mem out/sim/mem/conv1_outMem.json
[stdout] Process output = 
[stdout] [@61] Starting output Addr Translation.
[stdout] [@1453] Output Addr Translation done.
[stdout] Finishing: conv1
[stdout] 
[stdout] [@0] Triggering: transporter_edge_conv1_pooling1
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from conv1 to pooling1
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_conv1_pooling1
[stdout] 
[stdout] [@76] Triggering: pooling1
[stdout] [@76] Starting.
[stdout] [@76] Trigger process module.
[stdout] [@76] Starting input Addr Translation.
[stdout] [@1460] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/pooling --input_image_channel 6 --input_image_size 28 --kernel_size 2 --stride 2 --mode average --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/pooling1_inMem.json --out-mem out/sim/mem/pooling1_outMem.json
[stdout] Process output = 
[stdout] [@76] Starting output Addr Translation.
[stdout] [@470] Output Addr Translation done.
[stdout] Finishing: pooling1
[stdout] 
[stdout] [@0] Triggering: transporter_edge_pooling1_conv2
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from pooling1 to conv2
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_pooling1_conv2
[stdout] 
[stdout] [@113] Triggering: conv2
[stdout] [@113] Starting.
[stdout] [@113] Trigger process module.
[stdout] [@113] Starting input Addr Translation.
[stdout] [@481] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/conv --input_image_channel 6 --input_image_size 14 --kernel_channel 16 --kernel_size 5 --stride 1 --kernel examples/lenet5/data/conv2_kernel.json --bias examples/lenet5/data/conv2_bias.json --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/conv2_inMem.json --out-mem out/sim/mem/conv2_outMem.json
[stdout] Process output = 
[stdout] [@113] Starting output Addr Translation.
[stdout] [@760] Output Addr Translation done.
[stdout] Finishing: conv2
[stdout] 
[stdout] [@0] Triggering: transporter_edge_conv2_pooling2
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from conv2 to pooling2
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_conv2_pooling2
[stdout] 
[stdout] [@162] Triggering: pooling2
[stdout] [@162] Starting.
[stdout] [@162] Trigger process module.
[stdout] [@162] Starting input Addr Translation.
[stdout] [@802] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/pooling --input_image_channel 16 --input_image_size 10 --kernel_size 2 --stride 2 --mode average --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/pooling2_inMem.json --out-mem out/sim/mem/pooling2_outMem.json
[stdout] Process output = 
[stdout] [@162] Starting output Addr Translation.
[stdout] [@549] Output Addr Translation done.
[stdout] Finishing: pooling2
[stdout] 
[stdout] [@0] Triggering: transporter_edge_pooling2_conv3
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from pooling2 to conv3
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_pooling2_conv3
[stdout] 
[stdout] [@231] Triggering: conv3
[stdout] [@231] Starting.
[stdout] [@231] Trigger process module.
[stdout] [@231] Starting input Addr Translation.
[stdout] [@551] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/conv --input_image_channel 16 --input_image_size 5 --kernel_channel 120 --kernel_size 5 --stride 1 --kernel examples/lenet5/data/conv3_kernel.json --bias examples/lenet5/data/conv3_bias.json --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/conv3_inMem.json --out-mem out/sim/mem/conv3_outMem.json
[stdout] Process output = 
[stdout] [@231] Starting output Addr Translation.
[stdout] [@720] Output Addr Translation done.
[stdout] Finishing: conv3
[stdout] 
[stdout] [@0] Triggering: transporter_edge_conv3_reshape
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from conv3 to reshape
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_conv3_reshape
[stdout] 
[stdout] [@247] Triggering: reshape
[stdout] [@247] Starting.
[stdout] [@247] Trigger process module.
[stdout] [@247] Starting input Addr Translation.
[stdout] [@735] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/reshape --input_row 120 --input_col 1 --output_row 1 --output_col 120 --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/reshape_inMem.json --out-mem out/sim/mem/reshape_outMem.json
[stdout] Process output = 
[stdout] [@247] Starting output Addr Translation.
[stdout] [@289] Output Addr Translation done.
[stdout] Finishing: reshape
[stdout] 
[stdout] [@0] Triggering: transporter_edge_reshape_fc1
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from reshape to fc1
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_reshape_fc1
[stdout] 
[stdout] [@266] Triggering: fc1
[stdout] [@266] Starting.
[stdout] [@266] Trigger process module.
[stdout] [@266] Starting input Addr Translation.
[stdout] [@291] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/fc --input_size 120 --output_size 84 --weight examples/lenet5/data/fc1_weight.json --bias examples/lenet5/data/fc1_bias.json --activation=tanh --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/fc1_inMem.json --out-mem out/sim/mem/fc1_outMem.json
[stdout] Process output = 
[stdout] [@266] Starting output Addr Translation.
[stdout] [@297] Output Addr Translation done.
[stdout] Finishing: fc1
[stdout] 
[stdout] [@0] Triggering: transporter_edge_fc1_fc2
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from fc1 to fc2
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_fc1_fc2
[stdout] 
[stdout] [@279] Triggering: fc2
[stdout] [@279] Starting.
[stdout] [@279] Trigger process module.
[stdout] [@279] Starting input Addr Translation.
[stdout] [@313] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/fc --input_size 84 --output_size 10 --weight examples/lenet5/data/fc2_weight.json --bias examples/lenet5/data/fc2_bias.json --activation=softmax --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/fc2_inMem.json --out-mem out/sim/mem/fc2_outMem.json
[stdout] Process output = 
[stdout] [@279] Starting output Addr Translation.
[stdout] [@279] Output Addr Translation done.
[stdout] Finishing: fc2
[stdout] 
[stdout] [@0] Triggering: transporter_edge_fc2_store_output
[stdout] [@0] Starting.
[stdout] [@0] Trigger Transporter.
[stdout] [@0] Transferring data from fc2 to store_output
[stdout] Transporter done.
[stdout] Finishing: transporter_edge_fc2_store_output
[stdout] 
[stdout] [@281] Triggering: store_output
[stdout] [@281] Starting.
[stdout] [@281] Trigger process module.
[stdout] [@281] Starting input Addr Translation.
[stdout] [@284] Input Addr Translation done.
[stdout] Main process is preparing to run: examples/lenet5/store-output --addr 64 --size 1 --global-image out/sim/mem/global_mem_image.json --in-mem out/sim/mem/store_output_inMem.json
[stdout] Process output = 
[stdout] Finishing: store_output
[stdout] 
[stdout] Exiting simulation

[2025-08-07T13:49:32Z INFO  sylva::sim] Stage 3: verify simulation
[2025-08-07T13:49:32Z INFO  sylva::sim] Finish: simulation
[2025-08-07T13:49:32Z INFO  sylva] Sylva finished successfully!
```



