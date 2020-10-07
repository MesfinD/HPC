---
title: "Transferring files"
teaching: 20
exercises: 10
questions:
- "How do I upload/download files to/from a remote HPC system?"
objectives:
- "Be able to tranfer files to and from a remote HPC system."
keypoints:
- "`wget` downloads a file from the internet."
- "`sftp`/`scp` transfer files to and from your computer."
- "You can use an SFTP client like FileZilla to transfer files through a GUI."
---

One thing people very frequently struggle with is transferring files 
to and from a remote system.
We'll cover several methods of doing this from the command line,
then cover how to do this using the GUI program FileZilla. The method you 
choose will be decided by what is most covenient for your workflow.

## Grabbing files from the internet

To download files from the internet, 
the easiest tool to use is `wget`.
The syntax is relatively straightforward: `wget https://some/link/to/a/file.tar.gz`

```
[mesfind@mgmt01 ~]$ wget https://mesfind.github.io/hpc/files/cfd.tar.gz
```
{: .bash}
```
--2018-07-17 11:44:44--  https://epcced.github.io/hpc-intro/files/cfd.tar.gz
Resolving epcced.github.io (epcced.github.io)... 185.199.110.153, 185.199.111.153, 185.199.109.153, ...
Connecting to epcced.github.io (epcced.github.io)|185.199.110.153|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 20480 (20K) [application/gzip]
Saving to: ‘cfd.tar.gz’

100%[===========================================================================================================>] 20,480      --.-K/s   in 0.01s   

2018-07-17 11:44:44 (2.05 MB/s) - ‘cfd.tar.gz’ saved [20480/20480]

```
{: .output}

## Transferring single files and folders with scp

To copy a single file to or from the remote system, we can use `scp`.
The syntax can be a little complex for new users, 
but we'll break it down here:

To transfer *to* another computer:
```
[mesfind@mgmt01 ~]$ scp /path/to/local/file.txt yourUsername@remote.computer.address:/path/on/remote/computer
```
{: .bash}

To download *from* another computer:
```
[mesfind@mgmt01 ~]$ scp yourUsername@remote.computer.address:/path/on/remote/computer/file.txt /path/to/local/copy
```
{: .bash}

Note that we can simplify doing this by shortening our paths.
On the remote computer, everything after the `:` is relative to our home directory.
We can simply just add a `:` and leave it at that if we don't care where the file goes.

```
[mesfind@mgmt01 ~]$ scp local-file.txt yourUsername@remote.computer.address:
```
{: .bash}

To recursively copy a directory, we just add the `-r` (recursive) flag:

```
[mesfind@mgmt01 ~]$ scp -r some-local-folder/ yourUsername@remote.computer.address:target-directory/
```
{: .bash}

## Transferring files interactively with sftp

`scp` is useful, but what if we don't know the exact location of what we want to transfer?
Or perhaps we're simply not sure which files we want to tranfer yet.
`sftp` is an interactive way of downloading and uploading files.
Let's connect to a remote system using `sftp`, you'll notice it works the same way as SSH:

```
[mesfind@mgmt01 ~]$ sftp yourUsername@10.4.17.30
```
{: .bash}

This will start what appears to be a bash shell (though our prompt says `sftp>`).
However we only have access to a limited number of commands.
We can see which commands are available with `help`:

```
sftp> help
```
{: .bash}
```
Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit                               Quit sftp
get [-afPpRr] remote [local]       Download file
reget [-fPpRr] remote [local]      Resume download file
reput [-fPpRr] [local] remote      Resume upload file
help                               Display this help text
lcd path                           Change local directory to 'path'
lls [ls-options [path]]            Display local directory listing
lmkdir path                        Create local directory
ln [-s] oldpath newpath            Link remote file (-s for symlink)
lpwd                               Print local working directory
ls [-1afhlnrSt] [path]             Display remote directory listing

# omitted further output for clarity
```
{: .output}

Notice the presence of multiple commands that make mention of local and remote.
We are actually connected to two computers at once (with two working directories!).

To show our remote working directory:
```
sftp> pwd
```
{: .bash}
```
Remote working directory: /lustre/home/y15/yourUsername
```
{: .output}

To show our local working directory, we add an `l` in front of the command:

```
sftp> lpwd
```
{: .bash}
```
Local working directory: /Users/auser/carpentry
```
{: .output}

The same pattern follows for all other commands:

* `ls` shows the contents of our remote directory, while `lls` shows our local directory contents.
* `cd` changes the remote directory, `lcd` changes the local one.

To upload a file, we type `put some-file.txt`

```
sftp> put input.dat
```
{: .bash}
```
Uploading input.dat to /lustre/home/y15/yourUsername/input.dat
input.dat                                   100% 2000KB  11.0MB/s   00:00
```
{: .output}

To download a file we type `get some-file.txt`:

```
sftp> get input.dat
```
{: .bash}
```
Fetching /lustre/home/y15/yourUsername/input.dat to input.dat
/lustre/home/y15/yourUsername/input.dat                               100% 2000KB  11.0MB/s   00:00
```
{: .output}

And we can recursively put/get files by just adding `-r`.
Note that the directory needs to be present beforehand.

```
sftp> mkdir data
sftp> put -r data/
```
{: .bash}
```
Uploading content/ to /lustre/home/y15/yourUsername/data
Entering content/
ntering data/
data/data11.dat                                 100%  100KB   8.4MB/s   00:00    
data/data10.dat                                 100%  100KB   8.8MB/s   00:00    
data/data12.dat                                 100%  100KB   8.5MB/s   00:00    
data/data13.dat                                 100%  100KB   8.7MB/s   00:00    
data/data17.dat                                 100%  100KB   8.8MB/s   00:00    
data/data16.dat                                 100%  100KB   8.4MB/s   00:00    
data/data28.dat                                 100%  100KB   8.7MB/s   00:00    
data/data14.dat                                 100%  100KB   8.5MB/s   00:00    
data/data15.dat                                 100%  100KB   8.7MB/s   00:00    
data/data29.dat                                 100%  100KB   4.7MB/s   00:00    
data/data9.dat                                  100%  100KB   8.2MB/s   00:00    
data/data8.dat                                  100%  100KB   8.6MB/s   00:00    
data/data6.dat                                  100%  100KB   8.8MB/s   00:00    
data/data7.dat                                  100%  100KB   8.8MB/s   00:00    
data/data5.dat                                  100%  100KB   8.6MB/s   00:00    
data/data4.dat                                  100%  100KB   8.5MB/s   00:00    
data/data0.dat                                  100%  100KB   8.6MB/s   00:00    
data/data1.dat                                  100%  100KB   8.6MB/s   00:00    
data/data3.dat                                  100%  100KB   8.7MB/s   00:00    
data/data2.dat                                  100%  100KB   8.6MB/s   00:00    
data/data24.dat                                 100%  100KB   8.5MB/s   00:00    
data/data18.dat                                 100%  100KB   8.4MB/s   00:00    
data/data19.dat                                 100%  100KB   6.2MB/s   00:00    
data/data25.dat                                 100%  100KB   7.3MB/s   00:00    
data/data27.dat                                 100%  100KB   8.7MB/s   00:00    
data/data26.dat                                 100%  100KB   6.2MB/s   00:00    
data/data22.dat                                 100%  100KB   6.3MB/s   00:00    
data/data23.dat                                 100%  100KB   7.9MB/s   00:00    
data/data21.dat                                 100%  100KB   8.0MB/s   00:00    
data/data20.dat                                 100%  100KB   7.8MB/s   00:00 
```
{: .output}

To quit, we type `exit` or `bye`. 

## Transferring files interactively with FileZilla (sftp)

FileZilla is a cross-platform client for downloading and uploading files to and from a remote computer.
It is absolutely fool-proof(!) and always works quite well.
In fact, it uses the exact same protocol as `sftp` under the hood.
If `sftp` works, so will FileZilla!

Download and install the FileZilla client from [https://filezilla-project.org](https://filezilla-project.org).
After installing and opening the program, 
you should end up with a window with a file browser of your local system 
on the left hand side of the screen.
When you connect to the cluster, your cluster files will appear on the right hand side.

To connect to the cluster, 
we'll just need to enter our credentials at the top of the screen:

* Host: `sftp://hpc.ethernet.edu.et
* User: Your username
* Password: Your password
* Port: (leave blank to use the default port)

Hit "Quickconnect" to connect!
You should see your remote files appear on the right hand side of the screen.
You can drag-and-drop files between the left (local) and right (remote) sides 
of the screen to transfer files.

> ## Transferring files
> Using the above methods, try transferring files to and from the cluster.
> Which method do you like the best and why? Can you think of any potential issues
> with your preferred method?
{: .challenge}

## Transferring data efficiently

If we are transferring large amounts of data then there are some additional things we have to take
into account in terms of performance and being considerate to other users on the system. We will
talk about these later in the lesson...

## Some additional notes...

> ## Working with Windows
> When you transfer text files to from a Windows system to a Unix system 
> (Mac, Linux, BSD, Solaris, etc.) this can cause problems.
> Windows encodes its text files slightly different than Unix,
> and adds an extra character to every line.
> 
> On a Unix system, every line in a file ends with a `\n` (newline).
> On Windows, every line in a file ends with a `\r\n` (carriage return + newline).
> This causes problems sometimes.
> 
> Though most modern programming languages and software handle this correctly,
> in some instances, you may run into an issue.
> The solution is to convert a file from Windows to Unix encoding with the `dos2unix` command.
> 
> You can identify if a file has Windows line endings with `cat -A filename`.
> A file with Windows line endings will have `^M$` at the end of every line.
> A file with Unix line endings will have `$` at the end of a line.
> 
> To convert the file, just run `dos2unix filename`.
> (Conversely, to convert back to Windows format, you can run `unix2dos filename`.)
{: .callout}

> ## A note on ports
> All file tranfers using the above methods use encrypted communication over port 22.
> This is the same connection method used by SSH.
> In fact, all file transfers using these methods occur through an SSH connection.
> If you can connect via SSH over the normal port, you will be able to transfer files.
{: .callout}

