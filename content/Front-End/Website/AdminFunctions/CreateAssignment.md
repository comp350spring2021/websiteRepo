 **Author:** Jon Camarillo  
 **Last Updated:** May 2021  


 File name: `assign.html`
 
 One of the requirements of the project was to enable the user to create an assignment.  The snapshot of the page contains the properties of the assignment that will then be stored in the database.  For more info on {% crsf_token%}, refer to Django documentation [here](https://docs.djangoproject.com/en/3.2/ref/csrf/).  Essentially, it is a security measure related to securing `<form>` data in the POST method that we use.

JSON Event output (debug) was implemented for the purpose of displaying the contents of a pseudo-json file with the intention of storing the file in S3 once an assignment was created.  The json file would be used to give our AppServer the ability to pull necessary assignment information from the database for grading purposes.  The process of doing so has yet to be fully implemented.  The `<script>` related to the json file within the html was retained but is commented out.

The `<style>` and `<form>` sections, again, are not too extensive, and modifying and verifying changes to each should not be too difficult.  The file types for our test and grade cases were text files with .in and .out extensions, respectively.  Refer to our Back End Doc for more details about how these files were used to grade submitted assignment.


![](frontEnd/createAssignment.png)