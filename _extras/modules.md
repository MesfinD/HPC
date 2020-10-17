# Environmetal Modules

## Installing Application on Environmental Modules

Assuming that Environmetal Modules is installed in configured correcly, we now show show to install applications on this environment. 
First let us test the the modules are  fucntioning correctly by:

`$ module avail`

{: .bash}

```
------------------------ /usr/share/Modules/modulefiles ------------------------
dot         module-git  module-info modules     null        use.own

```
{: .output}

After installing  our application anyware(e.g in  `/home/software`), let's go to the Modules Home directory by:

```
# mkdir -p $MODULESHOME/gcc/
# vim $MODULESHOME/gcc/.version
```
{: .bash}

```
#%Module1.0
set ModuleVersion "10.2"
```
{: .output}


```
# vi $MODULESHOME/gcc/10.2
```
{: .bash }

```
#%Module1.0
proc ModulesHelp { } {
global dotversion

puts stderr "\tGCC 10.2 (gcc, g++, gfortran)"
}

module-whatis "GCC 10.2 (gcc, g++, gfortran)"
conflict gcc
prepend-path PATH /packages/gcc/10.2/bin
prepend-path LD_LIBRARY_PATH /home/software/gcc/10.2/lib64
prepend-path LIBRARY_PATH /home/software/gcc/10.2/lib64
prepend-path MANPATH /home/software/gcc/10.2/man
setenv CC gcc
setenv CXX g++
setenv FC gfortran
setenv F77 gfortran
setenv F90 gfortran
```
{: .output}

