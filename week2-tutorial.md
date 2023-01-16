# Counting, Sorting, and Redirecting Output 

## Redirect

* ```cmd < file``` Use file as input
* ```cmd > file``` Write output to file
* ```cmd >> file``` Append output to file 

## ```wc``` - Word Count

This command will give you the number of lines, words, and characters in a file. 

Try ```wc numSeq.txt```. 

```wc``` has options for showing only the number of lines ```wc -l```, the number of words ```wc -w```, or the number of characters ```wc -c```. 

## ```tr``` - Translate 

This command can take any individual character and replace it with another character. 

It is a little different than other commands as it requires standard output as the input rather than a filename. To do this, we will use the ```<``` redirect symbol.

Try ```tr '\n' ',' < numSeq.txt```, it means translating new line character ```\n``` to comma ```,``` taking input from file ```numSeq.txt```. 

## ```|``` - Pipe Redirect

It can pass the output of one command to another command.

For example ```cat numSeq.txt | tr '\n' ','``` gets the same output as ```tr '\n' ',' < numSeq.txt```. 

It passes the output of ```cat numSeq.txt``` as input to the command ```tr '\n' ','```. 

## ```shuf``` - Shuffle 

Generate random permutations of lines of the file supplied. 

Try shuffle our file ```numSeq.txt``` then write the output to a new file called ```numSeqRandom.txt``` using the redirect sign ```>```. 

Try shuffle ```numSeq.txt``` another time and append the output to the file ```numSeqRandom.txt``` using redirect sign ```>>```. 

Check if our new file ```numSeqRandom.txt``` has 200 lines by using ```wc -l```. 

## ```sort``` - Sort a File by Lines 

We can use this command to sort the shuffled ```numSeqRandom.txt``` file. 

Try ```shuf numSeqRandom.txt | head```.

Unfortunately it didn't sort numerically as we expected but alphabetically. To sort numerically, use ```-n``` option. 

Now try ```shuf -n numSeqRandom.txt | head``` see what results you got. 

## ```uniq``` - Remove Duplicates of Adjacent Lines (presorted) 

This command will remove duplicates only if the files are sorted first. 

Try below 

```sh
wc -l numSeqRandom.txt
uniq numSeqRandom.txt | wc -l
```

Do we get different results or same results? Why? 

Because we didn't sort the file first, so the ```uniq``` command failed to work. 

Now try 

```sh
wc -l numSeqRandom.txt
sort -n numSeqRandom.txt | uniq | wc -l
```

What results have we got? 

```uniq -c``` can tell you the frequency of each unique line. 

Try ```sort -n numSeqRandom.txt | uniq -c | head```. 

## Exercise

Create a new file and paste the following into it, name it as ```introToBiology.txt```. 

```
Biology is the scientific study of life. It is a natural science with a broad scope 
but has several unifying themes that tie it together as a single, coherent field. 
For instance, all organisms are made up of cells that process hereditary information 
encoded in genes, which can be transmitted to future generations. Another major theme is 
evolution, which explains the unity and diversity of life. Energy processing is also 
important to life as it allows organisms to move, grow, and reproduce. Finally, all organisms 
are able to regulate their own internal environments. 

Biologists are able to study life at multiple levels of organization, from the molecular 
biology of a cell to the anatomy and physiology of plants and animals, and evolution of 
populations. Hence, there are multiple subdisciplines within biology, each defined by the 
nature of their research questions and the tools that they use. Like other scientists, 
biologists use the scientific method to make observations, pose questions, generate hypotheses, 
perform experiments, and form conclusions about the world around them. 
```

What are the 5 most used words in ```introToBiology.txt```. 

```sh
cat introToBiology.txt | tr ' ' '\n' | sort | uniq -c | sort -n | tail -n 5 
```

Try run the above command line step by step for better understanding.

# Find and Replace 

## ```grep``` - Global Regular Expression Print 

This command will find things you specified in a document. 

```sh 
grep "biology" introToBiology.txt
```

To count the frequency of the word found, use

```sh
grep "biology" introToBiology.txt | wc -l
```

If you would like to only print out the occurrence of the word, use

```sh
grep -o "biology" introToBiology.txt 
```

If you would like to ignore the case of the text when finding matches, try

```sh
grep -i "biology" introToBiology.txt 
```

Some other useful parameters:

```sh 
grep -v  # invert matching, select non-matching lines
grep -c  # print only the count of matching lines
grep -n  # prefix the output with line number 
```

## ```history``` - Get the History of Recently Used Commands 

This command gives you the history of the commands you have used.

It is convenient to search commands from history and copy paste to reuse. 

```sh
history | grep introToBiology
history | grep introToBiology | tail -n 10
```

## ```sed``` - Stream Editor 

This command can perform functions on file like searching, find and replace, insertion or deletion. The most common use of SED command in UNIX is for substitution or for find and replace. By using SED you can edit files without opening them, which is much quicker than first opening that file in VI Editor and then changing it. SED command supports regular expression which allows it to perform complex pattern matching.

First, let's create a file for better practice, copy and paste the following to a new file named ```unix-linux.txt```

```
unix is great os. unix is opensource. unix is free os.
learn operating system.
unix linux which one you choose.
unix is easy to learn. unix is a multiuser os. learn unix. unix is powerful.
```

The syntax of ```sed``` is 

```sh
sed 'OPERATION/REGEXP/REPLACEMENT/FLAGS' FILENAME 
```

To find and replace text, use 

```sh
sed 's/unix/linux/g' unix-linux.txt 
sed 's/unix/linux/g' unix-linux.txt > newfile.txt # write output to a new file 
```

To delete a particular line in a file, e.g. the second line. Use 

```sh
sed '2d' unix-linux.txt
```

To delete the pattern matching line

```sh 
sed '/pattern/d' filename.txt # syntax 
sed '/unix/d' unix-linux.txt
```

# Downloading and Transfering Data 

## ```wget``` - Download from the Web 

You can downloan files from the internet using ```wget``` and http/https/ftp addresses. 

For example, try

```sh
wget https://www.gutenberg.org/cache/epub/1513/pg1513.txt # ebook Romeo and Juliet 
wget https://zenodo.org/record/3736457/files/1_control_psbA3_A_2019_minq7.fastq # a sample fastq file 
```

## ```scp``` - Secure Copy 

You can transfer files from remote machine to local machine using ```scp```. 

On your local machine, open Terminal or Command Prompt. Use the code below to download file from Dayhoff to your local machine.

```sh
cd Downloads 
scp u1122333@dayhoff.rsb.anu.edu.au:~/intro-to-linux/romeo-juliet.txt . 
```

If you don't know the hostname of your remote server, you can use ```hostname``` command to find out. 

## ```rsync``` - Remote Sync 

```scp``` can only download files but ```rsync``` can download folders including everything in it. Unfortunately ```rsync``` is not available in Windows Command Prompt. 

Open your Terminal and try 

```sh
cd Downloads 
rsync -a ssh u1122333@dayhoff.rsb.anu.edu.au:~/intro-to-linux .
```

__Please Note:__ do not put ```/``` behind the folder name, otherwise it will download everything in it without the main folder.

# Statements and Loops

A loop is a powerful programming tool that enables you to execute a set of commands repeatedly. 


## ```if``` Statement 

The syntax of ```if``` statement:

```sh
if a-condition
then
    commands 
fi 
```

__EXAMPLE__: The following script will prompt you to input a number and then it checks whether the given number is even. 

Please create a new file called ```if.sh``` and input the script.

```sh
echo -n "Enter Number: "
read x

if [ $((x%2)) == 0 ]
then
    echo "Number is Even"
fi 
```

## ```if``` ... ```else``` statement 

The syntax of the ```if``` ... ```else``` statement

```sh
if a-condition 
then
    commands1
else
    commands2
fi
```

__EXAMPLE__: Let's extend the last example to check if the given number is even or odd. 

```sh
echo -n "Enter Number: "
read x

if [ $((x%2)) == 0 ]
then
    echo "Number is Even"
else
    echo "Number is Odd"
fi 
```

## ```if``` ... ```elif``` ... ```else``` Statement

The syntax:

```sh
if condition1
then
    commands1
elif condition2
then
    commands2
else
    commands3
fi
```

__EXAMPLE__: We can augment the last example to add the addition to check for zero. 

```sh
echo -n "Enter Number: "
read x 

if [ $x == 0 ]
then 
    echo "Number is zero"
elif [ $((x%2)) == 0 ]
then 
    echo "Number is even"
else 
    echo "Number is odd"
fi 
```

## Some Notes on Coding Conditions 

* Ensure white spaces between brackets and the comparison statement, for example the following code will not work, bash will report an error about a missing ```]```

```sh
if [$x==0] # wrong
if [ $x == 0 ] # correct 
```
* To use many conditions in one statement, use logical operators such as logical AND ```&&``` and logical OR ```||```, for example

```sh
if [[ $x -ge $y ]] && [[ $x -ge $z ]] # -ge means greater than or equal to
    echo "x is the greatest"
fi 
```

## ```for``` Loop 

The syntax:

```sh
for variable in a-list-of-things
do
    command1
    command2
    ...
done 
```

A example:

```sh
for i in 1 2 3 4 5
do
    echo "Welcome $i times"
done 
```

Use sequence builder with for loop:

```sh
for i in {1..5}
do 
    echo "Welcome $i times"
done 
```

You can also set up increment value in sequence builder:

```sh
for i in {0..10..2}
do 
    echo "Welcome $i times"
done 
```

## Conditional exit with ```break```

You can take early exit using the ```break``` statement inside for loops, if a condition has been met the for loop will be abandoned. 

The general syntax for using ```break``` inside the for loop would be:

```sh
for i in a-list-of-things
do 
    command1
    command2
    if [ a-condition ]
    then
        break
    fi 
    command3
    command4
done 
```

__EXAMPLE__: Go through all the files stored in the /etc folder, and abandon the loop when the file /etc/resolv.conf is found. 

```sh
for file in /etc/*
do 
    if [ "${file}" == "/etc/resolv.conf" ]
    then
        countNameservers=$(grep -c nameserver /etc/resolv.conf)
        echo "Total ${countNameservers} nameserver defined in ${file}"
        break
    fi
done 
```

```sh 
for i in {1..5}
do 
    echo $i
    if [ $i == 4 ]
    then
        break
    fi 
done
``` 


# References

* Andrew Severin - [Introduction to Unix](https://bioinformaticsworkbook.org/Appendix/Unix/unix-basics-1.html#gsc.tab=0) 
* Wikipedia - [Biology](https://en.wikipedia.org/wiki/Biology) 
* GeeksforGeeks - [Sed Command in Linux/Unix with Examples](https://www.geeksforgeeks.org/sed-command-in-linux-unix-with-examples/) 
* Zenodo - [Sample FASTQ Files](https://zenodo.org/record/3736457#.Y74p_XZByUk) 
* Software Carpentry - [The Unix Shell](https://swcarpentry.github.io/shell-novice/)
* tutorialspoint - [Unix/Linux Shell Loop Types](https://www.tutorialspoint.com/unix/unix-shell-loops.htm#)
* nixCraft - [Bash For Loop Examples](https://www.cyberciti.biz/faq/bash-for-loop/) 
* GeeksforGeeks - [Bash Scripting - If Statement](https://www.geeksforgeeks.org/bash-scripting-if-statement/) 
* GeeksforGeeks - [Bash Scripting - For Loop](https://www.geeksforgeeks.org/bash-scripting-for-loop/) 