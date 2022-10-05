# Coding Guidelines for RTL and VHDL coding

## SiLago Team

- Supervisor:
  > Ahmed Hemani <hemani@kth.se>

- HW architecture and verification:
  - Post-Doc:
    > Dimitrios Stathis <stathis@kth.se>

  - PhD students:
    > Jordi Altayo Gonzalez <jordiag@kth.se>

    > Ritika

- Backend software and compilers:
  - Post-Doc:
    > Yu Yang

  - PhD students:
    > Bjorn

    > Saba

- Frontend and applications:
  - PhD students:
    > Sofia

    > Ivan

## File Header

Each file in the SiLago project has to contain a header that includes the copyright, license, author and change log information. The header should have the following form:

```
-------------------------------------------------------
--! @file <name of the file>
--! @brief <Brief description of the file>
--! @details <Detail description of the file (optional)>
--! @author <Author’s Name>
--! @version <Version>
--! @date <Date of last edit>
--! @bug <Known Bugs (“NONE” if there are no bugs)>
--! @todo <Todo, create one “--! @todo” for every todo>
--! @copyright GNU Public License [GPL-3.0].
-------------------------------------------------------
---------------- Copyright (c) notice -----------------------------------------
--
-- The VHDL code, the logic and concepts described in this file constitute
-- the intellectual property of the authors listed below, who are affiliated
-- to KTH(Kungliga Tekniska Högskolan), School of EECS, Kista.
-- Any unauthorised use, copy or distribution is strictly prohibited.
-- Any authorised use, copy or distribution should carry this copyright notice
-- unaltered.
-------------------------------------------------------------------------------
-- Title      : <Title of the Entity or Package>
-- Project    : <Project that file belongs to (e.g. SiLago, eBrain, etc)>
-------------------------------------------------------------------------------
-- File       : <name of the file>
-- Library    : <Name of the library that this file should be compiled in>
-- Author     : <Author’s Name>
-- Company    : KTH
-- Created    : <Date of creation>
-- Last update: <Date of last edit>
-- Platform   : SiLago
-- Standard   : VHDL'08
-------------------------------------------------------------------------------
-- Copyright (c) <year of creation>
-------------------------------------------------------------------------------
-- Contact    : <Main contact person eg. Dimitrios Stathis <stathis@kth.se>>
-------------------------------------------------------------------------------
-- Revisions  :
-- Date        Version  Author                  Description
-- 2020-12-07  1.0      <Authors Name>          <description>
-------------------------------------------------------------------------------

--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#
--                                                                         #
--This file is part of SiLago.                                             #
--                                                                         #
--    SiLago platform source code is distributed freely: you can           #
--    redistribute it and/or modify it under the terms of the GNU          #
--    General Public License as published by the Free Software Foundation, #
--    either version 3 of the License, or (at your option) any             #
--    later version.                                                       #
--                                                                         #
--    SiLago is distributed in the hope that it will be useful,            #
--    but WITHOUT ANY WARRANTY; without even the implied warranty of       #
--    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the        #
--    GNU General Public License for more details.                         #
--                                                                         #
--    You should have received a copy of the GNU General Public License    #
--    along with SiLago.  If not, see <https://www.gnu.org/licenses/>.     #
--                                                                         #
--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#

```

### VSCODE Snippet

This is the snippet code for vs-code. You can modify the code and add it as a snippet in your favorite editor.

```
"vhdl_notice": {
        "scope": "vhdl",
        "prefix": "note",
        "body": [
            "-------------------------------------------------------",
            "--! @file $TM_FILENAME",
            "--! @brief ${1:UnitX}",
            "--! @details ",
            "--! @author ${3:Dimitrios Stathis}",
            "--! @version 1.0",
            "--! @date $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE",
            "--! @bug NONE",
            "--! @todo NONE",
            "--! @copyright  GNU Public License [GPL-3.0].",
            "-------------------------------------------------------",
            "---------------- Copyright (c) notice -----------------------------------------",
            "--",
            "-- The VHDL code, the logic and concepts described in this file constitute",
            "-- the intellectual property of the authors listed below, who are affiliated",
            "-- to KTH(Kungliga Tekniska Högskolan), School of EECS, Kista.",
            "-- Any unauthorised use, copy or distribution is strictly prohibited.",
            "-- Any authorised use, copy or distribution should carry this copyright notice",
            "-- unaltered.",
            "-------------------------------------------------------------------------------",
            "-- Title      : ${1:UnitX}",
            "-- Project    : ${2:SiLago}",
            "-------------------------------------------------------------------------------",
            "-- File       : $TM_FILENAME",
            "-- Library    : ${4:work}$"
            "-- Author     : ${3:Dimitrios Stathis}",
            "-- Company    : KTH",
            "-- Created    : $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE",
            "-- Last update: $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE",
            "-- Platform   : ${2:SiLago}",
            "-- Standard   : VHDL'08",
            "-------------------------------------------------------------------------------",
            "-- Copyright (c) $CURRENT_YEAR",
            "-------------------------------------------------------------------------------",
            "-- Contact    : ${3:Dimitrios Stathis} ${4:<stathis@kth.se>}",
            "-------------------------------------------------------------------------------",
            "-- Revisions  :",
            "-- Date        Version  Author                  Description",
            "-- $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE  1.0      ${3:Dimitrios Stathis}      Created",
            "-------------------------------------------------------------------------------",
            "",
            "--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#",
            "--                                                                         #",
            "--This file is part of ${2:SiLago}.                                             #",
            "--                                                                         #",
            "--    ${2:SiLago} platform source code is distributed freely: you can           #",
            "--    redistribute it and/or modify it under the terms of the GNU          #",
            "--    General Public License as published by the Free Software Foundation, #",
            "--    either version 3 of the License, or (at your option) any             #",
            "--    later version.                                                       #",
            "--                                                                         #",
            "--    ${2:SiLago} is distributed in the hope that it will be useful,            #",
            "--    but WITHOUT ANY WARRANTY; without even the implied warranty of       #",
            "--    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the        #",
            "--    GNU General Public License for more details.                         #",
            "--                                                                         #",
            "--    You should have received a copy of the GNU General Public License    #",
            "--    along with ${2:SiLago}.  If not, see <https://www.gnu.org/licenses/>.     #",
            "--                                                                         #",
            "--~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~#",

```

## Style and Formatting

1. Tabs and indentation 	          :
  - Each indentation and tab should be 2 spaces. Remember to change the settings of your editor so that it adds 2 space characters when pressing tab, and not a ‘tab’ character.
2.	Keywords			                  :
  - All VHDL keywords, such as SIGNAL, VARIABLE, etc., should be capitalized.
3.	=, <=, =>, :, and :=		        :
  - All VHDL operands should be aligned as best as possible.
4.	Constant’s name		              :
  - The names of all constants should be capitalized.
5.	Signal’s & variable’s name      :
  - A Signal/Variable’s name should be low-case. The name should be descriptive and multiple words should be separated with ‘_’.
6.	Type’s name			                :
  - A type or subtype name should be in low-case letters and end with the suffix _ty. For example conf_ty.
7.	Special suffix for signal names	:
  - When a signal is active on a negative level (for example a negative triggered reset) use the suffix _n. For example rst_n.
8.	Entity’s name 		              :
  - The name of the entity should be the same as the name of the file and low-case.
9.	Architecture’s name 		        :
  - The name of the architecture should be the name of the entity with the suffix ‘_BEH’, ‘_STR’ for behavioral or structural style.
10.	ENTITY			                    :
  - When defying an entity the GENERIC and PORT should always start in a new line. The declaration of the signals and generics should also start in a new line. For example:

```
ENTITY <NAME> IS
  GENERIC (
    width : NATURAL := 8 --! Input width (N)
  );
  PORT (
    in_a : IN STD_LOGIC_VECTOR(width - 1 DOWNTO 0);
    in_b : IN STD_LOGIC_VECTOR(width - 1 DOWNTO 0);
    conf : IN STD_LOGIC;
    out  : OUT STD_LOGIC_VECTOR(2 * width - 1 DOWNTO 0)
  );
```

11.	IF - THEN … ELSE … END	:
  - IF - THEN … ELSE statements should always start in a new line and comments should be added in the previous line, before the condition. Always leave an empty line before and after the code block for readability. For example:

```
-- This if statement checks condition A, if True it does code A
IF condition A THEN

	Code A

-- IF condition B is true it does code B
ELSIF condition B THEN

	Code B

-- In other case do code other
ELSE

	Code other

END;
```

12.	CASE, FOR, etc.			:
  - Use the same guide as IF.
13.	WHEN … ELSE			:
  - When using WHEN … ELSE there should be a new line after the ELSE. For example:

```
-- Description comment
<signal> <= '1' WHEN <condition> ELSE
    '0';
```

14.	IF/FOR … GENERATE		:
  - Generate statements should always have a descriptive name.
15.	PORT MAPS			:
  - When instantiating a component, the GENERIC/PORT MAP should always start in a new line and the port/generic connection should also start in a new line. The component should always have a descriptive name starting with U_. Also, it is recommended to instantiate using ENTITY instead of COMPONENT. Before the installation there should be a comment to describe the component. For example:

```
--! This component is doing something
DUT : ENTITY work.conf_mul_Beh
    GENERIC MAP(
      width => bit_width
    )
    PORT MAP(
      in_a => STD_LOGIC_VECTOR(inst_a),
      in_b => STD_LOGIC_VECTOR(inst_b),
      conf => conf,
      prod => prod
    );
```

16.	ASSERTIONS			:
  - ASSERT, REPORT and SEVERITY should always start in a new line.
17.	There should be a maximum of 1 empty line between code segments and comments should be directly above or next to the code that they comment.

## Coding style

Each entity should implement a specific function of the design. It is not recommended to have large entities that implement complex functions. We recommend that when describing a complex function, you use one entity for each FSM of the design and a separate entity for each register files, data computation, etc.

###	Libraries
For all VHDL files always use `numeric_std` library and do not use `std_logic_arith`, `std_logic_signed`, or `std_logic_unsigned`.

###	Clocking
Always use the `rising_edge` or `falling_edge` for checking the clock edge and not `clk=’1’ and clk’event`.

###	 FSM description
When describing in VHDL it is recommended that you use a two-process design. In the two process design each ARCHITECTURE has two processes. The first process is a clocked process and registers the state of the FSM and any other signal that is required. The second process is combinatorial, and it is used to describe the FSM, using a CASE statement. When writing the combinatorial process the designer needs to be careful to include all the required signals in the sensitivity list and make sure that no latches are generated. While debugging, the ALL keyword can be used in the sensitivity list to make sure that the behavior of the process is correct. To avoid any latches, all signals that are edited inside the combinatorial process should be assigned a default value in the beginning of the process (before the case statement).

Alternatively, a single process design can be used. In a single process design, only one process is required in the entity. The process is clocked and it both describes the FSM and registers its state. The single process design should be used with care since it can only describe registered FSMs (i.e. the outputs of the FSM are always registered).

##	COMMENTING AND DOCUMENTATION

Commenting in RTL is important for you as a designer, and for the people taking over your code. It is important to remember that RTL is not programming, when you code in RTL you describe something real. So you should add descriptions before your entities or modules and your architecture that describe what exactly your entity, module or architecture is meant to implement. If you can not describe it in words, then you can not implement it. In the following examples we follow the Doxygen commenting style, but this is not necessary.

###	 Libraries
Before each custom library/package import a comment should be added that describes the library. For example:
```
--! Use misc package for utility functions
USE work.misc.ALL;
```

###	 Entities

Each entity should have a brief and possible a detail description. The brief description should explain the basic function of the component. The detail description should describe its interface, such as special relation between signals, and a more detail view of its functionality. For example:
```
--! This is a reconfigurable multiplier. It can operate in different fixed point precision.

--! The input of the multiplier is a N-bit bit stream. The stream can be treated as a N-bit signed
--! number, two N/2-bit signed numbers, or four N/4-bit signed numbers. The output is a 2N-bit bit stream.
--! The output is splitted in one, two, or four, depending on the configuration.
ENTITY conf_mul_Beh IS
  GENERIC (
    width : NATURAL := 8 --! Input width (N)
  );
  PORT (
    in_a : IN STD_LOGIC_VECTOR(width - 1 DOWNTO 0);       --! Input A, N-bit
    in_b : IN STD_LOGIC_VECTOR(width - 1 DOWNTO 0);       --! Input B, N-bit
    conf : IN STD_LOGIC;                                  --! Configuration bits, define
    prod : OUT STD_LOGIC_VECTOR(2 * width - 1 DOWNTO 0)   --! Product output, 2N-bit
  );
END ENTITY conf_mul_Beh;
```

###	IN/OUT/SIGNALS/VARIABLES/GENERICS
All signals, variables, generics, etc. should include a description comment after its definition, see the code above.

###	Architectures

All architectures should have possible a brief and mandatory a detail description. The descriptions should describe the architectural details of the implementation, for example if it is a one or two process design etc. For example:

```
--! @brief This is the architecture of a reconfigurable multiplier (16-bit)
--! @details In this version we are using a shift and ADD architecture instead of the
--! conventional array multiplier, the multiplication is decomposed in to 4 smaller
--! multipliers. The products of the smaller multiplication are shifted and then added
--! to generate the final result. A sign extension is used to switch between the two modes
ARCHITECTURE rtl OF conf_mul_Beh IS

  CONSTANT width2 : NATURAL := divUp(width, 2); --! Constant value for Width/2
  SIGNAL temp_a1 : signed(width2 - 1 DOWNTO 0); --! MSBs of A
  SIGNAL temp_a0 : signed(width2 DOWNTO 0);     --! LSBs of A

BEGIN
```

###	Processes
Complex processes should have a description of what they are implementing

```
--! This process checks if the loop should be executed (i.e. if  number of iterations >0 )
    zero_loop : PROCESS (instr, config)
    BEGIN

		<CODE BLOCK>

    END PROCESS zero_loop;
```

##	OTHER USEFULL TOOLS/PLUGINS
###	VSCODE:
####	VHDL specific
- https://marketplace.visualstudio.com/items?itemName=rjyoung.vscode-modern-vhdl-support : Highlight, Snippets and some autocompletion.

- https://marketplace.visualstudio.com/items?itemName=Vinrobot.vhdl-formatter : VHDL formatter

####	Language servers:
- https://marketplace.visualstudio.com/items?itemName=hbohlin.vhdl-ls : Good but it has some small issues.

- https://marketplace.visualstudio.com/items?itemName=ViDE-Software.v4pvhdlforprofessionals : Extremely good, free as of now (2021) in beta version but will require a license in the future. Does not work with modern-VHDL plugin.

####	Productivity plugins
- https://marketplace.visualstudio.com/items?itemName=dkundel.vscode-new-file : Advance functions for creating new files.

- https://marketplace.visualstudio.com/items?itemName=bladnman.auto-align : Auto align

- https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow : Colors the indentation.

- https://marketplace.visualstudio.com/items?itemName=adammaras.overtype : Enables overtype using INS button.

- https://marketplace.visualstudio.com/items?itemName=2gua.rainbow-brackets : Colors the brackets

- https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker : Spell checker for your code and comments

####	Shortcut keys and emulation
- https://marketplace.visualstudio.com/items?itemName=hiro-sun.vscode-emacs : Emacs keymap

- https://marketplace.visualstudio.com/items?itemName=vscodevim.vim : Vim emulator
