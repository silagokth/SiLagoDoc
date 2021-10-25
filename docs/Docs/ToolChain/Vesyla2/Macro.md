# Macro Expansion Preprocess

Veyla uses [inja](https://github.com/pantor/inja) to implement the macro expansion preprocess. The program which performs the macro expansion is called **VeExpand**. This program will convert a template file to an output file with the help of a json formatted data file. In the template file, one can use macros which should be substituted by the variable values defined in the data file when the macro expansion is performed. The Grammar rule is based on the [inja grammar](#inja-grammar).

## Program Execution Format

### Command

```sh
$VESYLA_BIN/VeExpand -t template_file [-d data_file] -o output_file
```

### Arguments

- -t/-template: specify the template file. Default value is "template.cpp".
- -d/-data: specify the data file in json format. This argument is optional.
- -o/-output: specify the output file. This file should be a **_.cpp_** file that include the expanded model.

## Inja Grammar

### Basics
Whatever content that is not specially marked in the template file will be considered as normal content and will be directly copied to the output file.

To reference a simple variable, use `{{ }}` to wrap the varible name. All referenced variable should be defined in the data file.

!!! Example
    The example shows how to reference a simple variable.

    The template file:

    ```inja
    int x = {{VAR_A}};
    ```

    The data file:

    ```json
    {"VAR_A" : 2}
    ```

    The redered output would be:

    ```cpp
    int x = 2;
    ```

To write a comment, use `{# #}` to wrap the commented words.

!!! Example
    The example shows how to write a commnet.

    The template file:

    ```inja
    int x = 2; {# This is a comment #};
    ```

    The redered output would be:

    ```cpp
    int x = 2;
    ```


Inja supports complex types (like array or object) to be wrapped in `{{ }}`.

!!! Example
    The example shows how to reference a complex variable.

    The template file:

    ```inja
    int x = {{VAR_A/1}};
    int y = {{VAR_B/one}};
    ```

    The data file:

    ```json
    {
        "VAR_A" : [2, 3, 4],
        "VAR_B" : {
            "one": 1,
            "two": 2
        }
    }
    ```

    The redered output would be:

    ```cpp
    int x = 2;
    int y = 1;
    ```

### Commands

Several commands are supported by inja to implement control flow. They include: branches and loops. The commands are wrapped in `{% %}`.

!!! Example
    The example shows how to implement a branch.

    The template file:

    ```inja
    {% if VAR_A <= 0 %}int x = 0;
    {% else %}int x = {{VAR_A}};
    {% endif %}
    ```

    The data file:

    ```json
    {"VAR_A" : 2}
    ```

    The redered output would be:

    ```cpp
    int x = 2;
    ```

!!! Example
    The example shows how to implement a loop.

    The template file:

    ```inja
    {% for i in range(3) %}int x = {{i}};
    {% endfor %}
    ```

    The redered output would be:

    ```cpp
    int x = 0;
    int x = 1;
    int x = 2;
    ```

### Pre-defined functions

There are many pre-defined functions that are directly supported by inja.

!!! Example
    The example shows how to use pre-defined functions.

    The template file:

    ```inja
    Hello {{ world }}!
    Hello {{ upper(world) }}!
    Hello {{ lower(world) }}!

    {% for i in range(4) %}{{ loop/index1 }}{% endfor %}

    In total {{ length(guests) }} guests.

    {{ first(guests) }} was first.
    {{ last(guests) }} was last.

    Guest list in alphabetic order: {{ sort(guests) }}.

    {{ round(3.1415, 0) }}
    {{ round(3.1415, 3) }}

    {% if odd(a) %}{{a}} is odd! {% endif %}
    {% if even(a) %}{{a}} is even! {% endif %}
    {% if divisibleBy(a, 7) %}{{a}} is divisible by 7! {% endif %}

    Max of vec is {{ max(vec) }}.
    Min of vec is {{ min(vec) }}.

    "m" can be converted to number {{ int(m) }}
    "n" can be converted to number {{ float(n) }}

    {% if exists("node1") and existsIn(node1, "node2") %}node1/node2 exists! The value is {{node1/node2}}{% endif %}
    ```
    
    The data file:

    ```json
    {
        "world" : "World",
        "guests" : ["Tom", "Mike", "John"],
        "a" : 42,
        "vec" : [1, 2, 3],
        "m" : "2",
        "n" : "1.85",
        "node1" : {
        	"node2" : 1
        }
    }
    ```

    The redered output would be:

    ```cpp
    Hello World!
    Hello WORLD!
    Hello world!

    1234

    In total 3 guests.

    Tom was first.
    John was last.

    Guest list in alphabetic order: ["John","Mike","Tom"].

    3.0
    3.142


    42 is even! 
    42 is divisible by 7! 

    Max of vec is 3.
    Min of vec is 1.

    "m" can be converted to number 2
    "n" can be converted to number 1.85

    node1/node2 exists! The value is 1
    ```