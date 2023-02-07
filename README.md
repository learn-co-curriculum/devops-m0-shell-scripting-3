# Control flow

## Learning goals

- Gain an understanding of the essentials of control flow in shell scripts
- Learn how to use `if`, `for`, `while`, and `case` statements

## Introduction

Now that we're acquainted with variables, functions, and input, the last essential ingredient we need for our scripts is *control flow*.

**Control flow** simply refers to the order in which your script's code is executed in a program. Its usage allows your script to make decisions, and execute different pieces of code depending on the outcome of a condition. We can achieve this using the `if`, `for`, `while`, and `case` statements.

## If

The `if` statement is the simplest of the bunch; simply execute some code based on the result of a condition. Here's what it looks like:

```bash
#!/bin/bash

number = 10

# Checks if number is equal to 7
if [[ $number -eq 7 ]] 
then
	echo "Equal!"
else
	echo "Not equal!"
fi

# Checks if number is greater than 5
if [[ $number -gt 5 ]] 
then
	echo "Number is greater than 5!"
else
	echo "Number is not greater than 5."
fi
```

Although the general structure might be a little confusing, it should be relatively easy to understand! 

The first and last line of each block, `if` and `fi`, simply mark the beginning and end of the conditional.

The `[[ condition ]]` section marks the condition being tested (the spaces between the `[[ ]]` brackets are mandatory). The condition can be any number of things, and you can even chain them together.

In general, the operators you can use to compare are very easy to memorize; they're based off plain English:

| Operator | Definition |
|------|---------------|
| -eq  |   **eq**ual   |
| -ne  |   **n**ot **e**qual   |
| -gt  |   **g**reater **t**han   |
| -ge  |   **g**reater than or **e**qual to   |
| -lt  |   **l**ess **t**han   |
| -le  |   **l**ess than or **e**qual to   |
| &&   |   AND  |
|  \|  |   OR  |
| ==   |   equal (used for strings) |

So if you want to find out whether a variable or number is less than or equal to another variable or number, you can do:

`if [[ $number1 -le $number2 ]]`

In the case of strings, these compare in ASCII alphabetical order; for example, `if [[ "apple" -lt "carrot" ]]` will always evaluate to true because `a` comes before `c` alphabetically. However, something to keep in mind when comparing stings, is that you should use the `==` operator instead of `-eq` in order to 

In order to chain these, you can use the `&&` and `||` operators for AND and OR respectively:

```bash
#!/bin/bash
# Ask user for age
echo "Please enter your age:"
read age
if [[ $age -ge 18 && $age -le 60 ]]
then
	echo "Within age range!"
else
	echo "Outside age range!"
fi
```

You can also wrap the conditions in parentheses `()` to prioritize what is inside those parenthesis first, e.g:

```bash
if [[ ($age -ge 18 && $age -le 60 && $name == "John") || $age -gt 60 ]]
```

The above example checks the following:

- Age is greater than 18, less than or equal to 60, and the name is `John`
OR
- Age is greater than 60

## For

The **for-loop** allows you to iterate over a sequence of values, such as the lines in a file or a list of numbers. Let's say you want to execute a block of code five times, you could do something like:

```bash
for i in 1 2 3 4 5
do
	echo $i
done
```

In this case, we have a sequence of numbers 1-5, and the for-loop goes through each one of those, executing the code we place under `do`. 

In each iteration of the loop, `i` gets set to the current element in the sequence. Running that block of code would output each number in a separate line. You can gave `i` whatever name you want, although `i` is customary (stands for index).

> You can also specify a range using the `{x..y}` format, e.g: `for i in {1..5}`

You can get pretty creative with it! For example, let's make a simple script that goes through every file in a folder and prints out whether it's called `target.txt`:

```bash
#!/bin/bash
files=$(ls)
for file in $files
do
	if [[ "$file" == "target.txt" ]]
	then
		echo "Found target file!"
	fi
done
```

## While

The **while-loop** is very similar to the for-loop; all it does is execute a block of code repeatedly as long as a certain condition is met. For example:

```bash
#!/bin/bash
counter=10
while [[ $counter -gt 0 ]]
do
	echo $counter
	# We're doing math as a command here, subtracting 1 from the value of counter
	counter=$((counter-1))
done
```

## Case

The `case` statement is a bit more situational, but it can still be very useful and concise when you need to match against against several patterns. For example, let's say our user types in a command to `start`, `stop`, or `restart` our script. We could simply chain `if-else` for every possibility, or we could use the `case` statement:

```bash
#!/bin/bash
echo "What would you like to do? 'start', 'stop', or 'restart'?"
read input
case $input in
	start)
		echo "Starting!"
		;;
	stop)
		echo "Stop!"
		;;
	restart)
		echo "Restart!"
		;;
	*)
		echo "Invalid input!"
		;;
esac
```

The first couple of lines just ask the user for input, and then reads and stores it into the `input` variable. 

Then we start our `case` statement which simply reads like `case $<variable> in`.

Afterwards, each of the conditions we're testing go in the next line, and inside that block, the actual code that executes for that condition.

To mark the end of that block of code, we follow the line with another line containing `;;`.

Lastly, we end the `case` statement with `esac` all the way at the end, similar to `fi` or `done`. 

You can also chain multiple conditions in the same line; for example, if you want users to be able to type both `start` and `s`, you can chain them together like so:

```bash
...
case $input in
	start|s)
		# do stuff here
...
```

## Sample script

Let's go over a sample script that incorporates most of what we've learned and introduces a few interesting ways of using control flow. Try to dissect it yourself and understand what it does before reading the explanation:

```bash
#!/bin/bash

# Function to display menu options
display_menu() {
  echo "Menu Options:"
  echo "1. Option 1"
  echo "2. Option 2"
  echo "3. Option 3"
  echo "4. Quit"
}

# Function to process user's menu selection
process_selection() {
  case $1 in
    1)
      # Code to execute for Option 1
      echo "You selected Option 1"
      ;;
    2)
      # Code to execute for Option 2
      echo "You selected Option 2"
      ;;
    3)
      # Code to execute for Option 3
      echo "You selected Option 3"
      ;;
    4)
      # Code to execute for Quit
      echo "Quitting program."
      exit
      ;;
    *)
      # Default option
      echo "Invalid selection"
      ;;
  esac
}

# Main loop to display menu and process user input
while true 
do
  display_menu
  read -p "Enter your selection: " selection
  process_selection $selection
done
```

If you run this script, you'll be greeted by a simple menu, which then waits for your input:

```bash
Menu Options:
1. Option 1
2. Option 2
3. Option 3
4. Quit
```

If you type any option number and hit enter, it'll run the code under the `process_selection()` function and choose what to run. The interesting bit here is the `while true`-- since the condition tested by the while loop is *always* `true`, this means it's going to loop forever.

This means that right after running the specific option code, it'll go right back to the menu. The only exception here is the fourth option, because we explicitly call `exit`. Running `exit` will quit out of our shell script session.

This is a relatively common design pattern for scripts that present the user with multiple options that are not mutually exclusive. So if your script lets the user choose multiple options (often repeatable), this is a good way of handling it!

## Conclusion

Now you're finally ready to make your own shell scripts! This lesson was a bit more complex than the previous ones, but with enough practice, all these concepts will click in just fine.

There are a lot of other, more situational features to the language that we didn't get to go over. You'll most likely uncover these while searching for how to do something specific, but in the meantime, here are a couple of great and concise resources you can fall-back to:

- https://devhints.io/bash
- https://www.pcwdld.com/bash-cheat-sheet
