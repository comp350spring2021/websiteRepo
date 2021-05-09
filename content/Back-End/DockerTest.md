
This will describe how we test the functionality of our custom container and subexecutor script. These tests are to be very basic and treated like unit tests to ensure that we have the functionallity down for deployment and future tests.

## Test Files
The files used for testing are:
1. `test.in`
2. `test.out`
3. `<MD5>.py`

## Test 1
We are simulating an event assignment in which the admin wants students to write a python program that takes any number of integers from the command line and sums them together. The output should be a single integer value.  
The file `test.in` contains a single test case on each line. Each test case consists of any number of integers.  
Each line in the file `test.out` consists of a single integer value. When a line from `test.in` is passed into the student's code, the expected return value is on the corresponding line number in `test.out`.   
We will use \<MD5\> = 612a6ba796901572fb0fd23842062549 for our subID.  
`612a6ba796901572fb0fd23842062549.py` is a student submission and should generate an output file `612a6ba796901572fb0fd23842062549.out`. 

## Test 2  
We are simulating an event assignment in which the admin wants students to print a word provided as a command line argument. The output should be a single string.  
The file `test.in` contains a single test case on each line. Each test case consists of a single string.  
Each line in the file `test.out` is identical to `test.in`. When a line from `test.in` is passed into the student's code, the expected return value is on the corresponding line number in `test.out`.   
We will use \<MD5\> = 328b426862813868adb62fad0c5a165f for our subID.  
`328b426862813868adb62fad0c5a165f.py` is a student submission and should generate an output file `328b426862813868adb62fad0c5a165f.out`. 


## How the test will be run
* We must manually set up the environment for the container, but in production a daemon will be preparing the environment and executing the `docker run` command. This means we must create a directory on the host at `/usr/src/submitter/<MD5>/` and move all of the test files into it. 
* Execute the `docker run` command.

## What a successful result looks like
Upon completion of the `docker run` command, there will be a file `/usr/src/submitter/<MD5>/<MD5>.out` on the host. It is a csv file that lists the input, expected output, the actual output, and pass/fail on each line according to each test case.

## Executing Test 1
### Simulated directory layout   
```bash
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/612a6ba796901572fb0fd23842062549$ ll
total 12
drwxrwxr-x 2 ec2-user ec2-user  80 Apr  5 09:38 ./
drwxrwxr-x 4 ec2-user ec2-user 157 Apr  5 08:56 ../
-rw-r--r-- 1 ec2-user ec2-user 287 Apr  5 09:08 612a6ba796901572fb0fd23842062549.py
-rw-r--r-- 1 ec2-user ec2-user  63 Apr  5 08:11 test.in
-rw-r--r-- 1 ec2-user ec2-user  22 Apr  5 08:11 test.out
```
### Docker run
```bash
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/612a6ba796901572fb0fd23842062549$ docker run -it --rm --name="MD5" -v "$PWD":/usr/src/submitter -w /usr/src/submitter subexecutor executeSubmission.py 612a6ba796901572fb0fd23842062549
```

## Results
```
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/612a6ba796901572fb0fd23842062549$ lltotal 16
drwxrwxr-x 2 ec2-user ec2-user 124 Apr  5 09:38 ./
drwxrwxr-x 4 ec2-user ec2-user 157 Apr  5 08:56 ../
-rw-r--r-- 1 root     root     174 Apr  5 09:38 612a6ba796901572fb0fd23842062549.out
-rw-r--r-- 1 ec2-user ec2-user 287 Apr  5 09:08 612a6ba796901572fb0fd23842062549.py
-rw-r--r-- 1 ec2-user ec2-user  63 Apr  5 08:11 test.in
-rw-r--r-- 1 ec2-user ec2-user  22 Apr  5 08:11 test.out
``` 
Output report
```bash
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/612a6ba796901572fb0fd23842062549$ cat 612a6ba796901572fb0fd23842062549.out 
input,expected output,actual output,pass/fail
1 2 3,6,6,Pass
1 2 3 6,12,12,Pass
90000 5000 4000 1000,100000,100000,Pass
400 50 6,456,456,Pass
```
Success!


## Executing Test 2
### Simulated directory layout   
```bash
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/328b426862813868adb62fad0c5a165f$ ll
total 12
drwxrwxr-x 2 ec2-user ec2-user  80 Apr  5 09:41 ./
drwxrwxr-x 4 ec2-user ec2-user 157 Apr  5 08:56 ../
-rw-r--r-- 1 ec2-user ec2-user 220 Apr  5 09:08 328b426862813868adb62fad0c5a165f.py
-rw-r--r-- 1 ec2-user ec2-user  31 Apr  5 06:41 test.in
-rw-r--r-- 1 ec2-user ec2-user  31 Apr  5 06:41 test.out
```
### Docker run
```bash
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/328b426862813868adb62fad0c5a165f$ docker run -it --rm --name="MD5" -v "$PWD":/usr/src/submitter -w /usr/src/submitter subexecutor executeSubmission.py 328b426862813868adb62fad0c5a165f
```

### Results
```bash
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/328b426862813868adb62fad0c5a165f$ ll
total 16
drwxrwxr-x 2 ec2-user ec2-user 124 Apr  5 09:42 ./
drwxrwxr-x 4 ec2-user ec2-user 157 Apr  5 08:56 ../
-rw-r--r-- 1 root     root     155 Apr  5 09:42 328b426862813868adb62fad0c5a165f.out
-rw-r--r-- 1 ec2-user ec2-user 220 Apr  5 09:08 328b426862813868adb62fad0c5a165f.py
-rw-r--r-- 1 ec2-user ec2-user  31 Apr  5 06:41 test.in
-rw-r--r-- 1 ec2-user ec2-user  31 Apr  5 06:41 test.out
``` 
Output report
```bash
ec2-user@ip-10-0-1-230:~/testing/testFiles/dockerbuild/328b426862813868adb62fad0c5a165f$ cat 328b426862813868adb62fad0c5a165f.out 
input,expected output,actual output,pass/fail
hello,hello,hello,Pass
nice_to_meet_you,nice_to_meet_you,nice_to_meet_you,Pass
pardon,pardon,pardon,Pass
```
Success!
