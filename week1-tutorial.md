# Connect to Dayhoff

* Connecting to the ANU intranet through GlobalProtect VPN. 
* For Windows users, open Command Prompt. For Mac users, open Terminal. 
* Typing in ```ssh u1122333@dayhoff.rsb.anu.edu.au``` and press ```enter```. 
* Typing in ```yes``` if you've been asked to authenticate the connection. 
* The password is the same as your uni account password. 
* If you successfully logged into Dayhoff, you will see a welcome message. 

# Navigating through Directories 

Because there is no graphic user interface in command line, we can imaging the directories as trees to help navigating through. In Linux, the path always start with root ```/```. 

![image](https://docs.oracle.com/cd/E19253-01/806-7612/images/Files.fig154.epsi.gif) 

## ```pwd``` - Path of Working Directory 

This command tells you where you are. 

When you logged into a Linux system, the default location you will be is your home directory ```~```. But there is no directory called ```~```, it is equivalent to the path to your home directory which you can get by using the command ```pwd```. 

For example, ```/home/u1122333```.

## ```ls``` - Listing 

This command lists all files and directories under your current directory. 

For now, ```ls``` returns nothing because we are new users and don't have anything under our home directory. 

We can also put the path to a certain directory behind ```ls``` to inspect the contents of the directory such as ```ls /home```. 

## ```mkdir``` - Make Directory 

This command lets you create a new directory.

```mkdir new-folder``` to create a new directory called ```new-folder``` under your working directory.

```mkdir /home/u1122333/new-folder``` to create a new directory under ```/home/u1122333``` regardless of where you are. 

## ```cd``` - Change Directory 

This command lets you move between directories. 

```cd new-folder``` to go into the new directory you just created. 

```pwd``` will return your current working directory which is ```/home/u1122333/new-folder```. 

To go back to the previous directory we can use either ```cd /home/u1122333``` or ```cd ..``` command. 

```..``` here means parent directory, and ```.``` means current directory. 

# Creating and Editing Files 

## ```nano``` - A Text Editor 

```nano``` is like any other text editor in your Windows or Mac laptop but to use arrows to navigate through texts. 

Try ```nano poem-0.txt``` and paste the following texts in and press ```ctrl + x``` and type ```yes``` and press ```enter``` to save the file. 

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

On the bottom of the ```nano``` interface there are notes of shortcuts to help you use the text editor. 

__SIDE NOTE__: you can use right click to copy texts and another right click to paste in Terminal/Command Prompt.

## ```vim``` - Another Text Editor 

```vim``` is another text editor in Linux, it has different ways to edit and save files.

```vim poem-1.txt``` to create a file and go into the ```vim``` interface. Press ```i``` to start the insert mode, and copy and paste the following poem to the file. 

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

Press ```esc``` to exit the insert mode, then type ```:wq``` and hit ```enter``` to save the file and exit the editor. If you want to quit without saving, type ```:q``` and hit ```enter```. 

# Viewing File Contents 

First try 

```sh 
seq 1 1 100 > numSeq.txt
``` 

to create a file named ```numSeq.txt``` contains numbers from 1 to 100. 

## ```cat``` - Concatenate and Print Files 

```cat filename``` prints out everything in the file on the screen. 

Try print out the two poems we saved. 

Some files can be very long and we wouldn't want to print out everything on the screen. In this case, we can use other commands to inspect the content of the file. 

## ```head``` and ```tail``` - Print out the Head or Tail of the File 

These two commands print out the first/last 10 lines of the file. 

Use ```-n``` to choose how many lines you would like to display, for example ```head -n 5 numSeq.txt``` to see the first 5 lines of file ```numSeq.txt```. 

## ```more``` and ```less``` - Step through a File 

```more``` allows you to go through a file page by page, use ```space``` to move to the next page. Hit ```q``` to quit.

```more``` does not allow you to go back to a previous page, but ```less``` does. 

```less``` has more functions. 
* Hit ```space``` or ```d``` to go down one page
* ```u``` to go up one page
* ```g``` plus number let you jump to a certain line
* up and down arrows let you go up or down one line 
* ```q``` to quit 

# Moving, Copying, Deleting, Renaming Files and Directories 

## ```touch``` - To Create Empty Files 

Try commands below. 

```sh 
touch AA.txt
touch BB.txt
touch CC.txt
touch 1.txt
touch 2.txt
touch 3.txt 
```

And use ```ls``` to see what files do you have. 

It can also be written in this way:

```sh
touch DD.txt EE.txt GG.txt 4.txt 5.txt 6.txt 
```

Try ```ls``` to see the result. 

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

# References

* Andrew Severin - [Introduction to Unix](https://bioinformaticsworkbook.org/Appendix/Unix/unix-basics-1.html#gsc.tab=0) 
* GeeksforGeeks - [Getting Started with Vim Editor in Linux](https://www.geeksforgeeks.org/getting-started-with-vim-editor-in-linux/) 
