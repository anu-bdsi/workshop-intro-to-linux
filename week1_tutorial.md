# Connecting to Dayhoff

* Connecting to the ANU intranet through GlobalProtect VPN. 
* Open Terminal (Windows system should have this as well, otherwise open Command Prompt). 
* Typing in ```ssh u_id@dayhoff.rsb.anu.edu.au``` and press ```enter```. 
* Typing in ```yes``` if you've been asked to authenticate the connection. 
* The password is the same as your uni account password. 
* If you successfully logged into Dayhoff, you will see a welcome message. 

# Navigating through Directories 

## The General Syntax of a Shell Command 

Before we start to learn any command, let's talk about the interface of the Linux operating system. The interface is just a window with texts displaying. The actual place where we communicate with the computer is a line with some information at the front and a ```$``` sign, after the ```$``` sign is where we can type things in. It is something like this:

![linux-interface](https://ubuntucommunity.s3.dualstack.us-east-2.amazonaws.com/original/2X/b/ba76cbf3dc8dc2cc94d26dd61c7aad3cedcd5102.png)

The texts before the ```@``` sign is usually the user name, and behind the ```@``` is the server name. The texts after the ```:``` sign and before the ```$``` sign is the directory you are in. In the above image, it is ```~``` which means the home directory of the user. We will talk about it later in the course as well. 

When we type a command, there are some rules to follow. Please see the following image for what a standard command looks like:

![shell-syntax](https://swcarpentry.github.io/shell-novice/fig/shell_command_syntax.svg)

* The ```$``` is called prompt.
* Following the prompt the first thing we should type in is a command, here this command ```ls``` means to list all the files/directories. 
* After the command, sometimes an option/flag follows it to add some other functions for the command, here ```-F``` option means to modify the output of command ```ls``` by adding some symbols after the file/directory names to show what type of the file is. It might be confusing, but don't worry, as we go along with the course, you'll understand more. 
* The last thing is called an argument, it means the thing/place where we want our command to perform on. 

Options and arguments are optional, for a command it is not necessary to have an option or argument. Some commands can be used just by itself. It is also possible for a command to have more than one option and one argument. A ```space``` is needed between when typing the command, option, and argument.

## File Path on a Linux Machine 

Because there is no graphic user interface in command line, we can imagine the directories as trees to help navigating through. In Linux, the path always start with root ```/```. 

![image](https://cdn-wordpress-info.futurelearn.com/info/wp-content/uploads/a2794f8f-b0c1-468d-89c6-bcf29d2d6517-1.png) 

## ```pwd``` - Path of Working Directory 

This command tells you where you are. 

When you logged into a Linux system, the default location you will be is your home directory. It is usually showed as a ```~``` sign before your command prompt, the ```~``` is equivalent to the path to your home directory. 

Let's try use the command ```pwd``` to get where we are. The expected result would be ```/home/u_id```. 

## ```ls``` - Listing 

This command lists all the things in your current directory. 

For now, ```ls``` returns nothing because we are new users and don't have anything in our home directory. We can also put the path to a certain directory behind ```ls``` to inspect the contents of the directory such as ```ls /```, ```ls /home```, or ```ls /usr```. 

```ls``` has many useful options to inspect our files with more details, I'll list some below and we'll practice it during the course:

* ```ls -l``` list with long format - show permissions
* ```ls -a``` list all files including hidden files starting with "."
* ```ls -lh``` show file sizes in KB/MB/GB format

## ```cd``` - Change Directory 

This command lets you move between directories. 

Let's explore the directories on Dayhoff a little bit. First, let's go to the root directory using ```cd /``` and use ```ls``` to see what directories do we have under the root directory. Next, let't go to the ```home``` directory using ```cd home``` and use ```ls``` to see what directories we have under home directory. Ideally, you can find your uni id in it. Then you can use ```cd u_id``` to go to your home directory. 

If you want to go to the last directory (or parent directory) of your current directory, you can use ```cd ..```. ```..``` means the parent directory, you can also use it in the file path. But when you use ```..``` in the file path, it would be called a relative file path because it is relative to your current directory. If you are not in this same directory anymore, the file path will no longer work. 

## ```mkdir``` - Make Directory 

This command lets you create a new directory.

Let's create a new directory in our home directory, the new directory will be the main directory we're working in for this course. Let's name it as ```intro_to_linux```. 

```sh
cd ~ # go to our home directory 
mkdir intro_to_linux # and make a new directory in your current directory 
ls # to see the result 
```

### There are some rules when naming your files/directories in Linux:

* A file or directory name can have 1 to 256 characters in length. 
* Linux is case sensitive, uppercase characters are different from lowercase characters. 
* File or directory names can include letters, numbers, and the following 3 special charachers: period ```.```, underscore ```_```, and dash ```-```. 
* There are some other special characters you can use in the name but it is highly against, so I won't be mention them here. 

### Linux provides 2 wildcard characters to represent ambiguous characters or strings in file or directory names:

* The question mark ```?``` represents any single character. For example, ```ls file?.txt``` would list ```file1.txt``` and ```file2.txt``` but not ```file50.txt```. 
* The asterisk ```*``` represents any string of zero or more characters. For example, ```ls file*.txt``` would list ```file.txt```, ```file1.txt```, ```file2.txt```, and ```file50.txt``` but not ```file01.data```. 

# Creating and Editing Files 

## ```touch``` - To Create Empty Files 

Try ```touch new_file.txt```, and use ```ls``` to view the result. 

```touch``` can take multiple input in to create several files together. Try ```touch doc1.txt doc2.txt doc3.txt```, and use ```ls``` to view the results. 

The ```touch``` command has another funtion which is to change the timestamp of a file, but we won't cover it here. 

## Copy and Paste in Linux

The copy and paste shortcuts are not the same as Windows, in Linux we use ```right click``` to copy and paste text. In Linux, ```ctrl + c``` means to terminate the current process so we should be cautious using it. 

Most of the time, we're using a terminal to connect to a server. In this case, you can still use ```ctrl + c``` in other applications (for example in the browser) to copy texts but to use ```right click``` to paste it into your terminal. Vice versa, you can also use ```right click``` to copy texts in the terminal and use ```ctrl + v``` to paste into other applications. 

## Mouse does not work in Linux

Mouse doesn't work as the same anymore, we need to use the arrow keys on the keyboard to move the insertion point. 

If you're using a terminal on a Windows/MacOS laptop, you can still use mouse to select texts only if you want to copy it. You can't do select and delete using mouse. 

## ```nano``` - A Text Editor 

```nano``` is an easy to use command line text editor for Unix and Linux operating systems. It includes all the basic functionality you'd expect from a regular text editor. 

To open an existing file or to create a new file, type ```nano``` and the file name behind:

```sh
nano new_file.txt 
```

This opens a new window, and you can start editing the file. 

At the bottom of the window, there is a list of the most basic command shortcuts to use with the nano editor. All commands are prefixed with either ```^``` or ```M``` character. The caret symbol ```^``` represents the ```ctrl``` key. For example, the ```^J``` command means to press the ```ctrl``` and ```J``` key at the same time. The letter ```M``` represents the ```alt``` key. You can get a list of commands by typing ```ctrl + g```. 

Now, try copy the following poem and paste it into the editor:

* ```ctrl + c``` to copy the texts from the browser and ```right click``` to paste it into terminal. 
* Press ```^X``` to exit the file. 
* You'll be asked "Save modified butter?", we should press ```y``` for yes. 
* Then you'll be asked "File name to write: new_file.txt", press ```enter``` to accept the name or input other names then press ```enter```. 

```
I taste a liquor never brewed – 
From Tankards scooped in Pearl – 
Not all the Frankfort Berries
Yield such an Alcohol!

Inebriate of air – am I – 
And Debauchee of Dew – 
Reeling – thro' endless summer days – 
From inns of molten Blue – 

When "Landlords" turn the drunken Bee
Out of the Foxglove's door – 
When Butterflies – renounce their "drams" – 
I shall but drink the more!

Till Seraphs swing their snowy Hats – 
And Saints – to windows run – 
To see the little Tippler
Leaning against the – Sun!
```

## ```vim``` - Another Text Editor 

```vim``` is an advanced and highly configurable text editor built to enable efficient text editing. It supports most file types and vim editor is also known as a programmer's editor. 

There are 2 operating modes in vim editor:

* Command mode: By default, command mode is on as soon as the vim editor is started. This command mode helps users to copy, paste, delete, or move text. We should press ```esc``` key to go to command mode if we're in other modes.
* Insert mode: Whenever we try to open vim editor, it will go to command mode by default. To write the contents in the file, we must go to insert mode. Press ```i``` to go to insert mode. If we want to go back to command mode, press the ```esc``` key. 

To create a new file and open the vim editor, run:

```sh 
vim poem.txt 
```

Copy and paste the following poem into the file:

* Press ```i``` to go into the insert mode.
* Right click to paste the texts into the editor.
* Press ```esc``` key to go back to the command mode.
* Type ```:wq``` to save and exit the file. 

```
The readers of the Boston Evening Transcript
Sway in the wind like a field of ripe corn.

When evening quickens faintly in the street,
Wakening the appetites of life in some
And to others bringing the Boston Evening Transcript,
I mount the steps and ring the bell, turning
Wearily, as one would turn to nod good-bye to Rochefoucauld,
If the street were time and he at the end of the street,
And I say, "Cousin Harriet, here is the Boston Evening Transcript.
```

# Viewing File Contents 

First, let's create a file that contains numbers from 1 to 100. Then we can practice the different ways to view a file in linux. 

```sh 
seq 1 1 100 > numSeq.txt
```

* ```seq``` is a command to generate a sequence of numbers. The basic syntax is ```seq FIRST INCREMENT LAST```. So ```seq 1 1 100``` means generate a sequence of numbers from 1 to 100 with increment is 1.
* ```>``` is a shell operator that redirects the standard output of a command to a file. In here, it means to redirect the output of ```seq 1 1 100``` to a file named ```numSeq.txt```. 

## ```cat``` - Concatenate and Print Files 

```cat``` is a command line that concatenates and displays the contents of one or more files. It is oftened used for simple file manipulation tasks, such as displaying the contents of a file, combining multiple files into one, or creating new files. 

Here are a few examples of how the ```cat``` command can be used:

* ```cat file.txt``` display the contents of a file.
* ```cat file1.txt file2.txt``` concatenate two files and display the result.
* ```cat -n file.txt``` display the contents with line numbers.
* ```cat > newfile.txt``` create a new file by redirecting the output of cat. 

__Excercise__: 

1. Display the contents of ```new_file.txt```, ```poem.txt```, and ```numSeq.txt``` separately.
2. Concatenate the two files ```new_file.txt``` and ```poem.txt``` together, and display it. 
3. Display the ```poem.txt``` file with line numbers. 
4. Concatenate the two files ```new_file.txt``` and ```poem.txt``` together, and create it as a new file called ```poems.txt```. 

## ```head``` and ```tail``` - Print out the Head or Tail of the File 

The ```head``` command is used to display the first part of a file, while the ```tail``` command is used to display the last part of a file. 

The basic syntax of the head/tail command is:

```sh 
head [-options] [file_name] 
tail [-options] [file_name] 
```

Let's try them on ```numSeq.txt```:

```sh
head numSeq.txt
tail numSeq.txt
```

By default, the head/tail command displays the first/last 10 lines of a file. We can specify how many lines we want to display by using the option ```-n```. For example:

```sh
head -n 5 numSeq.txt # display the first 5 lines of the file numSeq.txt 
tail -n 20 numSeq.txt # diaplay the last 20 lines of the file numSeq.txt 
```

## ```more``` and ```less``` - Viewing a File  

```more``` command is used to view the text files, displaying one screen at a time in case the file is large. The more command also allows the user to scroll up and down through tha page. Another application of more is to use it with pipe, when the output is large, we can use more command to see output page by page. 

The basic syntax of more is:

```sh
more [-options] [-num] [+/pattern] [file_name]
```

* ```-options```: any option you want to use in order to change the way that a file was displayed.
* ```-num```: the number of lines you want to display per screen. 
* ```+/pattern```: 


```more``` allows you to go through a file page by page, use ```space``` to move to the next page. Hit ```q``` to quit.

```more``` does not allow you to go back to a previous page, but ```less``` does. 

```less``` has more functions. 
* Hit ```space``` or ```d``` to go down one page
* ```u``` to go up one page
* ```g``` plus number let you jump to a certain line
* up and down arrows let you go up or down one line 
* ```q``` to quit 

# Moving, Copying, Deleting, Renaming Files and Directories 

## ```rmdir``` - Remove Directory 

Can only delete empty directory. 

We have mentioned how to create new directory before, the command ```mkdir```. Please try creating a new directory and delete it. Remember to use ```ls``` to check between steps. 

Now, create another new directory ```deleteMe``` and create a new text file ```deleteMe.txt``` in it. And then try to delete it. What happens? __HINT__: use ```touch```, ```nano```, or ```vim``` to create a new file. 

If a folder is not empty, we can use ```rm``` command to delete it. 

## ```rm``` - Remove Files and Directories 

__NOTE__: be careful deleting files in Linux because there is no Bin like Windows or Mac to restore files. 

```rm filename``` to remove files. Try to remove the 2 poems we created before. 

```rm -r directoryName``` to remove directory with files in it. Try to remove the folder ```deleteMe```. 

## ```mv``` - Move Files and Rename Files 

Try create two new directories called ```numbers``` and ```letters```, and move the txt files into each directory according to their names. 

You can move multiple files to a directory at once, such as:

```sh
mv 1.txt 2.txt 3.txt numbers
```

To rename files, simply do: 

```sh
mv old-filename new-filename 
```

Try to rename the file ```GG.txt``` to ```FF.txt```. 

## ```cp``` - Copy

```sh
cp filename copied-filename 
```

Unlike Windows and Mac, Linux does not automatically give your copied file a name if there is already a file with the same name. Making sure to name the copied file correctly. 

Try to copy the file ```numSeq.txt``` to a new file called ```num1-100.txt```. 

To copy a directory, add the parameter ```-r```: 

```sh
cp -r numbers numbers-copy
```

Try the copy ```letters``` directory. 

# Other Useful Information of Linux 



## Get Help with a Command

If there is a new command that you are not familiar with, you can always use ```--help``` option to get help. 

Such as:

```sh
ls --help 
```

```ls``` is probably the most used command in Linux, there are some useful options allowing us to see more information about the files. Try the commands below. 

```sh
ls -l   # list with long format - show permissions. 
ls -a	# list all files including hidden files starting with '.'.
ls -la  # list all files with long format 
ls -lh	# this command will show you the file sizes in human readable format.
```

## How to read the file information

![file-information](https://camo.githubusercontent.com/9fcde9faba729c5d2658204ef8df30a1fe714b7297cf4f0e7c4e8389da0d5a0e/68747470733a2f2f692e737461636b2e696d6775722e636f6d2f49763553522e706e67) 

![file-permissions](https://linuxcommand.org/images/file_permissions.png) 

## Absolute vs Relative Path 

The absolute path is always the same no matter which directory you're in. 

The relative path is different based on the current working directory you're in. 

__NOTE__: ```..``` means the parent directory and ```.``` means the current directory. 

For example, now we're in the folder ```/home/mary```. The absolute path to ```robert``` is ```/home/robert```, and the relative path to ```robert``` is ```../robert```. 

![directory-diagram](https://cdn-wordpress-info.futurelearn.com/info/wp-content/uploads/a2794f8f-b0c1-468d-89c6-bcf29d2d6517-1.png) 

### Questions

* What is the absolute path to ```man```? 
* What is the relative path to ```man``` from ```/usr/lib```? 
* What is the relative path to ```man``` from ```/usr/local/lib```? 

## Shortcuts

* ```tab``` key to complete file and directory names
* ```↑``` key to get the previous command, and the previous command of the previous command
* ```ctrl + c``` to kill process
* ```clear``` command to clear terminal
* ```exit``` command to exit from the current shell 

## Wild Card

```*``` is a wild card, it matches zero or more characters. 

Let's create some empty files first.

```sh
touch note0.txt note1.txt note2.txt notebook.txt 
```

* ```ls *.txt``` lists every file ends with ```.txt```. 
* ```ls note*``` lists every file starts with ```note```.

```?``` is also a wild card, it matches one character. 

* Try ```ls note?.txt``` and ```ls note*.txt```, and see what's the difference?  

# Homework 

Do some research on below questions to get a better understanding of the Linux operating system. 

* What is Linux?
* What is Unix?
* What are the differences and relationships between Linux and Unix?
* What are the differences and relationships between Linux and Windows/MacOS?
* What is Shell?
* What is Bash? 
* What are the differences and relationships between Shell and Bash? 
* Why people use Linux and what are the advantages? 
* What types of computer use Linux as operating systems? 
* What are the popular Linux distributions on the market? 
* Why do we need to learn Linux as a biologist? 

# References

* Andrew Severin - [Introduction to Unix](https://bioinformaticsworkbook.org/Appendix/Unix/unix-basics-1.html#gsc.tab=0) 
* GeeksforGeeks - [Getting Started with Vim Editor in Linux](https://www.geeksforgeeks.org/getting-started-with-vim-editor-in-linux/) 
* Software Carpentry - [The Unix Shell](https://swcarpentry.github.io/shell-novice/) 
* University of Calgary - [Managing Linux Directories and Files](https://people.ucalgary.ca/~appinst/linux/fileManage.html#:~:text=A%20file%20or%20directory%20name,%2C%20and%20dash%20(%20%2D%20).) 
* GeeksforGeeks - [touch command in Linux with Examples](https://www.geeksforgeeks.org/touch-command-in-linux-with-examples/) 
* atatus - [Linux "ls" Command with Examples](https://www.atatus.com/blog/ls-command-in-linux-with-example/#:~:text=In%20Linux%2C%20the%20command%20%22ls,as%20well%20as%20system%20administrators.) 
* Linuxize - [How to Use Nano, the Linux Command Line Text Editor](https://linuxize.com/post/how-to-use-nano-text-editor/) 
* EDUCBA - [Vim Command in Linux](https://www.educba.com/vim-command-in-linux/) 
* Baeldung - [The head and tail commands in LINUX](https://www.baeldung.com/linux/head-tail-commands) 