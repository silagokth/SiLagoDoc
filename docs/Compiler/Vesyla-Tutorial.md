# Vesyla tutorial

## Working environment

- **OS**: Windows
- **Software**:
    - Matlab
    - QuestaSim

## Write the matlab code

Please Check full documentation of Vesyla (chapter 3) : [Vesyla user manual](Vesyla-Tutorial/vesyla_user_manual.pdf)

## Compilation and simulation

### File structure

![File structure](Vesyla-Tutorial/file_structure.png)

### Compilation

1. Copy your matlab script into folder *$AlgoSilDir/test/testcases/*.
2. Edit *$AlgoSilDir/make/make_test.bat* and change the **filename** section as following.
```tcl
set file_name = ^
		<YOUR SCRIPT FILENAME WITHOUT SUFFIX>
```
3. Open **powershell**, change to *$AlgoSilDir/make/* directory and execute the *make_test.bat* script.
```tcl
cd $AlgoSilDir\make
.\make_test.exe
```
4. Wait for compilation and simulation. The simulation result (**pass** or **fail**) will be reported at the end of the process.
5. The report can be found in folder *$AlgoSilDir/test/testcases/<YOUR SCRIPT FILENAME WITHOUT SUFFIX>*.

### Output files

Please Check full documentation of Vesyla (section 4.3): [Vesyla user manual](Vesyla-Tutorial/vesyla_user_manual.pdf)
