# Connect to Dayhoff

* Connecting to the ANU intranet through GlobalProtect VPN. 
* For Windows users, open Command Prompt. For Mac users, open Terminal. 
* Typing in ```ssh u1122333@dayhoff.rsb.anu.edu.au``` and press ```enter```. 
* Typing in ```yes``` if you've been asked to authenticate the connection. 
* The password is the same as your uni account password. 
* If you successfully logged into Dayhoff, you will see a welcome message. 

# Navigating through Directories 

Because there is no graphic user interface in command line, we can imaging the directories as trees to help navigating through. In Linux, the path always start with root ```/```. 

![example](https://docs.oracle.com/cd/E19253-01/806-7612/images/Files.fig154.epsi.gif) 

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

Make sure you are in the directory ```new-folder``` before starting the following excercise. 

## ```touch``` - To Create an Empty File 

Try ```touch notebook0.txt``` and use ```ls``` to see the file you just created. 

Try ```touch notebook{0..9}.txt``` and ```ls``` to see what you have. 

Curly brackets here is an array builder. 

## ```nano``` - A Text Editor 

```nano``` is like any other text editor in your Windows or Mac laptop but to use arrows to navigate through texts. 

__SIDE NOTE__: you can use right click to copy texts and another right click to paste in Terminal or Command Prompt. 

Try ```nano notebook0.txt``` and paste the following texts in and press ```ctrl + x``` and type ```yes``` and press ```enter``` to save the file. 

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

On the bottom side of the ```nano``` interface there are notes of the shortcuts to help you use the text editor. 




# References

* Andrew Severin - [Introduction to Unix](https://bioinformaticsworkbook.org/Appendix/Unix/unix-basics-1.html#gsc.tab=0) 
* Linux.com - [All about {curly braces} in Bash](https://www.linux.com/topic/desktop/all-about-curly-braces-bash/)
