[![Twitter](https://img.shields.io/twitter/url/https/twitter.com/xbforce.svg?style=social&label=Follow%20%40xbforce)](https://twitter.com/xbforce)


# Ways of saving outputs

There are different ways to save stdout and stderr to a file on Unix like machines.

In this post I mention stdout and stderr a lot, so first we need to know what they are:
Stdout is short for standard output, which is the default data stream for output.
Stderr or standard error is another output stream typically used by programs to output error messages.


If you want to overwrite outputs you can use a single **greater than** sign, e.g:

```
$ python portscanner.py 86.15.10.18 > result.txt
```

The single “>” sign will overwrite the outputs and shows stderr on the terminal, but the file does not contain any error. Actually it shows stderr in the terminal and sends stdout to the file.


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
overwrite     stdout      stderr          stdout      stderr
   >            No         Yes              Yes         No
```


If you need to append something to a file you can use two “greater than” sign. e.g:

```
$ python portscanner.py 86.15.10.18 >> result.txt
```

This syntax behaves the same as single “>”, means it shows the stderr in the terminal and put the stdout in the file.


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
append     stdout      stderr             stdout      stderr
  >>         No         Yes                 Yes         No
```


If you don’t like stdout/stderr to be printed on the terminal at all, then you can add an ampersand “&” right before the above syntaxes! e.g:

```
$ python portscanner.py 86.15.10.18 &> result.txt
```

The syntax will overwrite both stdout and stderr to the result.txt file, but you would not see anything on your terminal.


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
overwrite     stdout      stderr          stdout      stderr
  &>            No         No              Yes         Yes
```


If you do not want to print anything in your terminal but append stdout and stderr to your result.txt file then you can use “&>>”. e.g:
```
$ python portscanner.py 86.15.10.18 &>> result.txt
```


```
Syntax       [ Terminal ]                  [ Output file ]
-------------------------------------------------------------------------------------------- 
append     stdout      stderr             stdout      stderr
 &>>         No         No                 Yes         Yes
```


If errors matter to you and you are going to overwrite the stderr to a file, then you can use “2>”. e.g:

```
$ python portscanner.py 86.15.10.18 2> result.txt
```
This syntax does not add the stdout to your file, instead, it prints them on the terminal.


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
overwrite     stdout      stderr          stdout      stderr
  2>            Yes         No              No         Yes
```


As you guess to append errors but not stdout to your file you can use “2>>”, e.g:
```
$ python portscanner.py 86.15.10.18 2>> result.txt
```


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
append     stdout      stderr             stdout      stderr
 2>>         Yes         No                 No         Yes
```



Also It is possible to send the stdout and stderr to two different files:
To overwrite outputs:

```
$ python portscanner.py 86.15.10.18 > result.txt 2> errors.txt
```

To append the outputs to the file:

```
$ python portscanner.py 86.15.10.18 >> result.txt 2>> errors.txt
```
In this case, nothing would be printed on the terminal and you are only able to see the outputs from the files.


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
overwrite     stdout      stderr          stdout      stderr
 1> - 2>        No         No              Yes         Yes
```


```
Syntax            [ Terminal ]                   [ Output file ]
-------------------------------------------------------------------------------------------- 
append          stdout      stderr              stdout      stderr
1>> - 2>>         No         No                 Yes         Yes
```


The “tee” tool will print stdout and stderr on the terminal but only save the stdout to the file. To overwrite stdout to result.txt you need to use a pipline between two commands:

```
$ python portscanner.py 86.15.10.18 | tee result.txt
```


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
overwrite     stdout      stderr           stdout      stderr
  | tee        Yes         Yes              Yes         No
```

If you are going to append stdout to a file you can use “tee” with its “-a” flag. Like the previous command it prints stdout and stderr but only save the stdout, e.g:

```
$ python portscanner.py 86.15.10.18 | tee -a result.txt
```


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
append          stdout      stderr             stdout      stderr
| tee -a         Yes         Yes                 Yes         No
```

But what if you want to have stdout and stderr on both terminal and the result.txt file?! How do you do that?
In this case you can add a single ampersand right after the pipeline and before the tee command:


```
$ python portscanner.py 86.15.10.18 |& tee result.txt
```

The above syntax will overwrite everything to the file.


```
Syntax          [ Terminal ]               [ Output file ]
-------------------------------------------------------------------------------------------- 
overwrite     stdout      stderr          stdout      stderr
 |& tee        Yes         Yes              Yes         Yes
```


And finally use the same syntax with “-a” flag to append the outputs to result.txt, e.g:

```
$ python portscanner.py 86.15.10.18 |& tee -a result.txt
```


```
Syntax             [ Terminal ]                  [ Output file ]
-------------------------------------------------------------------------------------------- 
append           stdout      stderr             stdout      stderr
 |& tee -a         Yes         Yes                 Yes         Yes
```


<link href="https://fonts.googleapis.com/css?family=Cookie" rel="stylesheet"><a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/xbforce"><img src="https://cdn.buymeacoffee.com/buttons/bmc-new-btn-logo.svg" alt="Buy me a coffee"><span style="margin-left:5px;font-size:28px !important;">Buy me a coffee</span></a>
