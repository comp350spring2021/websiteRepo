---

---
# Submitter Overview  

**Author: Alex Sullivan**  
**last updated: 4/4/2021**

{{<toc>}}

The submitter is a web application implemented With AWS cloud services, designed to work as an automatice grader for python programming assignments. Designed and built in Spring 2021, for Software Engineering (Comp 350) at California State University Channel Islands, with Professor Soltys.

## Description
The submitter project consists, of a website used by administrators to create **events**, either for a class or a competitive coding competition. Students, or participants can then submit answers to various **assignments** assoicated with an event. Admins can create an account, in order to create, and modify events, as well as monitor student submissions. 

Students can submit their code via the web site where it is passed to a backend server and run against pre determined test cases provided by the event admin. Students are given immediate feedback through email detailing the results of the test cases, grade cases are hidden from students until the end of the event.

## Front-End
The front end consists of a website hosted on an AWS ec2 instance, we decided to use django as our framework of choice for the webserver.

### Administrators
Administrators have the following  abilities within the front end interface:

* Create an event
* Modify an event or assoicated assignments
* View student submission grades

**Event Creation**

When an admin creates an event the following information is given:

* Event Name
* Due date
* student email addresses
* Team size
* Assignment info
	* Assignment Due date
	* Test case input, and expected output
	* Grade case input and expected output

When an assignment is created, a unique token is created for *each* student for *each* assignment, thus if there are 5 assigments within the event then each student will recieve 5 separate tokens to submit there work.

**Event Modification**

When an admin is trying to modify an event they will need to log in, then they will be able to view there open events. When an event is chosen, fields will poulate with the *current* event information. The fields can be changed and then submitted to the RDS.



### Students
Students have very limited abilities, and do not even require an account. Via the frontend students can only do the following:

* Submit token(s) and a `.py` python file for each event they are enrolled in

**Student Submission**
	
Students, can submit code via a unique token for *each* assignment in which they are associated with. Students, can work alone or in groups depending on the event rules. If a student is working within a group one student can submit with all of their group members tokens. If they are working alone, then the student can submit with just their own token.

Once the tokens are submitted the RDS is checked to make sure that all of the tokens submitted are associated with the same assignment. If the tokens validated successfully, then the students can submit their `.py` file. When the python file is submitted, a submission id is generated along with a `json` file containing:

* Submission ID
* admin
* event
* problem
* Tokens

Both the submission json and the python file are placed within an S3 bucket at which point the back end takes over.

[Click here for more info]( {{<ref "Front-End/Website/StudentSubmit">}} )
## Back End
[Click here for a detailed overview of the backend]({{<ref "Back-End">}})

The back end consists of several major parts and AWS serviced including the following:

* *AWS Lambda* function
* *AWS SQS*
* *AWS SNS*
* ec2 instance

Rather than explain each of these services in isloation the backend makes more sense described as a chronological sequence of events after a submission from the front end.
Once the submission is placed within the S3 bucket an AWS lambda function is triggered. This generates a json message for the sqs queue.

Once Within the SQS queue, that is when our own library of code comes in. The EC2 instance pulls from the message from the queue, and retrieves the associated `submission.json` and the students code that goes along with that submission.

Test inputs and there expected outputs are then queried from the database, and files used for comparison are generated. For example we may create a `test.in` for input to the program and a `test.out` to compare the student programs output with.

All of the execution of student code, happens with a docker container in order to sandbox student code. As running unsanitized code directly within an AWS is a large security risk.

Once the student code has finished running and the output has been written to a file, the output is placed back into the S3 bucket. That file can at that point be delivered via email to all students who's tokens are associated with the submission or, we may try to recieve the output on the front end to display via the web app.



