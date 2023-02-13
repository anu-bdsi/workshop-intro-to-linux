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

### Simple For Loop 

```sh
for i in a b c
do 
    echo $i
done 
```

The above code will iterate over the specified elements after the ```in``` keyword one by one. The elements can be numbers, strings, or other forms of data. 

### Range-based For Loop

In this type of for loop, we can specify the number to start, to stop, and to increment at every iteration. 

The syntax looks like this:

```sh
for i in {1..5} # sequence builder {}
do 
    echo $i
done 
```

We can also add increment:

```sh
for i in {1..5..2} # increse by two 
do 
    echo $i
done 
```

### Array Iteration For Loop

We can iterate over arrays conveniently in bash using for loops with a specific syntax. In Bash, the "@" symbol is used as a shorthand for referencing elements of an array. 

For example, when you have an array called fruits:

```sh
fruits=("apple" "banana" "cherry")
```

You can reference all elements of the array by using "@" like this:

```sh
for i in "${fruits[@]}"
do 
    echo $i
done 
```

## ```break``` and ```continue``` Keywords

Both "break" and "continue" are used to transfer control of the program to another part of the program. It is used within loops to alter the flow of the loop and terminate the loop or skip the current iteration. 

### ```break``` 

The break statement is used to terminate the loop and can be used within a while, for, until, and select loops. 

```sh
for i in {1..5}
do 
    if (( $i == 3))
    then
        break
    fi 
    echo $i 
done 
```

It can be seen that when the value of i is 3, the loop execution is terminated and hence i is only printed up to 2.

### ```continue``` 

Continue is a command which is used to skip the remaining command inside the loop for the current iteration in for, while, and until loop. 

```sh
for i in {1..5}
do 
    if (( $i == 3))
    then 
        continue
    fi 
    echo $i
done 
```

__Please note:__ if you use break and continue in a nested loop (a loop inside a loop), it will only skip the innermost loop.

For example:

```sh
for i in {1..3}
do 
    for j in {1..3}
    do 
        if (( $j == 2 ))
        then
            continue 
        fi 
        echo $i, $j
    done
done 
```

The output would be:

```
1 1
1 3
2 1
2 3
3 1
3 3
```

As you can see, when ```j``` is equal to 2, the ```continue``` command is executed, and the current iteration of the inner loop is skipped. However, the outer loop still continues to execute and the next iteration of the outer loop starts.

It means that the ```continue``` command only skips the current iteration of the innermost loop, but the other loops (outer loops) continue to execute.

It's important to note that if you want to exit the inner loop, you should use the ```break``` command instead of ```continue```. If you want to exit all the loops, you should use the ```break``` command in all the loops.

# Homework

* Learn ```until``` loop and ```while``` loop

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
* OpenAI - [ChatGPT](https://chat.openai.com/chat)
* GeeksforGeeks - [Break and Continue Keywords in Linux with Examples](https://www.geeksforgeeks.org/break-and-continue-keywords-in-linux-with-examples/) 