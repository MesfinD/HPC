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
$ cd $MODUELSHOME
$ mkdir -p gcc/10.2
$ cp modulesfile/modules gcc/10.2/
```
{: .bash}


