#Run an example program

## Create lenet example

To create input files necessary to run lenet example, you simply need to run execute the following command, where `lenet5` is the name of our wanted application in the testcase program.


```
python3 -m tb.testcase -n lenet5
```

After that, four input constraint files for lenet application will be generated in ``const/`` directory. Note that we need another constraint file called ``const/technology_constraint.json`` to run the tool chain, but this file depends on the underlying technology not the applicaiton. Therefore, this constraint file is already provided as an example and will not be changed.

## Run Sylva

We provided a script file (``run.sh``) calling the Python environment to run the program. You can type ``-h`` to see the parameters required for the program as also provided below.

```
>> ./run.sh -h
usage: main.py [-h] [-g GRAPH] [-c CONSTRAINT] [-l LIBRARY] [-p PARAMETER]
               [-t TECHNOLOGY] [-o OUTPUT]

options:
  -h, --help            show this help message and exit
  -g GRAPH, --graph GRAPH
                        SDF graph file
  -c CONSTRAINT, --constraint CONSTRAINT
                        global constraint file
  -l LIBRARY, --library LIBRARY
                        alimp library file
  -p PARAMETER, --parameter PARAMETER
                        hyper parameter file
  -t TECHNOLOGY, --technology TECHNOLOGY
                        technology constraint file
  -o OUTPUT, --output OUTPUT
                        output directory
```

Although you can also manually set the locations of each constraint, by defualt, all of them except ``output`` are pointed to ``const/`` with their specified file names. The output is located to ``bin/`` if it is not specified by the user. So, to run the example, simply use this command.

```
>> ./run.sh
```

## lenet Application

### Introduction

The application consists of 10 SDF nodes where the first and the last one are loading the data and storing the result, respectively. Other nodes perform their functionalities as described in ``examples/lenet5/model/``.

<p align="center">
    <img src="./Tutorial_lenet5/lenet_graph.png" />
</p>

### Main Compilation

The input constraints will go through stages of selection and optimisations as we have binding, placing, routing, noc synthesis, and glic synthesis. Normally, after each step is done, we will be able to vitualise its result in ``bin\``. A NoC synthesis result, for exmaple, is available in ``bin\noc\layout_graph.pdf``. Since the example is not small, you are expected to run this process about half an hour up to two hours, depending on your specifications.

After this process is done, it writes a binary file called ``db.bin`` as a result database file.

### Simulation Part

This part will be immediately invorked by the program after the main compilation. It uses the executables located at ``examples/lenet5/model/`` and also some additional data in ``examples/lenet5/data/``. Since lenet is a simple graph that has no splits or merges, we can see here in below log that it starts from the entry node, its corresponding transporter, conv1 node, and so on uptil the exit node.

Because we provide two memory images for simulation: one is the initial image and the other is supposed to be the correct image after running the simulation. It will compare the two images at the end and shows ``Fail to verify the design by simulation`` after this line ``INFO: Finish: ideal simulation`` if the images do not match. Otherwise, it will not display the line as you can see in the log.

```
INFO: Start: ideal simulation
Top[TopModule]: @[0]Triggering: load_input.
load_input[node]: [@0] Starting.
load_input[process]: [@0] preparing to run: examples/lenet5//model/load_input run --addr 0 --size 64 --global-image ./bin/ideal_sim/mem/global_mem_image.json --out-mem ./bin/ideal_sim/mem/load_input_outMem.json
load_input[process]: Process output = b'' (err=b'')
load_input[process]: Process done.
load_input[outAddrTranslater]: [@237] Done.
Top[TopModule]: Finishing: load_input.

Top[TopModule]: @[0]Triggering: transporter_edge_load_input_conv1.
transporter_edge_load_input_conv1[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_load_input_conv1.

Top[TopModule]: @[61]Triggering: conv1.
conv1[node]: [@61] Starting.
conv1[inAddrTranslater]: [@241] Done.
conv1[process]: [@61] preparing to run: examples/lenet5//model/conv run --input_image_channel 1 --input_image_size 32 --kernel_channel 6 --kernel_size 5 --stride 1 --kernel examples/lenet5//data/conv1_kernel.json --bias examples/lenet5//data/conv1_bias.json --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/conv1_inMem.json --out-mem ./bin/ideal_sim/mem/conv1_outMem.json
conv1[process]: Process output = b'' (err=b'')
conv1[process]: Process done.
conv1[outAddrTranslater]: [@1453] Done.
Top[TopModule]: Finishing: conv1.

Top[TopModule]: @[0]Triggering: transporter_edge_conv1_pooling1.
transporter_edge_conv1_pooling1[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_conv1_pooling1.

Top[TopModule]: @[76]Triggering: pooling1.
pooling1[node]: [@76] Starting.
pooling1[inAddrTranslater]: [@1460] Done.
pooling1[process]: [@76] preparing to run: examples/lenet5//model/pooling run --input_image_channel 6 --input_image_size 28 --kernel_size 2 --stride 2 --mode average --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/pooling1_inMem.json --out-mem ./bin/ideal_sim/mem/pooling1_outMem.json
pooling1[process]: Process output = b'' (err=b'')
pooling1[process]: Process done.
pooling1[outAddrTranslater]: [@470] Done.
Top[TopModule]: Finishing: pooling1.

Top[TopModule]: @[0]Triggering: transporter_edge_pooling1_conv2.
transporter_edge_pooling1_conv2[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_pooling1_conv2.

Top[TopModule]: @[115]Triggering: conv2.
conv2[node]: [@115] Starting.
conv2[inAddrTranslater]: [@483] Done.
conv2[process]: [@115] preparing to run: examples/lenet5//model/conv run --input_image_channel 6 --input_image_size 14 --kernel_channel 16 --kernel_size 5 --stride 1 --kernel examples/lenet5//data/conv2_kernel.json --bias examples/lenet5//data/conv2_bias.json --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/conv2_inMem.json --out-mem ./bin/ideal_sim/mem/conv2_outMem.json
conv2[process]: Process output = b'' (err=b'')
conv2[process]: Process done.
conv2[outAddrTranslater]: [@762] Done.
Top[TopModule]: Finishing: conv2.

Top[TopModule]: @[0]Triggering: transporter_edge_conv2_pooling2.
transporter_edge_conv2_pooling2[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_conv2_pooling2.

Top[TopModule]: @[164]Triggering: pooling2.
pooling2[node]: [@164] Starting.
pooling2[inAddrTranslater]: [@804] Done.
pooling2[process]: [@164] preparing to run: examples/lenet5//model/pooling run --input_image_channel 16 --input_image_size 10 --kernel_size 2 --stride 2 --mode average --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/pooling2_inMem.json --out-mem ./bin/ideal_sim/mem/pooling2_outMem.json
pooling2[process]: Process output = b'' (err=b'')
pooling2[process]: Process done.
pooling2[outAddrTranslater]: [@551] Done.
Top[TopModule]: Finishing: pooling2.

Top[TopModule]: @[0]Triggering: transporter_edge_pooling2_conv3.
transporter_edge_pooling2_conv3[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_pooling2_conv3.

Top[TopModule]: @[233]Triggering: conv3.
conv3[node]: [@233] Starting.
conv3[inAddrTranslater]: [@553] Done.
conv3[process]: [@233] preparing to run: examples/lenet5//model/conv run --input_image_channel 16 --input_image_size 5 --kernel_channel 120 --kernel_size 5 --stride 1 --kernel examples/lenet5//data/conv3_kernel.json --bias examples/lenet5//data/conv3_bias.json --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/conv3_inMem.json --out-mem ./bin/ideal_sim/mem/conv3_outMem.json
conv3[process]: Process output = b'' (err=b'')
conv3[process]: Process done.
conv3[outAddrTranslater]: [@722] Done.
Top[TopModule]: Finishing: conv3.

Top[TopModule]: @[0]Triggering: transporter_edge_conv3_reshape.
transporter_edge_conv3_reshape[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_conv3_reshape.

Top[TopModule]: @[249]Triggering: reshape.
reshape[node]: [@249] Starting.
reshape[inAddrTranslater]: [@737] Done.
reshape[process]: [@249] preparing to run: examples/lenet5//model/reshape run --input_row 120 --input_col 1 --output_row 1 --output_col 120 --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/reshape_inMem.json --out-mem ./bin/ideal_sim/mem/reshape_outMem.json
reshape[process]: Process output = b'' (err=b'')
reshape[process]: Process done.
reshape[outAddrTranslater]: [@291] Done.
Top[TopModule]: Finishing: reshape.

Top[TopModule]: @[0]Triggering: transporter_edge_reshape_fc1.
transporter_edge_reshape_fc1[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_reshape_fc1.

Top[TopModule]: @[269]Triggering: fc1.
fc1[node]: [@269] Starting.
fc1[inAddrTranslater]: [@294] Done.
fc1[process]: [@269] preparing to run: examples/lenet5//model/fc run --input_size 120 --output_size 84 --weight examples/lenet5//data/fc1_weight.json --bias examples/lenet5//data/fc1_bias.json --activation=tanh --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/fc1_inMem.json --out-mem ./bin/ideal_sim/mem/fc1_outMem.json
fc1[process]: Process output = b'' (err=b'')
fc1[process]: Process done.
fc1[outAddrTranslater]: [@300] Done.
Top[TopModule]: Finishing: fc1.

Top[TopModule]: @[0]Triggering: transporter_edge_fc1_fc2.
transporter_edge_fc1_fc2[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_fc1_fc2.

Top[TopModule]: @[282]Triggering: fc2.
fc2[node]: [@282] Starting.
fc2[inAddrTranslater]: [@316] Done.
fc2[process]: [@282] preparing to run: examples/lenet5//model/fc run --input_size 84 --output_size 10 --weight examples/lenet5//data/fc2_weight.json --bias examples/lenet5//data/fc2_bias.json --activation=softmax --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/fc2_inMem.json --out-mem ./bin/ideal_sim/mem/fc2_outMem.json
fc2[process]: Process output = b'' (err=b'')
fc2[process]: Process done.
fc2[outAddrTranslater]: [@282] Done.
Top[TopModule]: Finishing: fc2.

Top[TopModule]: @[0]Triggering: transporter_edge_fc2_store_output.
transporter_edge_fc2_store_output[node]: [@0] Starting.
Top[TopModule]: Finishing: transporter_edge_fc2_store_output.

Top[TopModule]: @[284]Triggering: store_output.
store_output[node]: [@284] Starting.
store_output[inAddrTranslater]: [@287] Done.
store_output[process]: [@284] preparing to run: examples/lenet5//model/store_output run --addr 64 --size 1 --global-image ./bin/ideal_sim/mem/global_mem_image.json --in-mem ./bin/ideal_sim/mem/store_output_inMem.json
store_output[process]: Process output = b'' (err=b'')
store_output[process]: Process done.
Top[TopModule]: Finishing: store_output.

INFO: Finish: ideal simulation
INFO: Start: memory synthesis
INFO: Finish: memory synthesis
INFO: Sylva finished successfully!
```



