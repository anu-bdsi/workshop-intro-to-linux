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

We can use this command to sort the shuffled ```numSeqRandom.txt``` file. 

Try ```shuf numSeqRandom.txt | head```.

Unfortunately it didn't sort numerically as we expected but alphabetically. To sort numerically, use ```-n``` option. 

Now try ```shuf -n numSeqRandom.txt | head``` see what results you got. 

### ```uniq``` - Remove Duplicates of Adjacent Lines (presorted) 

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

### Exercise

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

## Find and Replace 

### ```grep``` - Global Regular Expression Print 

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






## References 
* Andrew Severin - [Introduction to Unix](https://bioinformaticsworkbook.org/Appendix/Unix/unix-basics-1.html#gsc.tab=0) 
* Wikipedia - [Biology](https://en.wikipedia.org/wiki/Biology) 