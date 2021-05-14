

**Author: Alex Sullivan**  
**Last Update: 4/4/2021**

A student can submit python source code for an assignment via the front end web interface. Students submit one token for each member in their group. When all tokens are submitted, then they need to be validated.

Validation refers to making sure that all of the tokens submitted are associated with the same assignment. We allow very free form groups, and dont try to keep track of who is in which group. All that we try to validate is that the number of tokens is within the specified group size, and that all tokens are associated with the same assignment. We dont want a student to submit the wrong token and have grades mixed for different assignments.

Once the tokens are validated, then the students are allowed to submit their python source code for the assignment. At which point a `json` file is created for the submission, and the `.py` student submission is placed within the s3 bucket along with the `submission.json`. For more information about the `json` submission follow this [link]({{<ref "/Back-End/ExampleSubmissionJSON.md">}}).

file: `submit-python.html`  
**Author:** John Camarillo  
**Last Update:** May 2021  
The Student Submission page allows a student to upload a file to be stored into the S3 bucket.  The `<form>` tag uses a POST method.  Future developments can include the ability to view the results of a student's a group'ssubmission after their assignment is graded.

![](frontEnd/StudentSubmission.png)