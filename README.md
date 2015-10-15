# SendEmailViaParseCloudCode

(Update 10-12-2015) Here is the "<a href="https://parse.com/apps/quickstart#cloud_code/unix">get started on Cloud Code</a>". This talks about how to set up the Parse cloud code (including email), the integration with Mandrill is also there. You will need two accounts (both are free for small apps): Mandrill and Parse. Just replace the keys in my code with yours. 
 
(Original 11-24-2013)
<strong>The cloud code (javascript) </strong>
refer to this <a href="http://stackoverflow.com/questions/12620318/how-can-my-parse-com-app-send-an-email">stackoverflow thread</a>; and <a href="https://parse.com/docs/cloud_modules_guide#mandrill">this Parse documentation</a>. Note I used ManDrill (free for up to 12,000 mails per month), the main things I noticed is they requires 10 letters password, and the API key can be created in <a href="https://mandrillapp.com/settings/index">Settings</a> (this will be used later, we will use API key instead of SMTP server)

cat ./cloud/main.js
// Use Parse.Cloud.define to define as many cloud functions as you want.
// For example:
Parse.Cloud.define("hello", function(request, response) {
  response.success("Hello world!");
});

Parse.Cloud.define("sendMail", function(request, response) {
var Mandrill = require('mandrill');
Mandrill.initialize('12AkxxxxxxxxxxxxxxrZEg');

Mandrill.sendEmail({
  message: {
    text: request.params.text,
    subject: request.params.subject,
    from_email: request.params.fromEmail,
    from_name: request.params.fromName,
    to: [
      {
        email: request.params.toEmail,
        name: request.params.toName
      }
    ]
  },
  async: true
},{
  success: function(httpResponse) {
    console.log(httpResponse);
    response.success("Email sent!");
  },
  error: function(httpResponse) {
    console.error(httpResponse);
    response.error("Uh oh, something went wrong");
  }
});
});

To test it out, do the "parse deploy" after put the code above in main.js (under cloud dir), then issue this command:
<code>curl -X POST -H "X-Parse-Application-Id: your-parse-application-id" -H "X-Parse-REST-API-Key: your-parse-REST-API-key" -H "Content-Type: application/json" -d '{"toEmail":"major_xu@yahoo.com","toName":"Major Xu","fromEmail":"minjie.xu@gmail.com","fromName":"Minjie Xu","text":"testing ManDrill email","subject":"this is just a test"}' https://api.parse.com/1/functions/sendMail

<strong>Objective-C code</strong>
Refer to Parse documentation <a href="https://parse.com/docs/ios_guide#cloudfunctions/iOS">here</a>. Note this is a simple "Hello World" example. 
[PFCloud callFunctionInBackground:@"hello"
                   withParameters:@{} 
                            block:^(NSString *result, NSError *error) {
   if (!error) {
     // result is @"Hello world!"
   }
}];

My email code is a bit more complex compared to "HelloWorld" :-)

[PFCloud callFunctionInBackground:@"sendMail"
                           withParameters:@{@"toEmail":toEmail,@"toName":@"Minnjie Xu",@"fromEmail":@"uudaddy@gmail.com",@"fromName":@"uudaddy",@"text":@"resetPasswordButtonPressed...",@"subject":@"myNestEgg ~ testing ManDrill email"}
                                    block:^(NSString *result, NSError *error) {
                                        if (!error) {
                                            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Reset Passoword" message:@"Email Sent :-)"
                                                                                           delegate:self cancelButtonTitle:@"OK" otherButtonTitles: nil];
                                            [alert show];
                                            
                                        }
                                    }];
