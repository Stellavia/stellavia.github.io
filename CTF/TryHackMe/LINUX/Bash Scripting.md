🔗 [Link to the Room](https://tryhackme.com/room/bashscripting)

🔗 [Link for further learning](https://devhints.io/bash)

## 🏷️ A Walkthrough room to teach you the basics of bash scripting

1. [Our first simple bash scripts](#our-first-simple-bash-scripts)<br>
2. [Variables](#variables)<br>
2.1 [Debugging Bash scripts](#debugging-bash-scripts) <br>
2.2 [Using multiple variables](#using-multiple-variables)<br>
3. [Parameters](#parameters)<br>
3.1 [Command-line parameters](#command-line-parameters)<br>
3.2 [Interactive input with `read`](#interactive-input-with-read)<br>
3.3 [Special Parameters in Bash](#special-parameters-in-bash)<br>
4. [Arrays](#arrays)<br>
4.1 [Indexes](#indexes)<br>
4.2 [Creating an array in Bash](#creating-an-array-in-bash)<br>
4.3 [Printing all items in an array](#printing-all-items-in-an-array)<br>
4.4 [Accessing a single item](#accessing-a-single-item)<br>
4.5 [Removing an item from an array](#removing-an-item-from-an-array)<br>
4.6 [Changing (replacing) an item](#changing-replacing-an-item)<br>
5. [Conditionals](#conditionals)<br>
5.1 [if statement](#if-statement)<br>
5.2 [Common comparison operators](#common-comparison-operators)<br>
5.3 [Using parameters in conditionals](#using-parameters-in-conditionals)<br>
5.4 [Checking files with conditionals](#checking-files-with-conditionals)<br>


# 📚 Study Notes #

- Bash is a scripting language that runs within the terminal on most Linux distros, as well as MacOS.
- Shell scripts are a sequence of bash commands within a file, combined together to achieve more complex tasks than simple one-liner and are especially useful when it comes to automating sysadmin tasks such as backups.

# Our first simple bash scripts

1. Every bash script needs a starting line
- A bash script must start with this line: `#!/bin/bash`
- This tells your system: “Run this file using Bash.”
- Without it, the system won’t know how to execute your script.

2. Your first example: printing text
- You can make Bash print text to the terminal using echo.
- Example: `echo "Hello World"`
- When you run the script, it will display: `Hello World`
- Think of echo like print in Python — it just shows text on the screen.

3. You can run normal Linux commands in scripts
  - Any command you can run in the terminal can also go inside a bash script.
  - For example: `ls`
  - When the script runs, it will list the files in the current directory — just like typing ls directly into the terminal.
  - You can even combine commands, like `whoami` or `id` = This will show your current username and your user and group IDs

4. Making the script runnable
  - Before you can run your script, you must give it permission: `chmod +x yourfile.sh`
  - Then run it with `./yourfile.sh`
  - The ./ means “run this file from the current directory.”

>[!IMPORTANT]
> - Always include #!/bin/bash at the top of your script (even if examples don’t show it)
> - Basic Linux command knowledge is assumed
> - The best way to learn Bash is by running the commands yourself and experimenting


---
><details><summary>❓What piece of code can we insert at the start of a line to comment out our code?</summary>#</details>
---
><details><summary>❓What will the following script output to the screen, echo “BishBashBosh”</summary>BishBashBosh</details>
---

# Variables

- A variable is just a name that stores a value, so you can reuse it later.

- In Bash, you create a variable like this: `name="Jammy"`
- Important rules: No spaces around `=` and variable names **can’t contain spaces**
<img width="502" height="236" alt="carbon-2" src="https://github.com/user-attachments/assets/7fcb4243-49a7-48b1-98d6-1aab41960634" />

- To use a variable, you add $ in front of its name.
- Hence `name` is the variable and `$name` is the value stored inside it
<img width="678" height="308" alt="carbon-3" src="https://github.com/user-attachments/assets/93851167-0374-489f-a9c7-52b5aa6dc590" />

- Variables help you avoid repeating the same text over and over, change values in one place instead of many and/or make scripts easier to read and fix
- Instead of typing "Jammy" everywhere, you just use $name.

## Debugging Bash scripts

- Debugging means finding and fixing mistakes and Bash gives you tools to help.
- From terminal you can run your script in debug mode by `bash -x file.sh`
- This shows each command as it runs, what works, where errors happen which is great for understanding what your script is actually doing step by step.

- You can turn debugging on and off inside your script:
<img width="982" height="452" alt="carbon-4" src="https://github.com/user-attachments/assets/8680cb55-a1e1-4721-a0fa-b0f8523e8509" />
- This is useful when you only want to debug a specific part.

- When debugging `+` means the command ran - The output shows what the command produced.
- Errors are easy to spot because you can see exactly where things break
- This makes fixing mistakes much less frustrating.

## Using multiple variables

- You’re not limited to just one variable, e.g. `echo "Hello $name, welcome back!"`
- Bash will replace $name with its value automatically.

<img width="982" height="524" alt="carbon-7" src="https://github.com/user-attachments/assets/a147a7d1-f817-4c96-892c-5319e9db5afe" />


---
><details><summary>❓What would this code return?</summary>Jammy is 21 years old</details>
---
><details><summary>❓How would you print out the city to the screen?</summary>echo $city</details>
---
><details><summary>❓How would you print out the country to the screen?</summary>echo $country</details>
---

# Parameters

- Parameters are one of the most powerful parts of Bash.
- They let your script receive information from the outside, instead of hard-coding everything.
- Think of them as inputs to your script.

## Command-line parameters

- When you run a bash script, you can pass values to it like this: `./example.sh Alex`
- Inside the script `$1` is first argument, `$2` second argument, `$3` third argument, and so on…
<img width="898" height="452" alt="carbon-13" src="https://github.com/user-attachments/assets/f0e9bc01-200e-49c8-98fc-69c5d7be5713" />
<img width="762" height="452" alt="carbon-14" src="https://github.com/user-attachments/assets/d94841b8-3008-4356-9496-b0ff4c5c5b0c" />

## Interactive input with `read`
- Sometimes you don’t want to pass values in the command line, instead, you want the script to ask the user for input.
- You can do this with `read`:
<img width="101" height="60" alt="image" src="https://github.com/user-attachments/assets/1289d9f8-49bf-48be-9755-245e050f94ce" />

- When the script runs, it will pause and wait for you to type something.
- Once you press Enter, it stores the input in the variable and prints it.

## Special Parameters in Bash

|**Parameter**|**Description|**Usage**|
|:---:|-----------------------------------------------|--------------------------------------------------------|
|$0|tells you which script is currently running|writting usage messages; debugging; logging which script ran|
|$#|tells you how many arguments were passed to the script|useful for checking if the user passed enough arguments|
|$@|all arguments passed to the script, one by one|commonly used in loops|
|$*|similar to $@, but it treats everything as one big string|recommended to avoid it unless you know why you need it|
|$?|exit status of last command, if either succeeded or failed|for error handling, automation, security scripts|
|$$|gives the PID of the running script|for logging, temporary files, process tracking|

---
><details><summary>❓How can we get the number of arguments supplied to a script?</summary>$#</details>
---
><details><summary>❓How can we get the filename of our current script(aka our first argument)?</summary>$0</details>
---
><details><summary>❓How can we get the 4th argument supplied to the script?</summary>$4</details>
---
><details><summary>❓If a script asks us for input how can we direct our input into a variable called ‘test’ using “read”</summary>read test</details>
---
><details><summary>❓What will the output of “echo $1 $3” if the script was ran with “./script.sh hello hola aloha”</summary>hello aloha</details>
---


# Arrays 
- Arrays let you store multiple values inside one variable instead of creating lots of separate variables.
- Think of an array like a numbered list.
- Most commonly notated as `var[index_position]`

## Indexes
- Every item in an array has a number called an index.
- Indexes always start at 0
- e.g.: `['car', 'train', 'bike', 'bus']`
<img width="584" height="300" alt="image" src="https://github.com/user-attachments/assets/0466bca9-bb3a-413b-a648-57890bf6757f" />

### Creating an array in Bash

- Rules:
  - Variable name first
  - Items go inside parentheses `( )`
  - Items are separated by spaces
  - Quotes are recommended
- e.g.: `transport=('car' 'train' 'bike' 'bus')`

## Printing all items in an array

- `echo "${transport[@]}"`
- this means @ is all items and `${}` tells bash you're working with variable
- output: `car train bike bus`

## Accessing a single item

- `echo "${transport[1]}"`
- output: `train`
- Because Index `1` = second item

## Removing an item from an array

- To delete an item use `unset transport[1]`
- This removes `train` from the array.
- If you print the array again, it will be gone.

## Changing (replacing) an item

- You can assign a new value to a specific index by `transport[1]='trainride'`
- Now the array becomes `car trainride bike bus`

>[!NOTE]
> Arrays are great when you need to store multiple values of the same type, loop through data, organize inputs cleanly, build more advanced scripts (automation, security checks, etc.)

&nbsp;

>[!TIP]
>Extend your biography maker:
> - Store multiple names in an array
> - Store multiple facts (age, job, hobby)
> - Pick and display them dynamically
> - This is exactly how real scripts evolve — small upgrades over time

&nbsp;

---
><details><summary>❓What would be the command to print audi to the screen using indexing.</summary>echo "${cars[1]}"</details>
---
><details><summary>❓If we wanted to remove tesla from the array how would we do so?</summary>unset cars[3]</details>
---
><details><summary>❓How could we insert a new value called toyota to replace tesla?</summary>cars[3]='toyota'</details>
---

# Conditionals

- A conditional means that “Only do this if something is true.”
- Bash checks conditions using operators like `equal to`, `greater than`, `less than`

## if statement

- Every if statement in Bash follows this pattern:
<img width="335" height="153" alt="image" src="https://github.com/user-attachments/assets/87670b15-90af-46d2-942c-b8deddd051b1" />
- IMPORTANT: You must have spaces inside `[ ]` and `fi` ends the if statement (it’s `if` backwards)

Example:
<img width="167" height="202" alt="image" src="https://github.com/user-attachments/assets/3047a57b-8748-439d-8f97-bfa3d857e473" />

What’s happening:
  - `$count` is compared to `10`
  - `-eq` means “equal to”
  - Since 10 = 10, it prints: <img width="62" height="31" alt="image" src="https://github.com/user-attachments/assets/f694382a-ec90-4dcc-a6c7-8c228ac8f2dd" />

## Common comparison operators

|operator|definition|
|:----:|----------------|
|-eq|equal to|
|-ne|not equal to|
|-gt|greater than|
|-lt|less than|
|-ge|greater than or equal to|

## Using parameters in conditionals

- You can compare user input too.

<img width="253" height="157" alt="image" src="https://github.com/user-attachments/assets/4c7426a3-e5b1-47d4-97e4-35f54363e4af" />
- Run it like this: `./example.sh guessme` (output will be `They are equal)
- Run it with something else like: `./example.sh hi` (output will be `They are not equal)

## Checking files with conditionals

- Conditionals are very powerful for working with files.
- Two important file checks:
  - `-f` - does the file exist?
  - `-w` - is the file writable?


---
><details><summary>❓What is the flag to check if we have read access to a file?</summary>-r</details>
---
><details><summary>❓What is the flag to check to see if it's a directory?</summary>-d</details>
---
