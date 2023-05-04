# Style guide for Verilog and SystemVerilog RTL

The coding guides are splited into three parts:
 - Naming conventions
 - Coding style 
 - Inline documentation

## Naming conventions

### Capitalization
All names must be written in lower case. This includes acronyms and abbreviations such as `sram`, `dpu`, `drra`, etc. The only exception are constants and parameters, which must be written in upper case. The following example shows how to use capitalization:
```sv
// Bad
module My_Module (
    input Clk,
    input Rst,
    input [width-1:0] DataInput,
    output [width-1:0] RESULT
);

// Good
module my_module (
    input              clk,
    input              rst,
    input  [WIDTH-1:0] data,
    output [WIDTH-1:0] result
);
```
The only exception to this rule are macros that are originally written in upper case and thus shouldn't be changed. For example DesignWare/ChipWare, SRAM macros, etc. 

### Naming
Signals, modules, parameters and all design elements should be named with descriptive names. Try to avoid abbreviations unless for extremely common names, such as `clk`, `rst_n`, `ack`, etc.
Remember that abreviations are not always clear. For example, addr could mean address or `adder`. The over-use of abbreviations can make the code difficult to read and understand, especially for people that are not familiar with the code. The following example shows how to use descriptive names:
```sv
// Bad
logic din;
logic dout;
logic re;
logic we;

// Good
logic data_in;
logic data_out;
logic read_enable;
logic write_enable;
```

## Coding style
The style is the way to write the code. It is important to follow the same style across all files and projects. This helps people that might be unfamiliar with the code to navigate through it. The style is defined by the following rules:

### Indentation
The indentation is done with 4 spaces. No tabs are allowed. Modify your editor settings to use 4 spaces when pressing the tab key. 

#### Vim
Add the following lines to your `.vimrc` file:
```vim
set tabstop=4
set shiftwidth=4
set expandtab
```

#### Emacs
Add the following lines to your `.emacs` file:
```elisp
(setq-default indent-tabs-mode nil)
(setq-default tab-width 4)
(setq indent-line-function 'insert-tab)
```

#### Visual Studio Code
Add the following lines to your `settings.json` file:
```sv
"editor.tabSize": 4,
"editor.insertSpaces": true,
```

#### Gedit
Add the following lines to your `.profile` file:
```sv
export GEDIT_TAB_WIDTH=4
export GEDIT_REPLACE_TABS=1
```

#### Other editors
Please refer to the editor documentation to find out how to change the indentation settings.

### Line length
The line length should be limited to a reasonable number of characters. [The historically recommended maximum line length is 80 characters](https://softwareengineering.stackexchange.com/a/148678). 
Nowadays with big computer screens 80 characters is usually too small.  
A good compromise is to limit the line length to 120 characters.
This is not a hard limit, but it should be used as a guideline. If a line is too long, it should be split into multiple lines. The line should be split at a logical point, for example, after a comma or an operator. The following example shows how to split a line:
```sv
// Bad
assign result = (a + b) * (c + d) * (e + f) * (g + h) * (i + j) * (k + l) * (m + n) * (o + p) * (q + r) * (s + t) * (u + v) * (w + x) * (y + z);

// Good
assign result = (a + b) * (c + d) * (e + f) * (g + h) * (i + j) * (k + l) *
                (m + n) * (o + p) * (q + r) * (s + t) * (u + v) * (w + x) *
                (y + z);
```
Some exceptions for this rule are allowed. For example, long strings or long comments can be longer than 80 characters. For example paths to standard cell libraries, long names for macros, etc. In general you won't need to read and understand those lines, so it does not matter if they are longer than 80 characters.

### Spaces
Spaces should be used to improve readability. Spaces should be used after commas, semicolons, operators, etc. The following example shows how to use spaces:
```sv
// Bad
assign result=(a+b)*(c+d);

// Good
assign result = (a + b) * (c + d);
```

### Alignment
Alignment should be used to improve readability. The following example shows how to use alignment:
```sv
// Bad
assign result = (a + b) * (c + d);
assign result2 = (a + b) * (c + d);
assign result3 = (a + b) * (c + d);

// Good
assign result  = (a + b) * (c + d);
assign result2 = (a + b) * (c + d);
assign result3 = (a + b) * (c + d);
```

For module declarations, each port should be in a new line and aligned. The following example shows how to align module ports:
```sv
// Bad
module my_module (input clk, input rst, input [7:0] data, output reg [7:0] result);

// Good
module my_module (
    input       clk,
    input       rst,
    input [7:0] data,
    output reg  [7:0] result
);
```

For module instantiations, each port and parameter should also be in a new line and aligned. The following example shows how to align module ports and parameters:
```sv
// Bad
my_module $(WIDTH = 4, DEPTH = 5) my_module_inst (.clk(clk), .rst(rst), .data(data), .result(result));

// Good
my_module #(
    .WIDTH(4),
    .DEPTH(5)
) my_module_inst (
    .clk(clk),
    .rst(rst),
    .data(data),
    .result(result)
);
```

