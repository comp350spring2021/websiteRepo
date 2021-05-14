**Author:** Jon Camarillo  
**Last Update:** May 2021

The Student Submissions (Enter Token) page was used to process student tokens.  The tokens would be sent to a student's email as soon as an assignment was created.  The student would then validate the token, and only once the token was validated, would they be taken to the assignment submission page.

Inside html code are functions that are not displayed in the snapshot below.  These functions enabled the student to add and remove tokens.  It also contained a function that unblocked the button for submission, which signaled that the tokens were validated.  Instead, for the sake of presenting core functionality of our final project, we removed such functions, and instead, validated tokens that we knew would get processed correctly.  The <form> tag uses the POST method to process student tokens.

![](frontEnd/tokenValidation.png)