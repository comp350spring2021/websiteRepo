
## Django Information
**Author:** Arthur Hui  
**Author:** Shardad Shadrou  
**Last Updated:** May 2021  
{{<  toc >}}



We used Django to handle a lot of the website connection and deploy the html files. It is a high level python web frame work with a lot of utility.

- Steps for installing Django on a EC2 server.
    1. Update the server with the command: sudo yum update
    2. Install the latest version of Python on the EC2 server
    3. Install MYSQL or the database language you want to use
    4. Create a Django directory in the server
    5. Use pip to install Django
    6. Install git with the command: sudo yum install git
    7. Clone the git hub branch
    8. Edit the file "settings.py" with VIM or NANO to change the settings
    9. Use the command: pip install django-crispy-forms
    10. You're now done installing Django
- Django has SQLite when it was installed on the server, however we aren't using it. We are exporting the tables models to Amazon Relational Database Service. To connect and migrate the table models which will be referenced as views, follow the steps below.
    - Steps to connect to the RDS from the instance
    1. Find the user name of the RDS you want to connect to
    2. Find the password for the RDS you want to connect to
    3. Get the endpoint of the RDS you want to connect to
    4. Use the command below to access the database. 
        - Replace the `#######` with the RDS endpoint and `$$$$$$`with the user name of the RDS

    ```bash
    mysql -h ####### -P 3306 -u $$$$$$ -p
    ```

## models.py

Designated Django python file to define our abstract data structure, which correlates to the Database structure. Through terminal commands:

`python manage.py makemigrations`

`python manage.py migrate`

We are able to implement our changes into Database, Below are the models we utilized for this project

```jsx
class Event(models.Model):
    eventName = models.CharField(max_length=225)
    admin = models.ForeignKey(Admin, on_delete=models.CASCADE, blank=True, null=True)
    startDate = models.DateField(default=timezone.now)
    endDate = models.DateField(default=timezone.now)
    def __str__(self):
        return self.eventName

class EventRoster(models.Model):
    event = models.ForeignKey(Event, on_delete=models.CASCADE, blank=True)
    studentEmail = models.CharField(max_length=225)
    def __str__(self):
        return self.studentEmail

class Assignment(models.Model):
    assignmentName = models.CharField(max_length=225)
    event = models.ForeignKey(Event, on_delete=models.CASCADE, blank=True)
    startDate = models.DateField(default=timezone.now)
    dueDate = models.DateField(default=timezone.now)
    maxTeamSize = models.IntegerField(default=1)
    def __str__(self):
        return self.assignmentName

class Token(models.Model):
    tokenHash = models.CharField(max_length=225)
    assignment = models.ForeignKey(Assignment, on_delete=models.CASCADE, blank=True)
    student = models.ForeignKey(EventRoster, on_delete=models.CASCADE, blank=True)
    def __str__(self):
        return self.tokenHash

class Submission(models.Model):
    submissionId = models.CharField(max_length=225)
    #assignment = models.ForeignKey(Assignment, on_delete=models.CASCADE, blank=True)
    tokenHash = models.CharField(max_length=225)
    grade = models.IntegerField(default=1)
    def __str__(self):
        return self.tokenHash
```

## views.py

`views.py` handles sending an event, assignments, the test and grade cases, and validating tokens require a few things from AWS Identity and Access Management services. The file that handles sending all of these things is `views.py`. 

In the view function we render a template and pass templates to render 

```jsx
 return render(request,'interpretor/dashboard.html', {'event': event, 'assignment': assignment, 'eventS': eventS})
```

Then in the HTML file, we can load the parameter through {{}}, as such

```jsx
<tr> <!-- Event Name -->
  <th>Event Name</th>
  <td>{{event.eventName}}</td>
</tr>
```

In each view, if there is a form active, we handle POST submissions vs. just render loads as such:

```jsx
if request.method == 'POST':
        data = request.POST
				# Do stuff with data 
```

- To post/put into the S3 bucket, you need five things for it. Without these things it would be hard to access AWS resources from the web server using Django
    1. AWS Access Key
    2. AWS Secret Access Key
    3. The Bucket Name
    4. Default File Storage information
    5. AWS Query String Authentication

### eventCreate()

- The function `eventCreate()` takes in a request and creates a whole event. This event creates the assignment and event and put all the files related to the assignment into the S3 bucket.
    - Things that go into creating the event
        1. Event Name
        2. Start Date and End Date
        3. Assignment Name
        4. Team size
        5. Event
        6. A list of emails
        7. Test case input file
        8. Test case output file
        9. Grade case input file
        10. Grade case output file
    - Before it sends everything to the S3 bucket and to the database through a different file it must first hash all the emails that are provided
    - Using the same event name but a different assignment name would put the assignment in the same event "directory" in the S3 when creating the new event/assignment.
    - For the test and grade cases, the uploaded files should use .in and .out extensions. In addition to the file extension, each line in the each file should be the same thing. The two code snippets follow this rule as in writing the test and grade cases for `test.in` and `test.out`

    ```bash
    Test.in file example

    hello
    good_night
    GO_TO_SLEEP
    ```

    ```bash
    Test.out file example

    hello
    good_night
    GO_TO_SLEEP
    ```

    - Emails are handle in a for loop and hashed out using the md5 hash from the backend

## eventView(eventId)

Function to render assignments for: 

### submitToken(assignmentId)

- Loads submit-token.html template with text inputs for student tokens for submissions
- After Token is validated, a new Submission Record is made fore Every student
- The User is redirected to submitPython() view passing the submission id and assignment ID as parameter

### submitPython(submissionId, assignmentId)

- This function handles sending the python file and a json that contains information about pertains to the event, assignment, and the code. An example of this json can be found in the back end documentation.
- It needs to grab the assignment ID, event name, and assignment name before submitting it to the bucket.
    - The name of the python file will be sent through a MD5 hash before it is sent to the S3 bucket. This will allow for a submission file to have the same starting name but different in the bucket.
    - It will also convert everything to lower camel case before concatenating everything.
    - The assignment is tied to the token that was submitted before hand and the results of the test cases will be emailed back to the student who is associated with the token.

### assignNew(eventId):

- Create a new assignment for a specific Event
- Get event, and assign it as foreign key to new assignment
- Update Grading items

## urls.py

[urls.py](http://urls.py) is they python file responsible for routing user to the appropriate view function which would then render the correct template. Below is example of how is done.

```jsx
path('event/<int:event_id>/', views.eventView, name='eventView'),
```

This particular URL path highlights that what comes after the "event/" section in URL would be the event ID, which in turn would need to be parametrized in the view function, as such:

```jsx
def eventView(request, event_id):
    event = Event.objects.get(id = event_id)
    assignment = Assignment.objects.filter(event = event)
    eventS = True
    #print(assignment)
    #assignment = assignment[0]
    return render(request,'interpretor/dashboard.html', {'event': event, 'assignment': assignment, 'eventS': eventS})
```