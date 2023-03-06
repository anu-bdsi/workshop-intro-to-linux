# Week 2 - Basic Linux commands 

## Learning Objectives

At the end of this workshop, students will know how to:

* Piping and redirecting standard input and output 
* Counting, sorting, and shuffling texts and file contents 
* Finding and replacing texts 
* Downloading and transfering data 
* Using loops and statements

# Start a tmux session

Start a new tmux session and for the rest of the course we will use this session. 

```sh
tmux new -s session_name 
```

# Counting, Sorting, and Redirecting Output 

## Redirections

Redirection is a feature in Linux such that when executing a command, you can change the standard input/output devices. The basic workflow of any Linux command is that it takes an input and gives an output. 

* The standard input (stdin) device is the keyboard.
* The standard output (stdout) device is the screen. 

With redirection, the above standard input/output can be changed. 

## Output Redirection 

The symbol ```>``` is used for output (stdout) redirection. 

__Example:__ 

```sh 
ls -al > listings.txt 
```

Here, the output of command ```ls -al``` is redirected to the file ```listings``` instead of the screen. You can try the command step by step to understand. First, run the command ```ls -al``` to see the result on your screen. Then, run the command ```ls -al > listings``` and use ```cat listings``` to see the contents of the file ```listings```. 

__Note:__ use the correct file name while redirecting command output to a file. If there is an existing file with the same name, the redirected output will overwrite the contents in the original file. 

If you don't want a file to be overwritten but want to add more content to an existing file. You can use the ```>>``` operator which means "append". 

```sh
ls >> listings.txt 
```

## Input Redirection 

The symbol ```<``` is used for input (stdin) redirection. 

__Example:__ 

The mail program in Linux can help you send emails from the Terminal. You can type the contents of the email using the standard device keyboard. But if you want to use a file's content as the information, you can use the input redirect operator in the following format. 

First, let's create a txt file and input the email content in. 

```sh
nano email.txt 
```

Copy and paste the following in:

```
Dear [Name],

I hope this email finds you well. I wanted to touch base with you and see how things are going. It's been a while since we last spoke, and I wanted to catch up.

On my end, things have been pretty busy. Work has been keeping me occupied, but I'm enjoying the challenges and learning a lot in the process. Outside of work, I've been trying to keep up with my hobbies, which include hiking and reading.

What about you? How have things been going? I'd love to hear about what you've been up to lately.

If you have some free time, I was also wondering if you would be interested in grabbing coffee or lunch sometime soon. It would be great to catch up in person and hear more about what's been going on in your life.

Looking forward to hearing back from you soon.

Best regards,
[Your Name] 
```

Then we can redirect the content of the file ```email.txt``` as input to command ```mail```. 

```sh 
mail -s "Subject" email_address < email.txt 
```

## Piping 

So far we've dealt with sending data to and from files. Now we'll take a look at a mechanism for sending data from one program to another. It's called piping and the operator we use is ```|```. What this operator does is feed the output from the program on the left as input to the program on the right. 

__Example:__ 

List only the first 3 files in the directory.  

```sh
ls | head -n 3
```

We may pipe as many programs as we like. In the below example we have then piped the output to tail so as to get only the third file. 

```sh
ls | head -n 3 | tail -n 1
```

When having errors in a pipe, sometimes it's hard to find where the error starts. So, when building a pipe up, it is better to build it incrementally. Run the first program and make sure it provides the output you were expecting. Then add the second program and check again before adding the third and so on. 

You may combine pipes with redirections too:

```sh
ls | head -n 3 | tail -n 1 > my_output 
cat my_output 
```

In this example we will feed the output of a program into the program less so that we can view it easier. 

```sh
ls -l /etc | less
# Full screen of output you may scroll
```

## ```wc``` - Word Count

The ```wc``` command calculates a file's line, word, character, or byte count. For basic usage, all you need is a file with some text in it. For example:

```sh
wc numSeq.txt 
```

The result should look like:

```
100 100 292 numSeq.txt
```

It means the file ```numSeq.txt``` has 100 lines, 100 words, and 292 characters. And the last one is the path to the file. 

```wc``` also has some useful options:

* ```wc -l``` only shows the number of lines. 
* ```wc -w``` only shows the number of words. 
* ```wc -c``` only shows the number of characters. 

## ```shuf``` - Shuffle 

The ```shuf``` command writes a random permutation of the input lines to standard output. This command reads either from a file or standard input in bash. 

### File shuf

```sh
shuf file_name 
```

This command takes the content of the file as input and shuffle it by lines, and write the output on the screen. For example, let's try shuffle our file ```numSeq.txt```:

```sh
shuf numSeq.txt 
```

It should print the randomised numbers from 1 to 100. Try shuffle another time to see if the result is same. 

The option ```-n``` can randomly pick a specific number of lines from the file and shuffle it, for example:

```sh
shuf -n 10 numSeq.txt 
```

![shuf_10](./images/shuf_10_numseq.png?raw=true) 

It is useful when you trying to randomly pick something from a file. 

```shuf``` can also take input from the pipe, for example: 

```sh
seq 5 | shuf 
```

![shuf](./images/shuffle.png?raw=true)

__Exercise:__ 

1. Shuffle the file ```numSeq.txt``` then write the output to a new file called ```numSeqRandom.txt``` using the redirect sign ```>```. 
2. Shuffle ```numSeq.txt``` another time and append the output to the file ```numSeqRandom.txt``` using redirect sign ```>>```. 
3. Check if our new file ```numSeqRandom.txt``` has 200 lines using ```wc -l```. 

## ```sort``` - Sort a File by Lines 

The ```sort``` is used to sort a file, arranging the records in a particular order. The sort command sorts contents line by line. It supports sorting alphabetically, in reverse order, by number, by month, and can also remove duplicates. 

The sort command follows features stated below:

1. Lines starting with a number will appear before lines starting with a letter.
2. Lines starting with an uppercase letter will appear before lines starting with the same letter in lowercase. 

__Example:__

```sh
sort numSeqRandom.txt | head 
```

![sort_num_random](./images/sort_num_random.png?raw=true)

We can see that the command sort the file alphabetically not numerically, to sort the file numerically we need to use the ```-n``` option.

```sh
sort -n numSeqRandom.txt | head 
```

![sort_num_n](./images/sort_num_numerical.png?raw=true) 

Sort a text file that mixed with uppercase and lowercase letters. First, let's create a file named ```mixed.txt``` and input in the following words:

```sh
abc
Abc
best
better
Good
go
```

Let's use sort command to sort this file:

```sh
sort mixed.txt 
```

![sort_mixed](./images/sort_mixed.png?raw=true) 

If we want to ignore case, we can use the ```-f``` option:

```sh
sort -f mixed.txt 
```

![sort_ignore_case](./images/sort_ignore_case.png?raw=true) 

__Other useful options:__

* ```sort -r```: sort in reverse order 
* ```sort -nr```: sort numerically in reverse order 
* ```sort -c```: to check if the gived file is already sorted or not 
* ```sort -u```: to sort and remove duplicates

## ```uniq``` - Remove Duplicates of Adjacent Lines (presorted) 

The uniq command filters adjacent matching lines from input, and writing to output. 

Let's try the command below:

```sh
wc -l numSeqRandom.txt
uniq numSeqRandom.txt | wc -l
```

These two commands have the same result because uniq command only works when the adjacent lines are matching. We can sort the file first and then use uniq. 

```sh
wc -l numSeqRandom.txt
sort -n numSeqRandom.txt | uniq | wc -l
```

This time, the result of the second command changed to 100 which should be the correct unique number. 

__Note:__ you can use ```sort -u``` instead if you only want to have the unique values of the file. But the command also has other useful options.

* ```uniq -c```: it tells you how many times a line was repeated by displaying a number as a prefix with the line. 
* ```uniq -d```: it only prints duplicated lines. 
* ```uniq -i```: ignore case. By default, the uniq command is case sensitive. 
* ```uniq -u```: it allows you to print out only unique lines. 
 
__Exercise:__ 

* ```sort -n numSeqRandom.txt | uniq -c | head```. 
* Create a new file and paste the following into it, name it as ```introToBiology.txt```, and find the 5 most used words in it. 

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

```sh
cat introToBiology.txt | tr ' ' '\n' | sort | uniq -c | sort -n | tail -n 5 
```

Run the above command line step by step for better understanding.

# Find and Replace 

## ```grep``` - Global Regular Expression Print 

The ```grep``` command is used to search text and strings in a given file. In other words, grep command searches the given file for lines containing a match to given strings or words. 

__Examples:__ 

* Search any line that contains the word "biology" in file ```introToBiology.txt```:

```sh
grep 'biology' introToBiology.txt 
```

* Perform a case-insensitive search for the word "biology" in the file:

```sh
grep -i 'biology' introToBiology.txt  
```

* Print out only the occurence of the word "biology" from the file: 

```sh
grep -o 'biology' introToBiology.txt 
```

* Look for all files in the current directory and in all of its subdirectories for the word "httpd":

```sh
grep -R 'httpd' . 
```

* Some other useful options:

```sh 
grep -v  # invert matching, select non-matching lines
grep -c  # print only the count of matching lines
grep -n  # prefix the output with line number 
```

## ```history``` - Get the History of Recently Used Commands 

This command gives you the history of the commands you have used. It is convenient to search commands from history and copy paste to reuse. 

Run the command below:

```sh
history
```

You'll see a list of old commands got printed on your screen, depending on how many commands you have used the list can be very long. So when you just trying to find a specific command, you can use ```history``` with ```grep```. For example, if I wanted to find the commands that I used to find the word "biology" I can use:

```sh 
history | grep 'biology'
```

If I want to see just the last 20 lines of command that I used, I can run:

```sh
history | tail -20
```

## ```tr``` - Translate 

The ```tr``` command can translates or deletes characters from standard input (stdin) and writes the result to standard output (stdout). Use ```tr``` to perform different text transformations, including case conversion, squeezing or deleting characters, and basic text replacement. 

The ```tr``` command can't read a file directly, it is often used with pipes ```|``` and redirects ```<``` to allow more complex file content processing. 

The basic syntax of ```tr``` is:

```sh
tr [options] set_1 set_2 
```

Running ```tr``` without any options replaces each of the characters specified in ```set_1``` with the characters from ```set_2```. 

Example: 

```sh
echo "Welcome" | tr e o 
```

Output: 

```
Wolcomo
```

Here, we redirect the output of command ```echo "Welcome"``` as input to ```tr e o``` to replace all character "e" with character "o". 

Example:

```sh
cat numSeq.txt | tr '\n' ' '
```

Output:

```
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 100
```

Here, ```\n``` means the character for a newline. 

There are more usages of ```tr```, please read the help manual for more. 

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
* GURU99 - [Input Output Redirection in Linux/Unix Examples](https://www.guru99.com/linux-redirection.html#:~:text=Redirection%20is%20a%20feature%20in,stdout)%20device%20is%20the%20screen.) 
* Red Hat - [3 surprising things you can do with the Linux wc command](https://www.redhat.com/sysadmin/linux-wc-command#:~:text=The%20Linux%20wc%20command%20calculates,the%20Linux%20commands%20cheat%20sheet.%20%5D) 
* Ryans Tutorials - [Piping and Redirection](https://ryanstutorials.net/linuxtutorial/piping.php) 
* GeeksforGeeks - [shuf Command in Linux with Examples](https://www.geeksforgeeks.org/shuf-command-in-linux-with-examples/)
* GeeksforGeeks - [SORT command in Linux/Unix with examples](https://www.geeksforgeeks.org/sort-command-linuxunix-examples/)
* GeeksforGeeks - [uniq Command in LINUX with examples](https://www.geeksforgeeks.org/uniq-command-in-linux-with-examples/) 
* nixCraft - [How To Use grep Command In Linux / UNIX With Practical Examples](https://www.cyberciti.biz/faq/howto-use-grep-command-in-linux-unix/) 