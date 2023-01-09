# Week 2

## Counting, Sorting, and Redirecting Output 

### Redirect

* ```cmd < file``` Use file as input
* ```cmd > file``` Write output to file
* ```cmd >> file``` Append output to file 

### ```wc``` - Word Count

This command will give you the number of lines, words, and characters in a file. 

Try ```wc numSeq.txt```. 

```wc``` has options for showing only the number of lines ```wc -l```, the number of words ```wc -w```, or the number of characters ```wc -c```. 

### ```tr``` - Translate 

This command can take any individual character and replace it with another character. 

It is a little different than other commands as it requires standard output as the input rather than a filename. To do this, we will use the ```<``` redirect symbol.

Try ```tr '\n' ',' < numSeq.txt```, it means translating new line character ```\n``` to comma ```,``` taking input from file ```numSeq.txt```. 

### ```|``` - Pipe Redirect

It can pass the output of one command to another command.

For example ```cat numSeq.txt | tr '\n' ','``` gets the same output as ```tr '\n' ',' < numSeq.txt```. 

It passes the output of ```cat numSeq.txt``` as input to the command ```tr '\n' ','```. 

### ```shuf``` - Shuffle 

Generate random permutations of lines of the file supplied. 

Try shuffle our file ```numSeq.txt``` then write the output to a new file called ```numSeqRandom.txt``` using the redirect sign ```>```. 

Try shuffle ```numSeq.txt``` another time and append the output to the file ```numSeqRandom.txt``` using redirect sign ```>>```. 

Check if our new file ```numSeqRandom.txt``` has 200 lines by using ```wc -l```. 

### ```sort``` - Sort a File by Lines 





## References 
* Andrew Severin - [Introduction to Unix](https://bioinformaticsworkbook.org/Appendix/Unix/unix-basics-1.html#gsc.tab=0) 