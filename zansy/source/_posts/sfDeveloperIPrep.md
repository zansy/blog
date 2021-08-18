title: Prepare for Salesforce Developer I Credential (update 2021/08/19)
author: zansy
toc: true
tags:
   - Credential
   - Salesforce
categories:
   - 唯有爱与工作不可辜负
date: 2021-08-11 17:32:00
---
Prepare for Salesforce Platform Developer I Credential
<!--more-->
# Exam Outline
- Logic and Process Automation(40%) 24
  
  - Describe the capabilities of the declarative process automation features.
  - Declare variables, constants, methods, and use modifiers and interfaces in Apex.
  - Given a scenario, use and apply Apex control flow statements.
  - Given a scenario, write Apex classes and use Apex interfaces.
  - Given a scenario, write SOSL, SOQL, and DML statements in Apex.
  - Given a use case, write Apex classes and triggers following best practices.
  - Given a scenario, identify the implications of governor limits on Apex transactions.
  - Describe the relationship between Apex transactions, the save order of execution, and the potential for recursion and/or cascading.
  - Implement exception handling in Apex, including custom exceptions as needed.
  - Use programmatic techniques to prevent security vulnerabilities.
  - Given a scenario, use declarative functionality and Apex together to automate business logic.
  - Given a scenario, identify the appropriate publish/subscribe logic for platform events.

- User Interface(23%) 13-14
  - Given a scenario, display content or modify Salesforce data using a Visualforce page and the appropriate controllers or extensions as needed.
  - Incorporate Visualforce pages into Lightning Platform applications.
  - Describe the Lightning Component framework and its benefits.
  - Describe the types of content that can be contained in a Lightning web component.
  - Given a scenario, prevent user interface and data access security vulnerabilities. 
  - Given a scenario, display and use a custom user interface components, including Lightning Components, Flow, and Visualforce.
  - Describe the use cases for Lightning component events and application events.
  - Given a user interface requirement, describe interactions between Apex and various types of page components, including Lightning Components, Flow, Next Best Actions, etc.

- Testing, Debugging, and Deployment(17%) 10-11
  - Write and execute tests for triggers, controllers, classes, flows, and processes using various sources of test data.
  - Describe the use cases for invoking anonymous code and the differences between invoking Apex in execute anonymous vs. unit tests.
  - Describe the Salesforce Developer tools such as Salesforce DX, Salesforce CLI, and Developer Console, and when to use them.
  - Describe how to approach debugging system issues and monitoring flows, processes, and asynchronous and batch jobs, etc.
  - Describe the environments, requirements, and process for deploying code and associated configurations.

- Data Modeling and Management(13%) 7-8
  - Given a set of requirements, determine, create, and access the appropriate data model including objects, fields, and relationships.
  - Describe the capabilities of the various relationship types and custom IDs and the implications of each on record access and development.
  - Describe the options for and considerations when importing and exporting data into development environments.
  - Describe the capabilities and use cases for formula fields and roll-up summary fields.

- Salesforce Fundamentals(7%) 4-5
  - Describe the considerations when developing in a multi-tenant environment.
  - Understand design frameworks, such as MVC architecture and Lightning Component Framework, and how to build applications using both declarative and programmatic tools.
  - Given a scenario, identify common use cases for declarative versus programmatic customizations.

# About the Exam    
 - Content: 60 multiple-choice/multiple-select questions and 5 non-scored questions
 - Time allotted to complete the exam: 105 minutes
 - Passing score: 65%

# Salesforce Fundamentals(7%) 4-5
## Platform Development Basics
### Get Started with Platform Development
Learning Objectives
After completing this unit, you’ll be able to:

- Define the Salesforce platform.
- Describe the kinds of apps you can build with the platform.
- Install the DreamHouse app.

### Develop Without Code
Learning Objectives
After completing this unit, you’ll be able to:

- Describe the benefits of the metadata-driven development model.
- Define and give examples of the no-code and low-code development approaches.

Schema Builder

### Code with Salesforce Languages
Learning Objectives
After completing this unit, you’ll be able to:

- Identify the benefits of Lightning components.
- Describe how Visualforce is used in Lightning Experience.
- Outline the ways Apex is used to support Lightning components and Visualforce.

**Lightning Component Framework**: A UI development framework similar to AngularJS or React.
**Apex**: Salesforce’s proprietary programming language with Java-like syntax.
**Visualforce**: A markup language that lets you create custom Salesforce pages with code that looks a lot like HTML, and optionally can use a powerful combination of Apex and JavaScript.

### Extend the Salesforce Platform
Learning Objectives
After completing this unit, you’ll be able to:

- List the Salesforce APIs.
- Explain how Heroku and Salesforce are related.
- Identify ways Salesforce interacts with IoT and bots.

| API | What you can do with it    |
|---------------------|-------------------|
| SOAP API            | Integrate your org’s data with other applications using standard SOAP protocols.                                                               |
| REST API            | Access objects in your org using standard REST protocols.                                                                                      |
| Metadata API        | Manage customizations in your org and build tools that manage your metadata model.                                                             |
| Tooling API         | Build custom development tools for platform applications.                                                                                      |
| Marketing Cloud API | Expose Marketing Cloud capabilities with the REST API and get comprehensive access to most email functionality with the SOAP API.              |
| Bulk API            | Load, delete, and perform asynchronous queries on large data sets.                                                                             |
| Streaming API       | Send and receive notifications securely and efficiently. Notifications can reflect data changes in your org, or custom events.                 |
| Connect REST API    | Build UI for Commerce, CMS-Managed Content, Experience Cloud Sites, Files, Notifications, Topics, and more.                                    |
| Mobile SDK          | While it’s technically a software development kit, it’s worth including here. Integrate Native or Hybrid mobile apps directly with Salesforce. |

## Apex & .NET Basics
### Map .NET Concepts to the Lightning Platform
Learning Objectives
After completing this unit, you’ll be able to:

- Understand which key features make up the Lightning Platform and the Apex programming language
- Identify similarities and differences between .NET and the Lightning Platform
- Use Developer Console to create your first Apex class
- Use Anonymous Apex to invoke a method from an Apex class

**Create an Apex Class**
Create includes the public method sendMail. It includes a private helper method called inspectResults for inspecting the results of the email send call.
```Java
public with sharing class EmailManager {
    // Public method
    public static void sendMail(String address, String subject, String body) {
        // Create an email message object
        Messaging.SingleEmailMessage mail = new Messaging.SingleEmailMessage();
        String[] toAddresses = new String[] {address};
        mail.setToAddresses(toAddresses);
        mail.setSubject(subject);
        mail.setPlainTextBody(body);
        // Pass this email message to the built-in sendEmail method
        // of the Messaging class
        Messaging.SendEmailResult[] results = Messaging.sendEmail(
            new Messaging.SingleEmailMessage[] { mail });
        // Call a helper method to inspect the returned results.
        inspectResults(results);
    }
    // Helper method
    private static Boolean inspectResults(Messaging.SendEmailResult[] results) {
        Boolean sendResult = true;
        // sendEmail returns an array of result objects.
        // Iterate through the list to inspect results.
        // In this class, the methods send only one email,
        // so we should have only one result.
        for (Messaging.SendEmailResult res : results) {
            if (res.isSuccess()) {
                System.debug('Email sent successfully');
            } else {
                sendResult = false;
                System.debug('The following errors occurred: ' + res.getErrors());                 
            }
        }
        return sendResult;
    }
}   

```

**Invoke a Method**
Because we declared the public sendMail method as static, we can access it without creating an instance of the class. We can do so easily by using Anonymous Apex in Developer Console.

### Understand Execution Context
Learning Objectives
After completing this unit, you’ll be able to:
- Know which methods to use to invoke Apex
- Write a trigger for a Salesforce object
- Observe how execution context works by executing code in Developer Console
- Understand how governor limits impact design patterns
- Understand the importance of working with bulk operations

In the Lightning Platform world, code executes within an **execution context**. In a nutshell, this context represents the time between when the code is executed and when it ends.

**Methods of Invoking Apex**
| Method                         | Description    |
|--------------------------------|----------|
| Database Trigger  | Invoked for a specific event on a custom or standard object. |
| Anonymous Apex      | Code snippets executed on the fly in Dev Console & other tools.  |
| Asynchronous Apex   | Occurs when executing a future or queueable Apex, running a batch job, or scheduling Apex to run at a specified interval. |
| Web Services    | Code that is exposed via SOAP or REST web services.   |
| Email Services                 | Code that is set up to process inbound email.  |
| Visualforce or Lightning Pages | Visualforce controllers and Lightning components can execute Apex code automatically or when a user initiates an action, such as clicking a button. Lightning components can also be executed by Lightning processes and flows. |

**Examining the Execution Log**
The EXECUTION_STARTED event and that the last line is the EXECUTION_FINISHED event. Everything in between is the execution context.

### Use Asynchronous Apex
Learning Objectives
After completing this unit, you’ll be able to:

- Know when to use Asynchronous Apex
- Use future methods to handle a web callout
- Work with the batchable interface to process a large number of records
- Understand the advantages of using the queueable interface when you need to meet in the middle

The following three reasons are usually behind choosing asynchronous programming.

- **Processing a very large number of records.** This reason is unique to the multi-tenanted world of the Lightning Platform where limits rule. The limits associated with asynchronous processes are higher than those with synchronous processes. Therefore, if you need to process thousands or even millions of records, asynchronous processing is your best bet.

- **Making callouts to external web services.** Callouts can take a long time to process, but in the Lightning Platform, triggers can’t make callouts directly.

- **Creating a better and faster user experience** by offloading some processing to asynchronous calls. Why do everything at once? If it can wait, let it.

**Future Methods**
Changing a method from synchronous to asynchronous processing is amazingly easy. Essentially, you just add the `@future` annotation to your method. Other than that, just make sure that the method is static and returns only a void type. For example, to create a method for performing a web service callout, we could do something like this:
```Java
public class MyFutureClass {
    // Include callout=true when making callouts
    @future(callout=true)    
    static void myFutureMethod(Set<Id> ids) {
        // Get the list of contacts in the future method since
        // you cannot pass objects as arguments to future methods
        List<Contact> contacts = [SELECT Id, LastName, FirstName, Email
            FROM Contact WHERE Id IN :ids];
        // Loop through the results and call a method
        // which contains the code to do the actual callout
        for (Contact con: contacts) {
            String response = anotherClass.calloutMethod(con.Id,
                con.FirstName,
                con.LastName,
                con.Email);
            // May want to add some code here to log
            // the response to a custom object
        }
    }
}
```

**Future Limitations**
Here are some limitations to consider before using a future method.

- You can’t track execution because no Apex job ID is returned.
- Parameters must be primitive data types, arrays of primitive data types, or collections of primitive data types. Future methods can’t take objects as arguments.
- You can’t chain future methods and have one call another.

**Batch or Scheduled Apex**
<u>Another long-used asynchronous tool is the batchable interface.</u> The No. 1 reason to use it is if you need to process a large number of records. For example, if you want to clean up or archive up to 50 million records, the batchable interface is your answer. You can even schedule your batches to run at a particular time.

To use it, your class implements the `Database.Batchable` interface. You also define `start(), execute(), and finish() `methods. You can then invoke a batch class using the Database.executeBatch method. For example, the following code creates a batchable class that processes all accounts in an org and then sends an email when it is done.

```Java
global class MyBatchableClass implements
            Database.Batchable<sObject>,
            Database.Stateful {  
    // Used to record the total number of Accounts processed
    global Integer numOfRecs = 0;
    // Used to gather the records that will be passed to the interface method
    // This method will only be called once and will return either a
    // Database.QueryLocator object or an Iterable that contains the records
    // or objects passed to the job.            
    global Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator('SELECT Id, Name FROM Account');                
    }
    // This is where the actual processing occurs as data is chunked into
    // batches and the default batch size is 200.
    global void execute(Database.BatchableContext bc, List<Account> scope) {
        for (Account acc : scope) {
            // Do some processing here
            // and then increment the counter variable
            numOfRecs = numOfRecs + 1;
        }     
    }
    // Used to execute any post-processing that may need to happen. This
    // is called only once and after all the batches have finished.
    global void finish(Database.BatchableContext bc) {
        EmailManager.sendMail('someAddress@somewhere.com',
                              numOfRecs + ' Accounts were processed!',
                              'Meet me at the bar for drinks to celebrate');            
    }
}
```