
# How the back end takes care of expired events
**Author:** Rene Escamilla
**Last Update:** May 2021


It begins in the front end...

When an admin creates an event a `.json` file is generated and uploaded to the database. Each json file will contain the following:
* eventID: A string identifier that is the name of the event.
* assignmentName: A string identifier that is the name of the assignment.
* startDate: A date that signifies the start date of the event.
* endDate: A date that signifies the end date of the event.
* maxTeamSize: A number that sets the max number of people in a team for the event.

This is an example of a json file that was created by the admin on event creation
```
{
  "eventID": "aws_labs",
  "assignmentName": "lab5_containers",
  "startDate": "2021-04-06",
  "endDate": "2021-04-14",
  "maxTeamSize": "4"
}
```

At this point the event has been created and submissions are allowed on the day of the start date.
Submissions before the end date will be processed using the test cases.
Submissions after the end date will be processed using the grade cases, which will then record the results to the database.
By the end date all submissions should be uploaded to the S3 bucket which will allow us to process all expired events.

# How expired events are triggered
Every day at midnight we trigger _runExpiredEvents.py_ using Crontab on Linux. This allows us to process all expired events of the previous date.

The following is the current cronjob format:
```
0 0 * * * /executables/runExpiredEvents.py
```

# Processing Expired Events
When the back end's _runExpiredEvents.py_ is triggered it process all expired events from the database.
The master thread queries the database using the previous day and puts all the events that were expired into a list.
Each event from the list will spawn a client thread with the event passed into its constructor.
Then the master thread calls `start()` on this client thread, which then calls the class method `run()` defined in the EventThread class.

### Client Thread
When the client thread's constructor executes, it populates all instance variables.
The entry point for all client thread's is `run()`.
Since each event may have several assignments and submissions it must accommodate for that.
* Runs a nested loop; one for processing all assignments and the other to process all submissions
* The outer loop must get a list of all the submissions associated with the assignment
    * `submissions/m.soltys/aws_labs/lab5_containers/`
* The inner loop will then process each submission
    * `/submissions/m.soltys/aws_labs/lab5_containers/fc55c0190dde2bc413d8d1e79fb8cca2/`
* Creates a directory using the submission id from each submission
    * `/home/ec2-user/fc55c0190dde2bc413d8d1e79fb8cca2/`
* Changes present working directory into newly created working directory, then retrieves all the files from the S3 bucket
    * `/home/ec2-user/submitter/fc55c0190dde2bc413d8d1e79fb8cca2/fc55c0190dde2bc413d8d1e79fb8cca2.json`
    * `/home/ec2-user/submitter/fc55c0190dde2bc413d8d1e79fb8cca2/fc55c0190dde2bc413d8d1e79fb8cca2.py`
    * `/home/ec2-user/submitter/fc55c0190dde2bc413d8d1e79fb8cca2/grade.in`
    * `/home/ec2-user/submitter/fc55c0190dde2bc413d8d1e79fb8cca2/grade.out`

### Execute submitted code in Python 3.9 container
At this point we have the code to execute, the inputs to run, and the appropriate outputs for comparison.
We are using a python 3 docker image with a custom, executable script `executeSubmission.py` installed that will execute the submitted code against all provided inputs from `grade.in`, and generating the output file.
Our script will now:
* Create a new thread that will in turn execute the appropriate docker run command.
    * `docker run -it --rm --name="subidfc55c0190dde2bc413d8d1e79fb8cca2" -v "$PWD":/usr/src/submitter -w /usr/src/submitter python:3.9 executeSubmission.py fc55c0190dde2bc413d8d1e79fb8cca2`

This will spin up our container and keep it alive until it finishes executing.
The present working directory gets bind-mounted to a newly created directory in the container `/usr/src/submitter`.
Since the default for bind mount is read-write, this allows our script in the container to generate an output that can be read by our main thread.

### Record results to the database and clean house
Now that the container has finished executing, our script's current directory should contain:
* `/home/ec2-user/fc55c0190dde2bc413d8d1e79fb8cca2/fc55c0190dde2bc413d8d1e79fb8cca2.json`
* `/home/ec2-user/fc55c0190dde2bc413d8d1e79fb8cca2/fc55c0190dde2bc413d8d1e79fb8cca2.py`
* `/home/ec2-user/fc55c0190dde2bc413d8d1e79fb8cca2/grade.in`
* `/home/ec2-user/fc55c0190dde2bc413d8d1e79fb8cca2/grade.out`
* `/home/ec2-user/fc55c0190dde2bc413d8d1e79fb8cca2/fc55c0190dde2bc413d8d1e79fb8cca2.out`

Our script will now:
* Open the newly generated file `fc55c0190dde2bc413d8d1e79fb8cca2.out` and sends the results to the database according to the submission id.
* Changes working directories up to the root
* Deletes the directory `/home/ec2-user/fc55c0190dde2bc413d8d1e79fb8cca2/`

Finally the script is now done with the current submission from the list of submissions.
The next submission will then be processed.
Once all submissions are done processing it will move on to the next assignment.
As soon as all assignments are done being processed the client thread is completely done with processing the expired event.

This will be done for each client thread until all expired events are completely processed.
At this point the master thread is complete and `runExpiredEvents.py`will not be triggered again until the next day at midnight, which will then do the same process for the previous day.