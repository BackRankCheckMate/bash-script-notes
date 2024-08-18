## Lesson 1

Open a text editor using nano or vim

```bash
nano myscript.sh 
```

Write “ls” command inside the file, and save it, then exit from it

Use the following command to turn myscript.sh into an executable file

```bash
sudo chmod +x myscript.sh
```

Use the following command to execute the script

```bash
./myscript.sh
```

First line of bash script should always be:

```bash
	#!/bin/bash
```

## Lesson 2

### Variables

Declare name in myname variable, reference the variable using $ sign

```bash
myname="Joe"
echo $myname
```

After we exit the shell, the variables will be deleted

echoing the variable inside bash script:

```bash
echo "My name is $myname."
```

The echoed portion must be inside “ ”, and not inside ‘ ’.

To save an output in variables:

```bash
files=$(ls)
echo $files
```

Output will be all the files in the current directory

$() is a subshell, it runs a command in the background

There are some default/already declared variables in the environment. For eg: $USER

all caps var is a system variable

To see all the environment variables:

```bash
env
```

## Lesson 3

### Math functions

To evaluate an expression in bash,

```bash
expr 30 + 20
```

To multiply, you need to escape the *

```bash
expr 30 \* 10
```

This is because in bash, * is a wildcard

## Lesson 4

### If Statements

```bash
mynum=200
if [ mynum -eq 200 ]
then
    echo "The condition is true."
else 
		echo "The condition is false."
fi
```

fi is ending the if statement

-eq means equal

! works the same way it works in other programming languages

-ne means not equal

-gt means greater than

-f means file, it checks for a file

-d means directory, it checks for a directory

```bash
if [ -f ~/myfile ]
then 
		echo "The file exists"
else
		echo "The file does not exist"
fi
```

A code to check if htop exists or not

```bash
#!/bin/bash

command = /usr/bin/htop

if [ -f $command ]
then
		echo "$command exists, let's run it"
else
		echo "$command does not exist, lets install it"
		sudo apt update && sudo apt install -y htop
fi

$command	
```

if statement could also have been written as:

```bash
if command -v $command
```

command is just a command that checks whether a command is available or not

$command could have been named anything

[ -f $command ] is a test case

type man test for more details

```bash
man test
```

## Lesson 5

### Exit codes

```bash
ls -l /etc
# output
echo $?
#output is 0
```

$? is already declared when we ran the previous command

0 means success, anything else is a failure

```bash
package=htop
sudo apt install $package
echo "The exit code for the package installed is $?"
```

Can be used in knowing if something failed or succeeded

```bash
package=htop
sudo apt install $package >> package_install_results.log
if [ $? -eq 0 ]
		echo "Success, $package is now available at "
		which $package
else
		echo "Failure" >> package_install_failure.log
fi
```

>> redirects the output to the mentioned files

### Manipulating exit codes

```bash
echo "Hello World"
exit 1
echo $?
echo "Did you see the exit code print automatically?"
echo "Probably not"
```

Even though the exit code should be 0, it will now be one when we check it

Anything after exit will not run

This is why task order is important in bash

## Lesson 6

### While loops

```bash
myvar=1

while [ $myvar -le 10 ]
do
		echo $myvar
		myvar=$(( $myvar +1 ))
		sleep 0.5
done
```

-le means less or equal to

A simple, not realistic loop searching for a file:

```bash
while [ -f ~/testfile ]
do
		echo "As of $(date), the file still exists"
done

echo "As of $(date), the file does not exist"
```

$(date) is inside a subshell i.e. $(). It runs in the background 

## Lesson 7

### Universal Update Script

```bash
release_file=/etc/os-release

if grep -q "Arch" $release_file
then
		# This runs if host is based on Arch
		sudo pacman -Syu
fi

if grep -q "Debian" $release_file || grep -q "Ubuntu" $release_file
then
		# This rins if host is based on Debian or Ubuntu
		sudo apt update
		sudo apt dist-upgrade
fi	
```

grep searches files for specific strings

-q is quiet mode. Nothing is printed on the screen

/etc/os-release is a file containing info about the OS

## Lesson 8

### For Loops

```bash
for i in {1..10}
do
		echo $i
		sleep 1
done
```

To compress all log files in a directory

```bash
for file in logfiles/*.log
do
		tar -czvf $file.tar.gz
done
```

## Lesson 9

### Where to store scripts?

In /usr/local/bin 

```bash
	sudo mv updatescript.sh /usr/local/bin/update
	sudo chown root:root/usr/local/bin/update 
```

Now, to run update, just type update

This works s /usr/local/bin is included in $PATH variable

If it is not included in $PATH variable, you can add it :

```bash
export PATH=/usr/local/bin:$PATH
```

## Lesson 10

### Data Streams

To prevent errors from appearing into screen

```bash
find /etc -type f 2> /dev/null 
```

anything in /dev/null is completely wiped out

1> standard output ( default )

2> standard error 

&> Both output and error

Redirecting all successful output to a file

```bash
find /etc -type f > file.txt
```

Standard Input:

```bash
echo "Please enter your name:"
read myname
echo "Welcome $myname"
```

## Lesson 11

### Functions

```bash
check_exit_status () {
		if [ $? -ne 0 ] 
		then
				echo "An error occurred, please check $error_log file."
		fi
}

# Calling the function
check_exit_status
```

## Lesson 12

### Case Statements

```bash
echo "What is your favorite Linux distribution?"

echo "1 - Arch"    
echo "2 - CentOS"
echo "3 - Debian"
echo "4 - Mint"
echo "5 - Ubuntu"
echo "6 - Something else..."

read distro;

case $distro in
    1) echo "Arch is a rolling release.";;
    2) echo "CentOS is popular on servers.";;
    3) echo "Debian is a community distribution.";;
    4) echo "Mint is popular on desktops and laptops";;
    5) echo "Ubuntu is popular on both severs and computers";;
    6) echo "There are many distributions out there.";;
    *) echo "You didn't enter an appropriate value."
esac

```

All cases should end with double semi-colons;;

## Lesson 13

### Scheduling Jobs using “at”

```bash
at 11:43 -f ./myscript.sh
```

Use the at command to automatically run the myscript file at 11 34

if at command not present

```bash
sudo apt install at
```

To see the queue of the scheduled jobs

```bash
atq
```

To remove the job from the queue

```bash
atrm job_id
```

job_id is at left hand side when atq is executed

To execute the job some date in the future

```bash
at 18:35 081624 -f ./myscript.sh
```

Will run at 6 15 pm, august 16, 2024

### Scheduling Jobs using Cron

Use fully qualified name for the commands

```bash
/usr/bin/echo "This is an echo command. "
$(/usr/bin/date)
```

This is for security reasons, and also a good practice

This is also because when a job runs in cron, it might not run in the same path or environment.

To edit the cron tab

```bash
crontab -e
```

To schedule task

```bash
# minute hour dayOfMonth month dayOfWeek commandToRun
30 1 * * 5 /usr/local/bin/script
```

To write crontab for another user

```bash
sudo crontab -u username -e 
```

## Lesson 14

### Arguments

```bash
echo "You entered the argument $1"

################################

./myscript.sh Linux
 ###############################
 Output: You entered the argument Linux 
```

Another code snippet of argument

```bash
lines=$(ls -lh $1 | wc -l)

if [ $# -ne 1 ]
then
    echo "This script requires exactly one directory path passed to it."
    echo "Please try again."
    exit 1
fi

echo "You have $((lines-1)) objects in the $1 directory"

```

The if statement checks if the arguments provided is exactly one or more or less

