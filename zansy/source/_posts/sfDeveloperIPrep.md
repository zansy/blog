title: Prepare for Salesforce Developer I Credential (update 2021/08/21)
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

Learning Objectives
After completing this unit, you’ll be able to:

- Describe the considerations for developing in a multitenant environment.
- Understand design frameworks and how to build applications using declarative and programmatic tools.
- Recognize use cases for declarative versus programmatic customizations.

![fundamentals-1.png](/images/fundamentals-1.png)

![fundamentals-2.png](/images/fundamentals-2.png)

>Which three declarative methods help ensure quality data? Validation Rules, Lookup filters, Workflow Alerts, Page Layouts or Exception Handling 

Validation Rules, Lookup filters, Page Layouts 

>An important consideration when developing in a multitenant environment 

Governor limits 

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

You could then invoke the batch class using anonymous code such as this:
```Java
MyBatchableClass myBatchObject = new MyBatchableClass();
Database.executeBatch(myBatchObject);
```
**Batchable Limitations**

The batchable interface is great, but as with just about everything, consider its limitations.

- Troubleshooting can be troublesome.
- Jobs are queued and subject to server availability, which can sometimes take longer than anticipated.

**Queueable Apex**

It represents the best of future methods and the batchable interface, all rolled up into one super-duper asynchronous tool. Developers forced to use the slower batchable interface to get around limitations of future methods could now return to a tool that made more sense. Queueable Apex provides the following benefits to future methods.
- **Non-primitive types** - Classes can accept parameter variables of non-primitive data types, such as sObjects or custom Apex types.
- **Monitoring** - When you submit your job, a jobId is returned that you can use to identify the job and monitor its progress.
- **Chaining jobs** - You can chain one job to another job by starting a second job from a running job. Chaining jobs is useful for sequential processing.

```Java
public class MyQueueableClass implements Queueable {
    private List<Contact> contacts;
    // Constructor for the class, where we pass
    // in the list of contacts that we want to process
    public MyQueueableClass(List<Contact> myContacts) {
        contacts = myContacts;
    }
    public void execute(QueueableContext context) {
        // Loop through the contacts passed in through
        // the constructor and call a method
        // which contains the code to do the actual callout
        for (Contact con: contacts) {
            String response = anotherClass.calloutMethod(con.Id,
                    con.FirstName,
                    con.LastName,
                    con.Email);
            // May still want to add some code here to log
            // the response to a custom object
        }
    }
}
```
To invoke Queueable Apex, you need something like the following:
```Java
List<Contact> contacts = [SELECT Id, LastName, FirstName, Email
    FROM Contact WHERE Is_Active__c = true];
Id jobId = System.enqueueJob(new MyQueueableClass(contacts));
```

### Debug and Run Diagnostics
Learning Objectives
After completing this unit, you’ll be able to:

- Understand which debugging features are available on the Lightning Platform
- Use the Log Inspector in Developer Console to examine debug logs

you can write to the debug log. That is done with something like the following.
```Java
System.debug('My Debug Message');
```
You can also specify one of the following logging levels.
- NONE
- ERROR
- WARN
- INFO
- DEBUG
- FINE
- FINER
- FINEST

**Use the Log Inspector**

1. From Setup, select Your Name > Developer Console to open Developer Console.
2. Select Debug > Change Log Levels.
3. Click the Add/Change link in General Trace Setting for You.
4. Select INFO as the debug level for all columns.
5. Click Done.
6. Click Done.
7. Select Debug > Perspective Manager.
8. Select All (Predefined) and click Set Default.
9.  Click Yes to change this to your default perspective.
10. Close the Developer Console Perspective window.
11. Select Debug > Open Execute Anonymous Window.
12. Delete the existing code, and insert the following snippet:
```java
System.debug(LoggingLevel.INFO, 'My Info Debug Message');
System.debug(LoggingLevel.FINE, 'My Fine Debug Message');
List<Account> accts = [SELECT Id, Name FROM Account];
for(Account a : accts) {
    System.debug('Account Name: ' + a.name);
    System.debug('Account Id: ' + a.Id);
}
```

13. Make sure that Open Log is selected, and click Execute.
14. Select Debug > Switch Perspective > All (Predefined).
15. Examine the results in the Timeline and Executed Units tabs.
16. Under Execution Log, select the Filter option, and enter FINE. Because we set the debug level t INFO for Apex Code, no results appear.
17. Select Debug > Change Log Levels.
18. Click the Add/Change link in General Trace Setting for You.
19. Change the DebugLevel for ApexCode and Profiling to FINEST.
20. Click Done.
21. Click Done.
22. Select Debug > Open Execute Anonymous Window.
23. Leave t
24. Under Execution Log, select the Filter option, and enter FINE. The filter search is case 1. sensitive. You now see "My Fine Debug Message" displayed. You should also notice a size 1. difference between the two latest logs in the Logs tab.

**Set Checkpoints**

Checkpoints are similar to breakpoints in that they reveal a lot of detailed execution information about a line of code. They just don’t stop execution on that line.

## Aura Components Core Concepts
### Learn About Bundles and the Request Lifecycle
Learning Objectives
After completing this unit, you’ll be able to:
- Describe the difference between a Visualforce page and an Aura component bundle, and how each is represented in resources locally and on Salesforce.
- Describe basic differences in the Visualforce and Aura component request lifecycle, and where component code runs.

**Concept: Page vs. Bundle**

Visualforce pages (and Visualforce components, but let’s set those aside for now) are stored on Salesforce as a single entity, an ApexPage. When you use Salesforce Extensions for Visual Studio Code or another tool to copy your Visualforce pages to your local storage to work on them, an individual Visualforce page is represented as two files in the metadata/pages directory:

- yourPageName.page
- yourPageName.page-meta.xml

The first is the code for the page, and the second is the page metadata (API version, mobile settings, and so on).

Although your page might have dependencies on other artifacts, like an Apex controller or extension, static resources, and so on, they are separate from the page. Your page references them, but doesn’t include them.

**Concept: Server-Side vs. Client-Side**

<u>Classic Visualforce</u> runs “on the server side.” That is, when a page is requested, a Salesforce server processes the markup, and then the resulting HTML is sent to the requesting user’s browser for display. If the page has an expression (those things inside “ {! }” delimiters), it’s evaluated by the server. References to global variables are resolved on the server. If the page accesses a controller property, that’s processed on the server. And so on. For the purposes of this conversation, all of the “framework processing” happens **on the server**. (Note that we’re setting aside using Visualforce + JavaScript remoting as a mere container for your JavaScript app. But then you’re not really using Visualforce, except as a web server.)

The process for <u>Aura components</u> is the opposite. When a component is requested, the component’s resources are packaged up and sent to the requesting user’s browser. Most of this is JavaScript, with some markup providing structure. The browser runs that JavaScript, which renders the resulting HTML and inserts it into the existing “page.” Expression evaluation, global variables, controller properties—those are all resolved **on the client**.

Visualforce Request Cycle
1. User requests a page
1. The server executes the page’s underlying code and sends the resulting HTML to the browser
1. The browser displays the HTML
1. When the user interacts with the page, return to step one

Aura Components Request Cycle
- The user requests an application or a component
- The application or component bundle is returned to the client
- The browser loads the bundle
- The JavaScript application generates the UI
- When the user interacts with the page, the JavaScript application modifies the user interface as needed (return to previous step)

With classic Visualforce that interaction would come with a new, visible page request and reload. With an Aura component, the user’s interaction with client-side JavaScript feels more “live,” more responsive, so when the time comes to load new data, the latency of a server request can suddenly feel slow to the user—even if the overall experience takes less time than a Visualforce request!

You’ll find that Aura components perform well overall. However, without careful design of your server requests and how they affect the user experience, your users might very well think an app is “slow.”

### Learn Architectural Concepts
Learning Objectives
After completing this unit, you’ll be able to:
- Describe how the Visualforce page paradigm and the Aura component paradigm are different.
- Explain the concept of different containers, and how you insert an Aura app or component into a container.

**Concept: Page-by-Page vs. Single-Page Application**

When you design a traditional Visualforce-based app, you usually create a set of pages, and users of the app navigate by moving from one page to another page. You start on a list view page, click a view link to go to a record view page, click an edit button to go to an edit record page, and so on.

Aura components apps are very different. There’s only one “page” for the entire app! We call these “single-page applications” (SPAs). SPAs load a single page and a whole lot of JavaScript. Once that’s loaded, the JavaScript runs and updates the user interface of the “page” from then on.

Instead of navigating from page to page, users navigate from state to state. A state represents a mode that your app is currently in. The list view state, the view record state, and so on.

The SPA knows which components to load for each state, and those components know how to draw, or render, themselves.

**Concept: Page vs. Component**

A Visualforce page is a “large” building block. While you can include it as a “widget” in a page layout, or pull one page into another with the `<apex:include>` tag, you’d only do this with a handful of pages. You don’t put together a collection of “small” pages to build a “medium” page, and then assemble a bunch of those to build a “large” page. Visualforce isn’t designed for that, and if you try you’ll get...problematic behavior.

An Aura component is different. A “large” Aura component can be composed of dozens or even hundreds of smaller components, which themselves can be composed of many even smaller components. The Aura programming model is designed to handle thousands of components assembled together into a single app.

Taking small, fine-grained components and assembling them into a “next level up” component, and then repeating, and repeating, and repeating is the fundamental design process when using Aura components. In software design, this is called composition.

### Learn Coding Concepts
Learning Objectives
After completing this unit, you’ll be able to:
- Map fundamental concepts, such as controllers and requests, from Visualforce to Aura components.
- Avoid the #1 syntax error in Aura components code.
- Describe the advantage of loosely coupled components, and how loose coupling is accomplished.

**Concept: Controllers**

Visualforce controllers run on the server side, while Aura component controllers run on the client side. And sometimes on the server side, too. Visualforce controllers are written in Apex, while Aura component controllers are written in JavaScript. And sometimes also in Apex.

# Data Modeling and Management(13%) 7-8

Learning Objectives
After completing this unit, you’ll be able to:

- Determine the appropriate data model based on specific requirements.
- Describe the capabilities of various relationship types.
- Describe the use cases for formula fields and roll-up summary fields.
- Describe the options and considerations for importing and exporting data into development environments.

![dataModeling-1.png](/images/dataModeling-1.png)

![dataModeling-2.png](/images/dataModeling-2.png)

![dataModeling-3.png](/images/dataModeling-3.png)

![dataModeling-4.png](/images/dataModeling-4.png)

![dataModeling-5.png](/images/dataModeling-5.png)

![dataModeling-6.png](/images/dataModeling-6.png)

![dataModeling-7.png](/images/dataModeling-7.png)

![dataModeling-8.png](/images/dataModeling-8.png)

## Data Modeling 
### Understand Custom & Standard Objects

Learning Objectives
After completing this unit, you’ll be able to:

- Describe the perks of using objects on the Salesforce platform.
- Explain the difference between standard objects and custom objects.
- List the types of custom fields an object can have.

**Standard objects** are objects that are included with Salesforce. Common business objects like Account, Contact, Lead, and Opportunity are all standard objects.

**Custom objects** are objects that you create to store information that’s specific to your company or industry.

**Create a Custom Object**

Object Manager -> Create | Custom Object

**Create a Custom Field**

In the sidebar, click Fields & Relationships.

### Create Object Relationships

Learning Objectives
After completing this unit, you’ll be able to:
- Define the different types of object relationships and their typical use cases.
- Create or modify a lookup relationship.
- Create or modify a master-detail relationship.

Object relationships are a special field type that connects two objects together.

There are two main types of object relationships: lookup and master-detail.
- **Lookup Relationships**. A lookup relationship essentially links two objects together so that you can “look up” one object from the related items on another object. Lookup relationships can be one-to-one or one-to-many. The Account to Contact relationship is one-to-many because a single account can have many related contacts.
- **Master-Detail Relationships**. While lookup relationships are fairly casual, master-detail relationships are a bit tighter. In this type of relationship, one object is the master and another is the detail. The master object controls certain behaviors of the detail object, like who can view the detail’s data. For example, let’s say the owner of a property wanted to take their home off the market. DreamHouse wouldn’t want to keep any offers made on that property. With a master-detail relationship between Property and Offer, you can delete the property and all its associated offers from your system.

### Work with Schema Builder

Learning Objectives
Describe the advantages of using Schema Builder for data modeling.
- Use Schema Builder to create a schema for a given object model.
- Use Schema Builder to add a custom object to your schema.
- Use Schema Builder to add a custom field to your schema.

## Data Management
### Import Data

Learning Objectives
After completing this unit, you'll be able to:
- Describe and compare the different options for importing data into Salesforce.
- List the steps involved in preparing and importing data from a sample .csv file using the Data Import Wizard.

Salesforce offers two main methods for importing data.

- **Data Import Wizard**—this tool, accessible through the Setup menu, lets you import data in common standard objects, such as contacts, leads, accounts, as well as data in custom objects. It can import up to 50,000 records at a time. It provides a simple interface to specify the configuration parameters, data sources, and the field mappings that map the field names in your import file with the field names in Salesforce.
- **Data Loader**—this is a client application that can import up to five million records at a time, of any data type, either from files or a database connection. It can be operated either through the user interface or the command line. In the latter case, you need to specify data sources, field mappings, and other parameters via configuration files. This makes it possible to automate the import process, using API calls.

Use the Data Import Wizard When:
- You need to load less than 50,000 records.
- The objects you need to import are supported by the wizard.
- You don’t need the import process to be automated.

Use Data Loader When:
- You need to load 50,000 to five million records. If you need to load more than 5 million records, we recommend you work with a Salesforce partner or visit the AppExchange for a suitable partner product.
- You need to load into an object that is not supported by the Data Import Wizard.
- You want to schedule regular data loads, such as nightly imports.

### Export Data

Learning Objectives
After completing this unit, you'll be able to:
- Describe and compare the two methods of exporting data from Salesforce.
- Export data manually using the Data Export Service.
- Set up automatic export of data on a weekly or monthly schedule.

Salesforce offers two main methods for exporting data.

- **Data Export Service**—an in-browser service, accessible through the Setup menu. It allows you to export data manually once every 7 days (for weekly export) or 29 days (for monthly export). You can also export data automatically at weekly or monthly intervals. Weekly exports are available in Enterprise, Performance, and Unlimited Editions. In Professional Edition and Developer Edition, you can generate backup files only every 29 days, or automatically at monthly intervals only.
- **Data Loader**—a client application that you must install separately. It can be operated either through the user interface or the command line. The latter option is useful if you want to automate the export process, or use APIs to integrate with another system.

## Formulas and Validations
### Use Formula Fields

Learning Objectives
After completing this unit, you'll be able to:
- Create a custom formula field and use the formula editor.
- Explain why formula fields are useful.
- Outline at least one use case for formula fields.
- Create simple formulas.

You’ve got a lot of data in your organization. Your users need to access and understand this data at a glance without doing a bunch of calculations in their heads. Enter formula fields, the powerful tool that gives you control of how your data is displayed.

Let’s say you wanted to take two numeric fields on a record and divide them to create a percentage. Or perhaps you want to turn a field into a clickable hyperlink for easy access to important information from a record’s page layout. Maybe you want to take two dates and calculate the number of days between them. All these things and more are possible using formula fields.

### Implement Roll-Up Summary Fields

Learning Objectives
After completing this unit, you'll be able to:
- Describe what a roll-up summary field is.
- Create a roll-up summary field.
- Apply field-level security to your roll-up summary field.

**Introduction to Roll-Up Summary Fields**

While formula fields calculate values using fields within a single record, roll-up summary fields calculate values from a set of related records, such as those in a related list. You can create roll-up summary fields that automatically display a value on a master record based on the values of records in a detail record. These detail records must be directly related to the master through a master-detail relationship.

You can perform different types of calculations with roll-up summary fields. You can count the number of detail records related to a master record, or calculate the sum, minimum value, or maximum value of a field in the detail records. For example, you might want:
- A custom account field that calculates the total of all related pending opportunities.
- A custom order field that sums the unit prices of products that contain a description you specify.

**Defining a Roll-Up Summary Field**

Since roll-up summary fields are based on master-detail relationships, it’s useful to review object relationships before creating a roll-up summary field.

**Master-Detail Relationships**

Master-detail relationships closely link objects together so that the master record controls specific behaviors of the detail and subdetail record.

You define a roll-up summary field on the object that is on the master side of a master-detail relationship. For example, you can create a roll-up summary field on the Account object, summarizing related opportunities.

There are a few different types of summaries you can use.

| Type  | Description |
|-------|-------|
| COUNT | Totals the number of related records.     |
| SUM   | Totals the values in the field you select in the Field to Aggregate option. Only number, currency, and percent fields are available.  |
| MIN   | Displays the lowest value of the field you select in the Field to Aggregate option for all directly related records. Only number, currency, percent, date, and date/time fields are available.  |
| MAX   | Displays the highest value of the field you select in the Field to Aggregate option for all directly related records. Only number, currency, percent, date, and date/time fields are available. |

### Create Validation Rules

Learning Objectives
After completing this unit, you'll be able to:
- Describe two use cases for validation rules.
- List the elements of a validation rule.
- Create a validation rule.

**Introduction to Validation Rules**

Validation rules verify that data entered by users in records meets the standards you specify before they can save it. A validation rule can contain a formula or expression that evaluates the data in one or more fields and returns a value of “True” or “False.”

[Examples of Validation Rules](https://trailhead.salesforce.com/content/learn/modules/point_click_business_logic/validation_rules?trailmix_creator_id=strailhead&trailmix_slug=prepare-for-your-salesforce-platform-developer-i-credential)

# Logic and Process Automation(40%) 24

>Which two users can edit a record after it has been locked for approval? 

An administrator and a user who is assigned as the current approver 

>What happens to changes in the result when a Visualforce page calls an Apex controller, which calls another Apex class, and then hits a governor limit? 

Any changes up to the error are rolled back. 

>Which data structure is returned to a developer when performing a SOSL search? 

A list of lists of sObjects 

>A developer writes a before insert trigger. Which context variable can the developer use to access the incoming records in the trigger body? 

The Trigger.new context variable 

>Which method can a developer use to determine, from the DescribeSObjectResult, if the current user will be able to create records for an object in Apex? 

The isCreatable() method 

>In the execution order of triggers, what three steps happen after the before triggers execute, and before the after triggers execute? 

The Before triggers are executed System validation steps are run again and user-defined validation rules are checked. Executes duplicate rules. The record is saved to the database but doesn’t commit yet. Executes all after triggers. 

>True or False: Workflows support publishing platform events. 

False 

![logic-1.png](/images/logic-1.png)

![logic-2.png](/images/logic-2.png)

![logic-3.png](/images/logic-3.png)

![logic-4.png](/images/logic-4.png)

![logic-5.png](/images/logic-5.png)

![logic-6.png](/images/logic-6.png)

![logic-7.png](/images/logic-7.png)

![logic-8.png](/images/logic-8.png)
## Salesforce Flow
### Choose the Right Automation Tool

Learning Objectives
After completing this unit, you'll be able to:

- List the tools included in Salesforce Flow.
- Describe the tools available for automating guided visual experiences.
- Describe and compare the tools available for behind-the-scenes automation.
- Describe the tools available for approval automation.

Salesforce Flow provides declarative process automation for every Salesforce app, experience, and portal.

Included in Salesforce Flow are two point-and-click automation tools: Flow Builder, which lets you build flows, and Process Builder, which lets you edit existing processes.

To sum up the differences:

- Salesforce Flow is the name of the product.
- Flow Builder and Process Builder and are the names of the tools.
- Use Flow Builder to make flows; use Process Builder to refine existing processes.

Flow Builder

![FlowBuilder.jpeg](/images/FlowBuilder.jpeg)

Process Builder 

![ProcessBuilder.png](/images/ProcessBuilder.png)

**Which Automation Tool Is Right for My Use Case?**

| Type of Business Process     | Description     | Available Tools    |
|--------------|-------------------------|-------|
| Guided visual experience     | Business processes that need input from users, whether they’re employees or customers. | Flow Builder  |
| Behind-the-scenes automation | Business processes that get all the necessary data from your Salesforce org or a connected system. In other words, user input isn’t needed. | Flow Builder (Recommended) Process Builder (Use for existing processes) Apex |
| Approval automation  | Business processes that determine how a record, like a time-off request, gets approved by the right stakeholders.   | Approvals |

**Use Flow Builder To Create a New Automated Process**

For all behind-the-scenes automation needs, we recommend that you use Flow Builder. Use Process Builder only if you’re already familiar with using it and you need to edit an existing process. To create a new automated process, use Flow Builder instead.
### Automate Simple Business Processes with Process Builder

Learning Objectives
After completing this unit, you'll be able to:
- List the types of processes that you can build in Process Builder.
- Define the key components used to create a process.
- Build a process that updates the addresses for an account's contacts when the account’s address is updated.

**Process Types**

Process Builder can automate a few kinds of business processes. The main difference is the trigger: when the process starts.

| Type           | Process Starts When                  |
|----------------|--------------------------------------|
| Record Change  | A record is created or edited        |
| Invocable      | It’s called by another process       |
| Platform Event | A platform event message is received |


### Guide Users Through Your Business Processes with Flow Builder

Learning Objectives
After completing this unit, you'll be able to:

- Define a flow and list its key components.
- Describe the types of flow elements.
- Build a flow that creates a record and uploads files.

### Customize How Records Get Approved with Approvals

Learning Objectives
After completing this unit, you'll be able to:
- Define an approval process, and list its key components.
- Describe a business process that can be automated using an approval process.
- Set up an approval process to automatically manage when an account changes from a prospect to a new customer.

## Apex Basics & Database
### Get Started with Apex
Learning Objectives
After completing this unit, you'll be able to:
- Describe the key features of the Apex programming language.
- Save an Apex class and call methods with Anonymous.Apex.
- Use the Developer Console to inspect debug logs.

Apex is a programming language that uses Java-like syntax and acts like database stored procedures. Apex enables developers to add business logic to system events, such as button clicks, updates of related records, and Visualforce pages.

Unlike other object-oriented programming languages, Apex supports:

- Cloud development as Apex is stored, compiled, and executed in the cloud.
- Triggers, which are similar to triggers in database systems.
- Database statements that allow you to make direct database calls and query languages to query and search data.
- Transactions and rollbacks.
- The `global` access modifier, which is more permissive than the `public` modifier and allows access across namespaces and applications.
- Versioning of custom code.

```Java
public class EmailManager {
    // Public method
    public void sendMail(String address, String subject, String body) {
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
        
        // Call a helper method to inspect the returned results
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
            }
            else {
                sendResult = false;
                System.debug('The following errors occurred: ' + res.getErrors());                 
            }
        }
        
        return sendResult;
    }
}
```
**Call a Method to Send an Email**
```Java
EmailManager em = new EmailManager();
em.sendMail('Your email address', 'Trailhead Tutorial', '123 body');
```

**Call a Static Method**

Because the `sendMail()` method in our class doesn’t access class member variables, it doesn’t need to be an instance method. Let’s change it to a static method by adding the `static` keyword to its declaration. Static methods are easier to call than instance methods because they don’t need to be called on an instance of the class but are called directly on the class name.

```Java
public static void sendMail(String address, String subject, String body) {
```
```Java
EmailManager.sendMail('Your email address', 'Trailhead Tutorial', '123 body');
```

### Use sObjects
Learning Objectives
After completing this unit, you'll be able to:
- Describe the relationship between sObjects and Salesforce records.
- Create and use specific sObject variables.
- Cast a generic sObject to a specific sObject.


### Manipulate Records with DML
Learning Objectives
After completing this unit, you'll be able to:
- Use DML to insert, update, and delete records.
- Perform DML statements in bulk.
- Use upsert to either insert or update a record.
- Catch a DML Exception.
- Use a Database method to insert new records with the partial success option and process the results.
- Know when to use DML statements and when to use Database methods.
- Perform DML operations on related records.

Create and modify records in Salesforce by using the **Data Manipulation Language**, abbreviated as **DML**. DML provides a straightforward way to manage records by providing simple statements to **insert, update, merge, delete, and restore** records.

**Upserting Records**

If you have a list containing a mix of new and existing records, you can process insertions and updates to all records in the list by using the `upsert` statement. Upsert helps avoid the creation of duplicate records and can save you time as you don’t have to determine which records exist first.

The upsert statement matches the sObjects with existing records by comparing values of one field. If you don’t specify a field when calling this statement, <u>the upsert statement uses the sObject’s ID to match the sObject with existing records in Salesforce. Alternatively, you can specify a field to use for matching.</u> For custom objects, specify a custom field marked as external ID. For standard objects, you can specify any field that has the idLookup property set to true. For example, the Email field of Contact or User has the idLookup property set. 

**Upsert Syntax**

`upsert sObject | sObject[]`

`upsert sObject | sObject[]​​ field`

**Deleting Records**

You can delete persisted records using the `delete` statement. Deleted records aren’t deleted permanently from Lightning Platform, but they’re placed in the Recycle Bin for 15 days from where they can be restored.

**Database Methods**

Apex contains the built-in Database class, which provides methods that perform DML operations and mirror the DML statement counterparts.
These Database methods are static and are called on the class name.

- `Database.insert()`
- `Database.update()`
- `Database.upsert()`
- `Database.delete()`
- `Database.undelete()`
- `Database.merge()`

Unlike DML statements, Database methods have an optional allOrNone parameter that allows you to specify whether the operation should partially succeed. When this parameter is set to `false`, if errors occur on a partial set of records, the successful records will be committed and errors will be returned for the failed records. Also, no exceptions are thrown with the partial success option.

This is how you call the `insert` method with the allOrNone set to `false`.
```Java
Database.insert(recordList, false);
```
The Database methods return result objects containing success or failure information for each record. For example, insert and update operations each return an array of `Database.SaveResult` objects.
```Java
Database.SaveResult[] results = Database.insert(recordList, false);
```

By default, the allOrNone parameter is `true`, which means that the Database method behaves like its DML statement counterpart and will throw an exception if a failure is encountered.

**Should You Use DML Statements or Database Methods?**
- Use DML statements if you want any error that occurs during bulk DML processing to be thrown as an Apex exception that immediately interrupts control flow (by using `try. . .catch` blocks). This behavior is similar to the way exceptions are handled in most database procedural languages.
- Use Database class methods if you want to allow partial success of a bulk DML operation—if a record fails, the remainder of the DML operation can still succeed. Your application can then inspect the rejected records and possibly retry the operation. When using this form, you can write code that never throws DML exception errors. Instead, your code can use the appropriate results array to judge success or failure. Note that Database methods also include a syntax that supports thrown exceptions, similar to DML statements.

**Updating Related Records**

Fields on related records can't be updated with the same call to the DML operation and require a separate DML call. For example, if inserting a new contact, you can specify the contact's related account record by setting the value of the `AccountId` field. However, you can't change the account's name without updating the account itself with a separate DML call. Similarly, when updating a contact, if you also want to update the contact’s related account, you must make two DML calls. The following example updates a contact and its related account using two `update` statements.
```Java
// Query for the contact, which has been associated with an account.
Contact queriedContact = [SELECT Account.Name 
                          FROM Contact 
                          WHERE FirstName = 'Mario' AND LastName='Ruiz'
                          LIMIT 1];
// Update the contact's phone number
queriedContact.Phone = '(415)555-1213';
// Update the related account industry
queriedContact.Account.Industry = 'Technology';
// Make two separate calls 
// 1. This call is to update the contact's phone.
update queriedContact;
// 2. This call is to update the related account's Industry field.
update queriedContact.Account; 
```

**About Transactions**

DML operations execute within a **transaction**. All DML operations in a transaction either complete successfully, or if an error occurs in one operation, the entire transaction is rolled back and no data is committed to the database. The boundary of a transaction can be a trigger, a class method, an anonymous block of code, an Apex page, or a custom Web service method. For example, if a trigger or class creates two accounts and updates one contact, and the contact update fails because of a validation rule failure, the entire transaction rolls back and none of the accounts are persisted in Salesforce.

### Write SOQL Queries
Learning Objectives
After completing this unit, you'll be able to:
- Write SOQL queries in Apex.
- Execute SOQL queries by using the Query Editor in the Developer Console.
- Execute SOQL queries embedded in Apex by using Anonymous Apex.
- Query related records.

**Write SOQL Queries**

To read a record from Salesforce, you must write a query. Salesforce provides the Salesforce Object Query Language, or SOQL in short, that you can use to read saved records. SOQL is similar to the standard SQL language but is customized for the Lightning Platform.

Because Apex has direct access to Salesforce records that are stored in the database, you can embed SOQL queries in your Apex code and get results in a straightforward fashion. When SOQL is embedded in Apex, it is referred to as **inline SOQL**.
**Filtering Query Results with Conditions**
```SQL
SELECT Name,Phone FROM Account WHERE (Name='SFDC Computing' OR (NumberOfEmployees>25 AND BillingCity='Los Angeles'))
```
**Ordering Query Results**

The default sort order is in alphabetical order, specified as ASC.
```SQL
SELECT Name,Phone FROM Account ORDER BY Name ASC
SELECT Name,Phone FROM Account ORDER BY Name DESC
```

**Limiting the Number of Records Returned**
```SQL
Account oneAccountOnly = [SELECT Name,Phone FROM Account LIMIT 1];
```

**Accessing Variables in SOQL Queries**

SOQL statements in Apex can reference Apex code variables and expressions if they are preceded by a **colon (:)**. The use of a local variable within a SOQL statement is called a bind.

This example shows how to use the `targetDepartment` variable in the WHERE clause.
```Java
String targetDepartment = 'Wingo';
Contact[] techContacts = [SELECT FirstName,LastName 
                          FROM Contact WHERE Department=:targetDepartment];
```

**Querying Related Records**

Records in Salesforce can be linked to each other through relationships: lookup relationships or master-detail relationships.

To get child records related to a parent record, add an inner query for the child records. The FROM clause of the inner query runs against the relationship name, rather than a Salesforce object name. This example contains an inner query to get all contacts that are associated with each returned account. The FROM clause specifies the Contacts relationship, which is a default relationship on Account that links accounts and contacts.

```SQL
SELECT Name, (SELECT LastName FROM Contacts) FROM Account WHERE Name = 'SFDC Computing'
```

**Querying Record in Batches By Using SOQL For Loops**

With a SOQL for loop, you can include a SOQL query within a for loop. The results of a SOQL query can be iterated over within the loop. SOQL for loops use a different method for retrieving records—records are retrieved using efficient chunking with calls to the query and queryMore methods of the SOAP API. By using SOQL for loops, you can avoid hitting the heap size limit.

```Java
insert new Account[]{new Account(Name = 'for loop 1'), 
                     new Account(Name = 'for loop 2'), 
                     new Account(Name = 'for loop 3')};
// The sObject list format executes the for loop once per returned batch
// of records
Integer i=0;
Integer j=0;
for (Account[] tmp : [SELECT Id FROM Account WHERE Name LIKE 'for loop _']) {
    j = tmp.size();
    i++;
}
System.assertEquals(3, j); // The list should have contained the three accounts
                       // named 'yyy'
System.assertEquals(1, i); // Since a single batch can hold up to 200 records and,
                       // only three records should have been returned, the 
                       // loop should have executed only once
```

### Write SOSL Queries
Learning Objectives
After completing this unit, you'll be able to:
- Describe the differences between SOSL and SOQL.
- Search for fields across multiple objects using SOSL queries.
- Execute SOSL queries by using the Query Editor in the Developer Console.

**Salesforce Object Search Language (SOSL)** is a Salesforce search language that is used to perform text searches in records. Use SOSL to search fields across multiple standard and custom object records in Salesforce. SOSL is similar to Apache Lucene.
Adding SOSL queries to Apex is simple—you can embed SOSL queries directly in your Apex code. When SOSL is embedded in Apex, it is referred to as **inline SOSL**.

**Differences and Similarities Between SOQL and SOSL**

Like SOQL, SOSL allows you to search your organization’s records for specific information. Unlike SOQL, which can only query one standard or custom object at a time, a single SOSL query can search all objects.

Another difference is that SOSL matches fields based on a word match while SOQL performs an exact match by default (when not using wildcards). For example, searching for 'Digital' in SOSL returns records whose field values are 'Digital' or 'The Digital Company', but SOQL returns only records with field values of 'Digital'.

SOQL and SOSL are two separate languages with different syntax. Each language has a distinct use case:

- Use SOQL to retrieve records for a single object.
- Use SOSL to search fields across multiple objects. SOSL queries can search most text fields on an object.

**Basic SOSL Syntax**
SOSL allows you to specify the following search criteria:
- Text expression (single word or a phrase) to search for
- Scope of fields to search
- List of objects and fields to retrieve
- Conditions for selecting rows in the source objects

**SearchGroup is optional.** It is the scope of the fields to search. If not specified, the default search scope is all fields. SearchGroup can take one of the following values.

- `ALL FIELDS`
- `NAME FIELDS`
- `EMAIL FIELDS`
- `PHONE FIELDS`
- `SIDEBAR FIELDS`

**ObjectsAndFields is optional.** It is the information to return in the search result—a list of one or more sObjects and, within each sObject, list of one or more fields, with optional values to filter against. If not specified, the search results contain the IDs of all objects found.

```Java
FIND 'SearchQuery' [IN SearchGroup] [RETURNING ObjectsAndFields]

FIND {Wingo} IN ALL FIELDS RETURNING Account(Name), Contact(FirstName,LastName,Department)
```

**SOSL Apex Example**

This example shows how to run a SOSL query in Apex. This SOSL query combines two search terms by using the OR logical operator—it searches for Wingo or SFDC in any field. 
```Java
List<List<sObject>> searchList = [FIND 'Wingo OR SFDC' IN ALL FIELDS 
                   RETURNING Account(Name),Contact(FirstName,LastName,Department)];
Account[] searchAccounts = (Account[])searchList[0];
Contact[] searchContacts = (Contact[])searchList[1];
System.debug('Found the following accounts.');
for (Account a : searchAccounts) {
    System.debug(a.Name);
}
System.debug('Found the following contacts.');
for (Contact c : searchContacts) {
    System.debug(c.LastName + ', ' + c.FirstName);
}
```

## Apex Triggers
### Get Started with Apex Triggers
Learning Objectives
After completing this unit, you'll be able to:
- Write a trigger for a Salesforce object.
- Use trigger context variables.
- Call a class method from a trigger.
- Use the sObject `addError()` method in a trigger to restrict save operations.

Apex triggers enable you to perform custom actions before or after events to records in Salesforce, such as insertions, updates, or deletions. Just like database systems support triggers, Apex provides trigger support for managing records.

Typically, you use triggers to perform operations based on specific conditions, to modify related records or restrict certain operations from happening. You can use triggers to do anything you can do in Apex, including executing SOQL and DML or calling custom Apex methods.

**Trigger Syntax**
```Java
trigger TriggerName on ObjectName (trigger_events) {
   code_block
}
```
To execute a trigger before or after insert, update, delete, and undelete operations, specify multiple trigger events in a comma-separated list. The events you can specify are:
- `before insert`
- `before update`
- `before delete`
- `after insert`
- `after update`
- `after delete`
- `after undelete`

**Types of Triggers**

There are two types of triggers.

- **Before triggers** are used to update or validate record values before they’re saved to the database.
- **After triggers** are used to access field values that are set by the system (such as a record's `Id` or `LastModifiedDate` field), and to affect changes in other records. The records that fire the after trigger are read-only.

**Using Context Variables**
To access the records that caused the trigger to fire, use context variables. For example, `Trigger.New` contains all the records that were inserted in insert or update triggers. `Trigger.Old` provides the old version of sObjects before they were updated in update triggers, or a list of deleted sObjects in delete triggers. Triggers can fire when one record is inserted, or when many records are inserted in bulk via the API or Apex. Therefore, context variables, such as `Trigger.New`, can contain only one record or multiple records. You can iterate over `Trigger.New` to get each individual sObject.

Some other context variables return a Boolean value to indicate whether the trigger was fired due to an update or some other event. The following table is a comprehensive list of all context variables available for triggers.

| Variable      | Usage     |
|---------------|----------|
| isExecuting   | Returns true if the current context for the Apex code is a trigger, not a Visualforce page, a Web service, or an executeanonymous() API call.   |
| isInsert      | Returns true if this trigger was fired due to an insert operation, from the Salesforce user interface, Apex, or the API.   |
| isUpdate      | Returns true if this trigger was fired due to an update operation, from the Salesforce user interface, Apex, or the API.  |
| isDelete      | Returns true if this trigger was fired due to a delete operation, from the Salesforce user interface, Apex, or the API.   |
| isBefore      | Returns true if this trigger was fired before any record was saved.          |
| isAfter       | Returns true if this trigger was fired after all records were saved.       |
| isUndelete    | Returns true if this trigger was fired after a record is recovered from the Recycle Bin. This recovery can occur after an undelete operation from the Salesforce user interface, Apex, or the API.   |
| new           | Returns a list of the new versions of the sObject records. This sObject list is only available in insert, update, and undelete triggers, and the records can only be modified in before triggers.  |
| newMap        | A map of IDs to the new versions of the sObject records. This map is only available in before update, after insert, after update, and after undelete triggers. |
| old           | Returns a list of the old versions of the sObject records. This sObject list is only available in update and delete triggers.  |
| oldMap        | A map of IDs to the old versions of the sObject records. This map is only available in update and delete triggers.  |
| operationType | Returns an enum of type System.TriggerOperation corresponding to the current operation. Possible values of the System.TriggerOperation enum are: BEFORE_INSERT, BEFORE_UPDATE, BEFORE_DELETE,AFTER_INSERT, AFTER_UPDATE, AFTER_DELETE, and AFTER_UNDELETE. If you vary your programming logic based on different trigger types, consider using the switch statement with different permutations of unique trigger execution enum states. |
| size          | The total number of records in a trigger invocation, both old and new. |

**Using Trigger Exceptions**

You sometimes need to add restrictions on certain database operations, such as preventing records from being saved when certain conditions are met. To prevent saving records in a trigger, call the `addError()` method on the sObject in question. The `addError()` method throws a fatal error inside a trigger. The error message is displayed in the user interface and is logged.

The following trigger prevents the deletion of an account if it has related opportunities. By default, deleting an account causes a cascade delete of all its related records. This trigger prevents the cascade delete of opportunities.

```Java
trigger AccountDeletion on Account (before delete) {
   
    // Prevent the deletion of accounts if they have related opportunities.
    for (Account a : [SELECT Id FROM Account
                     WHERE Id IN (SELECT AccountId FROM Opportunity) AND
                     Id IN :Trigger.old]) {
        Trigger.oldMap.get(a.Id).addError(
            'Cannot delete account with related opportunities.');
    }
    
}
```

### Bulk Apex Triggers
Learning Objectives
After completing this unit, you'll be able to:
- Write triggers that operate on collections of sObjects.
- Write triggers that perform efficient SOQL and DML operations.

Apex triggers are optimized to operate in bulk. We recommend using bulk design patterns for processing records in triggers. When you use bulk design patterns, your triggers have better performance, consume less server resources, and are less likely to exceed platform limits.

**Performing Bulk SOQL**

**The following trigger shows a SOQL query pattern to avoid**. The example makes a SOQL query inside a for loop to get the related opportunities for each account, which runs once for each Account sObject in `Trigger.New`. If you have a large list of accounts, a SOQL query inside a for loop could result in too many SOQL queries. 

```Java
trigger SoqlTriggerNotBulk on Account(after update) {   
    for(Account a : Trigger.New) {
        // Get child records for each account
        // Inefficient SOQL query as it runs once for each account!
        Opportunity[] opps = [SELECT Id,Name,CloseDate 
                             FROM Opportunity WHERE AccountId=:a.Id];
        
        // Do some other processing
    }
}
```
**recommended approach**

```Java
trigger SoqlTriggerBulk on Account(after update) {  
    // Perform SOQL query once.    
    // Get the accounts and their related opportunities.
    List<Account> acctsWithOpps = 
        [SELECT Id,(SELECT Id,Name,CloseDate FROM Opportunities) 
         FROM Account WHERE Id IN :Trigger.New];
  
    // Iterate over the returned accounts    
    for(Account a : acctsWithOpps) { 
        Opportunity[] relatedOpps = a.Opportunities;  
        // Do some other processing
    }
}
```
**OR**
```Java
trigger SoqlTriggerBulk on Account(after update) {  
    // Perform SOQL query once.    
    // Get the related opportunities for the accounts in this trigger.
    List<Opportunity> relatedOpps = [SELECT Id,Name,CloseDate FROM Opportunity
        WHERE AccountId IN :Trigger.New];
  
    // Iterate over the related opportunities    
    for(Opportunity opp : relatedOpps) { 
        // Do some other processing
    }
}
```
**Performing Bulk DML**

When performing DML calls in a trigger or in a class, perform DML calls on a collection of sObjects when possible. Performing DML on each sObject individually uses resources inefficiently. The Apex runtime allows up to 150 DML calls in one transaction.

In this example, the update statement is **inefficiently** called once for each opportunity. 
```java
trigger DmlTriggerNotBulk on Account(after update) {   
    // Get the related opportunities for the accounts in this trigger.        
    List<Opportunity> relatedOpps = [SELECT Id,Name,Probability FROM Opportunity
        WHERE AccountId IN :Trigger.New];          
    // Iterate over the related opportunities
    for(Opportunity opp : relatedOpps) {      
        // Update the description when probability is greater 
        // than 50% but less than 100% 
        if ((opp.Probability >= 50) && (opp.Probability < 100)) {
            opp.Description = 'New description for opportunity.';
            // Update once for each opportunity -- not efficient!
            update opp;
        }
    }    
}
```

**recommended approach**

```Java
trigger DmlTriggerBulk on Account(after update) {   
    // Get the related opportunities for the accounts in this trigger.        
    List<Opportunity> relatedOpps = [SELECT Id,Name,Probability FROM Opportunity
        WHERE AccountId IN :Trigger.New];
          
    List<Opportunity> oppsToUpdate = new List<Opportunity>();
    // Iterate over the related opportunities
    for(Opportunity opp : relatedOpps) {      
        // Update the description when probability is greater 
        // than 50% but less than 100% 
        if ((opp.Probability >= 50) && (opp.Probability < 100)) {
            opp.Description = 'New description for opportunity.';
            oppsToUpdate.add(opp);
        }
    }
    
    // Perform DML on a collection
    update oppsToUpdate;
}
```

## Database & .NET Basics
### Move from SQL to SOQL
Learning Objectives
After completing this unit, you’ll be able to:

- Understand the benefits of the Force.com objects.
- Identify what is similar and different between SQL and SOQL.
- Build a simple SOQL statement using Workbench.
- Build more complicated relationship queries.
- Build aggregated queries.

**What is similar and different between SQL and SOQL**

The first thing to know is that although they’re both called query languages, <u>SOQL is used only to perform queries with the SELECT statement.</u> SOQL has no equivalent INSERT, UPDATE, and DELETE statements. In the Salesforce world, data manipulation is handled using a set of methods known as **DML (Data Manipulation Language)**.

One big difference you’ll notice right away is that <u>SOQL has no such thing as `SELECT *`.</u> Because SOQL returns Salesforce data, and that data lives in a multi-tenanted environment where everyone is kind of "sharing the database,” a wildcard character like * is asking for trouble. 

<u>In SOQL, you specify each field name to return. </u>

**A Different Kind of Join**

 Salesforce does things a little differently when it comes to combining objects, or tables, as you’re used to thinking of them. Rather than combine tables with a JOIN clause, you write what is known as relationship queries.

 Salesforce uses a parent-child relationship to combine two objects. Just like in SQL, SOQL uses a foreign key to relate these two objects, but in SOQL, the query syntax is different. 

 SOQL has two basic relationship query types that you need to remember:

- Child-to-parent
- Parent-to-children

**Write a Child-to-Parent Query**
```SQL
SELECT FirstName, LastName, Account.Name FROM Contact
```

**Writing Parent-to-Children Queries**
```SQL
SELECT Name, (Select FirstName, LastName FROM Contacts) FROM Account
```

**SOQL Aggregate Functions**

| Function            | Description      |
|--------------------------|---------------------|
| AVG()     | Returns the average value of a numeric field.           |
| COUNT() and COUNT(fieldName) and COUNT_DISTINCT() | Returns the number of rows matching the query criteria. |
| MIN()        | Returns the minimum value of a field.                   |
| MAX()         | Returns the maximum value of a field.                   |
| SUM()   | Returns the total sum of a numeric field.               |

In addition to the [GROUP BY clause](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_groupby.htm), SOQL offers other grouping clauses, such as GROUP BY ROLLUP, GROUP BY CUBE, and GROUPING. These clauses are useful for inspecting the results when a query returns values from several related tables. GROUP BY CUBE is a neat little clause that lets you add subtotals for all combinations of a grouped fields in the query results. To learn more about these clauses, look at the GROUP BY document in Resources.

Aggregate functions also support an optional [HAVING clause](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_having.htm) that is similar to the HAVING Clause in SQL Server. It basically lets you filter the results that an aggregate function returns. 

### Write SOSL Queries
Learning Objectives
After completing this unit, you’ll be able to:

- Understand how SOSL differs from other full-text search alternatives.
- Identify the basic SOSL syntax.
- Describe the differences between SOSL and SOQL.
- Build a SOSL query that searches across multiple sObjects.

 To perform text-based queries across multiple sObjects, you can use SOSL (Salesforce Object Search Language), Salesforce’s option for full-text searching.

 SOSL uses a simpler search syntax that doesn’t use the SOQL SELECT keyword. Instead, it uses the FIND keyword. A basic query looks like the following:

```Java
FIND {"grand*"} IN ALL FIELDS RETURNING Account(Name), Contact(LastName, FirstName, Email)
```
You can also include wildcard characters, which our example does, such as these two:
```Java
* matches zero or more characters at the middle or end of the search term
? matches only one character at the middle or end of the search term
```

### Write Efficient Queries
Learning Objectives
After completing this unit, you’ll understand:

- How the Force.com Query Optimizer optimizes query performance.
- The impact that using selective queries has on query performance.
- How to use the Query Plan tool to evaluate search queries.

Always strive to avoid creating queries with non-deterministic formula fields. Formula fields are custom fields that allow you to dynamically calculate a field’s value at runtime. These types of fields tend to be popular in most orgs and can easily be overused. A formula field is considered non-deterministic when its value varies over time.

**Index Selectivity Exceptions**
Using an indexed field in your query doesn’t always make it golden. You can do things in your queries to make them non-selective and thus prone to the dreaded full table scan. When building your queries always strive to avoid these things.

**Querying for null rows**—Queries that look for records in which the field is empty or null. For example:
```SQL
SELECT Id, Name FROM Account WHERE Custom_Field__c = null
```
**Negative filter operators**—Using operators such as !=, NOT LIKE, or EXCLUDES in your queries. For example:
```SQL
SELECT CaseNumber FROM Case WHERE Status != ‘New’
```
**Leading wildcards**—Queries that use a leading wildcard, such as this:
```SQL
SELECT Id, LastName, FirstName FROM Contact WHERE LastName LIKE ‘%smi%’
```
**Text fields with comparison operators**—Using comparison operators, such as >, <, >=, or <=, with text-based fields. For example:
```SQL
SELECT AccountId, Amount FROM Opportunity WHERE Order_Number__c > 10
```

### Modify Records with DML
Learning Objectives
After completing this unit, you’ll:

- Understand how to use the Query Grid in Developer Console to manipulate data records directly.
- Understand how to use DML to persist data to the database.
- Be able to identify two ways to write DML commands.

Salesforce uses SOQL to query data, but when it comes to managing the data, DML is used instead.

**DML Statements**

| Statement | Description      |
|-----------|------------------|
| insert    | Inserts a new record.    |
| update    | Updates an existing record.   |
| upsert    | This is a special operation that creates new records and updates records within a single statement. It uses a specified field to determine the presence of existing objects, or the ID field if no field is specified. |
| delete    | Deletes an existing record.   |
| undelete  | Restores one or more existing records from the recycle bin. |
| merge     | This is another special operation that can merge up to three records of the same object type into one of the records. It will then delete the other records and reparent any related records.   |

**Two Ways to Execute DML**

- **Using the statement itself**
```SQL
// Add Account
Account acct = new Account(
    Name='Test Account',
    Phone='(225)555-8989',
    NumberOfEmployees=10,
    BillingCity='Baton Rouge');
insert acct;
```

- **Using Database class methods**: The advantage of using the Database class method is that you have access to the optional `allOrNone` parameter to specify whether the operation can partially succeed. When this parameter is set to false, if an error occurs on a partial set of records, the successful records are committed. Errors for the failed records are returned. Also, no exceptions are thrown with the partial success option.
```Java
// Add Account
Account acct = new Account(
    Name='Test Account',
    Phone='(225)555-8989',
    NumberOfEmployees=10,
    BillingCity='Baton Rouge');
Database.SaveResult[] results = Database.insert(acct, false);
```

## Search Solution Basics
### Choose the Right Search Solution
Learning Objectives
After completing this unit, you’ll be able to:

- Explain when to create a customized search solution.
- Describe the difference between SOSL and SOQL.
- Identify which API protocols are available for search.

In general, you need a custom search solution when your org uses a custom UI instead of the standard Salesforce UI. Examples of home grown UIs are a customer-facing knowledge base or an internal product data site for your employees. Just a word of caution: Building a custom user interface isn’t for everyone and requires extra work. The good news is that a custom search still allows you to take advantage of some of the advanced features of Salesforce search. So, if your company has decided to build a custom UI and needs a custom search, this is the module for you.

Keep in mind two main APIs. We’ll talk about two more bonus APIs a little later. Think of it as getting free sides with your main meal.

- Salesforce Object Query Language (SOQL)
- Salesforce Object Search Language (SOSL)

Both SOQL and SOSL format text queries in a given API. But that’s where the similarities end. A SOQL query is the equivalent of a SELECT SQL statement and searches the org database. On the other hand, SOSL is a programmatic way of performing a text-based search against the search index. SOSL uses all the super cool, previously mentioned features of the search index.

SOQL and SOSL also have different syntaxes. 

**Use SOQL** when you know in which objects or fields the data resides and you want to:

- Retrieve data from a single object or from multiple objects that are related to one another.
- Count the number of records that meet specified criteria.
- Sort results as part of the query.
- Retrieve data from number, date, or checkbox fields.

**Use SOSL** when you don’t know in which object or field the data resides and you want to:

- Retrieve data for a specific term that you know exists within a field. Because SOSL can tokenize multiple terms within a field and build a search index from this, SOSL searches are faster and can return more relevant results.
- Retrieve multiple objects and fields efficiently, and the objects might or might not be related to one another.
- Retrieve data for a particular division in an organization using the divisions feature, and you want to find it in the most efficient way possible.

### Build Search for Common Use Cases
Learning Objectives
After completing this unit, you’ll be able to:

- Understand how to search within a single object.
- Understand how to search within multiple objects.
- Understand how to search within a custom object.

**Search Within a Single Object**
To search within a single object using SOSL, simply specify that object in the request. It’s just that easy.
if the user wants to find the March 2016 email campaign, the request looks like:
```SQL
FIND {march 2016 email} RETURNING Campaign
```

**Search Within Multiple Objects**
Luckily, adding another object is super simple in SOSL. Just add a comma-separated list.
```SQL
FIND {term} RETURNING ObjectTypeName1, ObjectTypeName2, ObjectTypeNameYouGetTheIdea
```

**Search Within Custom Objects**
For the internal product website you’re building, you’re adding a search box that only searches the Merchandise custom object. There’s no fancy way to specify custom objects in a SOSL search. Include the sObject name like any other standard object, and append a `__c` suffix.
```SQL
FIND {pink hi\-top} RETURNING Merchandise__c
```
### Optimize Search Results
Learning Objectives
After completing this unit, you’ll be able to:

- Describe how to create efficient text searches that return more targeted search results.
- Describe how to create auto-suggest functionality for searches.
- List more actions that admins can take to enhance search results for users.

How do you combat sluggish searches? By defining lean, mean, search-querying machines. Two basic strategies are at play here.

- Limit which data you’re searching through
- Limit which data you’re returning

| I want to...         | SOSL                | SOQL                |
|----------------------|---------------------|---------------------|
| Limit the data that is searched                 | IN SearchGroup      | WHERE               |
| Specify the data to be returned in the response | Returning FieldSpec | SELECT              |
| Sort results                                    | ORDER BYLIMITOFFSET | ORDER BYLIMITOFFSET |
| Filter by data category                         | WITH DATA CATEGORY  | WITH DATA CATEGORY  |

Let’s try out some WITH statements, which filter records by certain predefined fields. By prefiltering results, you return fewer results and improve performance. Plus, users don’t need to wade through as many results. You might be asking: What types of WITH filters does SOSL offer? Well, here they are:

| Resource           | Example                                                                                                                                                                  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| WITH DIVISION      | `FIND {Cloud Kicks} RETURNING Account (Name, Industry)     WITH DIVISION = 'Global' `    |
| WITH DATA CATEGORY | `FIND {race} RETURNING KnowledgeArticleVersion     (Id, Title WHERE PublishStatus='online' and language='en_US')     WITH DATA CATEGORY Location__c AT America__c` |
| WITH NETWORK       | `FIND {first place} RETURNING User (Id, Name), FeedItem (id, ParentId WHERE CreatedDate = THIS_YEAR Order by CreatedDate DESC) WITH NETWORK = '00000000000001'` |
| WITH PRICEBOOK     | `Find {shoe} RETURNING Product2 WITH PricebookId = '01sxx0000002MffAAE' ` |

**Display Suggested Results**

- Search Suggested Records—Returns a list of suggested records whose names match the user’s search string. The suggestions resource provides a shortcut for users to navigate directly to likely relevant records, before performing a full search.
- Search Suggested Article Title Matches—Returns a list of Salesforce Knowledge articles whose titles match the user’s search query string. Provides a shortcut to navigate directly to likely relevant articles before the user performs a search.
- SObject Suggested Articles for Case—Returns a list of suggested Salesforce Knowledge articles for a case.

**Work with Admins**

The first step is setting up and optimizing synonym groups, which have words or phrases that are treated equally in searches.(in the Quick Find box, then select `Synonyms`.)

the next step is promoted search terms for Knowledge articles.

## [Triggers and Order of Execution](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers_order_of_execution.htm)

- Runs system validation check.
- Executes all before triggers.
- Runs most system validation steps again, such as verifying that all required fields have a non-null value, and runs any user-defined validation rules. The only system validation that Salesforce doesn't run a second time (when the request comes from a standard UI edit page) is the enforcement of layout-specific rules.
- Executes duplicate rules. If the duplicate rule identifies the record as a duplicate and uses the block action, the record is not saved and no further steps, such as after triggers and workflow rules, are taken.
- Saves the record to the database, but doesn't commit yet.
- Executes all after triggers.
- Executes assignment rules.
- Executes auto-response rules.
- Executes workflow rules.
- If there are workflow field updates, updates the record again.
- If the record was updated with workflow field updates, fires before update triggers and after update triggers one more time (and only one more time), in addition to standard validations. Custom validation rules, duplicate rules, and escalation rules are not run again.

## Platform Events Basics
### Understand Event-Driven Software Architecture

Learning Objectives
After completing this unit, you’ll be able to:
- List the components of event-based software architecture.
- Explain the benefits of an event-driven software architecture.
- Describe use cases for the Platform Events feature.
- Describe the characteristics of a platform event.

**Understand Event-Driven Software Architecture**
The paradigm of event-based communication revolves around a publisher-subscriber model—a sender broadcasts a message that one or more receivers capture. It’s like radio transmission—a transmitter tower broadcasts a radio signal, and receivers get the signal if they’re tuned to the right frequency.

Much like radio transmission, event-based communication flows from the sender to the receiver. Events get sent whether or not receivers are listening, and receivers don’t acknowledge it when they receive an event. Event-based communication takes place in real-time—or, more accurately, in near-real time.

**Components of Event-Driven Systems**

**Event**
A change in state that is meaningful in a business process. For example, placement of a purchase order is a meaningful event, because the order fulfillment center expects to receive a notification before processing an order.

**Event message**
A message that contains data about the event. Also known as an event notification. For example, an event message can be a notification about an order placement containing information about the order.

**Event producer**
The publisher of an event message. For example, an order placement app.

**Event channel**
A stream of events on which an event producer sends event messages and event consumers read those messages. For platform events, the channel is for one platform event and groups all event messages for that platform event.

**Event consumer**
A subscriber to a channel that receives messages from the channel. For example, an order fulfillment app that is notified of new orders.

**Event bus**
A communication and storage service that enables event streaming using the publish-subscribe model. The event bus enables the retrieval of stored event messages at any time during the retention window.

Unlike request-response communication models, software architecture built on an event-driven model decouples event producers from event consumers, thereby simplifying the communication model in connected systems. No requests need to be made to a server to obtain information about a certain state. Instead, a system subscribes to an event channel and is notified whenever new states occur. Any number of consumers can receive and react to the same events. When an event occurs, systems obtain this information and can react to it in near-real time. Systems that send events and others that receive the events don’t have dependencies on each other, except for the semantics of the message content.

**Platform Events and Salesforce Objects**

A platform event is a special kind of Salesforce entity, similar in many ways to a Salesforce object. An event message is an instance of a platform event, similar to how a record is an instance of a Salesforce object. Unlike with records, you can’t update or delete event messages or view them in the Salesforce user interface.

**Using Platform Events in Native and External Apps**

Platform events enable the flow of event messages within Salesforce and to or from external apps. Apps on the Salesforce platform use an Apex method to publish events and an Apex trigger or the empApi Lightning component to consume events. As an alternative to code, you can publish events with declarative tools, such as Process Builder and Flow Builder. External apps publish events using the sObject API and consume events using CometD. 

### Define and Publish Platform Events

Learning Objectives
After completing this unit, you’ll be able to:
- Define a platform event.
- Describe how platform event messages can be published.
- Use an Apex method to publish an event.
- Publish an event using clicks in a process or flow.
- Publish an event using REST API by inserting an sObject.

**Event Retention and ReplayId System Field**

Salesforce stores high-volume platform events for 72 hours in the event bus. Standard-volume events that were defined before Spring ’19 are stored for 24 hours in the event bus. 

You can retrieve stored events from the event bus using API CometD clients. You can retrieve all stored events, or you can specify the replay ID of an event as the baseline for the retrieved portion of events. With Apex triggers, you can resume a suspended trigger and pick up the earliest retained unprocessed events. 

Even though Salesforce retains event messages temporarily, you can’t query them through SOQL or SOSL. Similarly, you can’t use event messages in the user interface in reports, list views, and search. You can retrieve past events only when subscribing in CometD and using a ReplayId option. 

Each event message is assigned an opaque ID contained in the ReplayId field. The ReplayId field value, which is populated by the system when the event is delivered to subscribers, refers to the position of the event in the event stream. Replay ID values are not guaranteed to be contiguous for consecutive events. For example, the event following the event with ID 999 can have an ID of 1,025. A subscriber can store a replay ID value and use it on resubscription to retrieve events that are within the retention window. For example, a subscriber can retrieve missed events after a connection failure. Subscribers must not compute new replay IDs based on a stored replay ID to refer to other events in the stream.

**Publish After Commit Publish Behavior**

A platform event defined with the **Publish After Commit** behavior is published <u>only after a transaction commits successfully</u>. Define an event with this option if subscribers rely on data that the publishing transaction commits. For example, a process publishes an event message and creates a task record. A second process that is subscribed to the event is fired and expects to find the task record. Another reason for choosing this behavior is when you don't want the event message to be published if the transaction fails.

**Publish Immediately Publish Behavior**

A platform event defined with the **Publish Immediately** behavior is published when the publish call executes. Select this option if you want the event message to be published <u>regardless of whether the transaction succeeds</u>. Also choose this option if the publisher and subscribers are independent, and subscribers don't rely on data committed by the publisher. For example, the immediate publishing behavior is suitable for an event used for logging purposes. With this option, a subscriber might receive the event message before data is committed by the publisher transaction.

**Publish Event Messages Using Apex**

The following example creates one event of type `Cloud_News__e`, publishes it, and then checks whether the publishing was successful or encountered errors. The `EventBus.publish()` method returns a `Database.SaveResult` object, which contains the result of the publishing. If `isSuccess()` returns `true`, the publish request is queued in Salesforce and the event message is published asynchronously. If `isSuccess()` returns `false`, the event publish operation resulted in errors, which are returned in the `Database.Error` object. This method doesn’t throw an exception due to an unsuccessful publish operation.

```Java
// Create an instance of the event and store it in the newsEvent variable
Cloud_News__e newsEvent = new Cloud_News__e(
           Location__c='Mountain City',
           Urgent__c=true,
           News_Content__c='Lake Road is closed due to mudslides.');
// Call method to publish events
Database.SaveResult sr = EventBus.publish(newsEvent);
// Inspect publishing result
if (sr.isSuccess()) {
    System.debug('Successfully published event.');
} else {
    for(Database.Error err : sr.getErrors()) {
        System.debug('Error returned: ' +
                     err.getStatusCode() +
                     ' - ' +
                     err.getMessage());
    }
}
```

To publish more than one event in the same call, add your events to a list of events, and pass the list to the `EventBus.publish()` method. The output of this method is an array of `Database.SaveResult` objects: one for each published event. `EventBus.publish()` can publish some passed-in events, even when other events can’t be published due to errors.

```Java
// List to hold event objects to be published.
List<Cloud_News__e> newsEventList = new List<Cloud_News__e>();
// Create event objects.
Cloud_News__e newsEvent1 = new Cloud_News__e(
           Location__c='Mountain City',
           Urgent__c=true,
           News_Content__c='Lake Road is closed due to mudslides.');
Cloud_News__e newsEvent2 = new Cloud_News__e(
           Location__c='Mountain City',
           Urgent__c=false,
           News_Content__c='Small incident on Goat Lane causing traffic.');
// Add event objects to the list.
newsEventList.add(newsEvent1);
newsEventList.add(newsEvent2);
// Call method to publish events.
List<Database.SaveResult> results = EventBus.publish(newsEventList);
// Inspect publishing result for each event
for (Database.SaveResult sr : results) {
    if (sr.isSuccess()) {
        System.debug('Successfully published event.');
    } else {
        for(Database.Error err : sr.getErrors()) {
            System.debug('Error returned: ' +
                        err.getStatusCode() +
                        ' - ' +
                        err.getMessage());
        }
    }
}
```

**Publish Event Messages Using Clicks**

To publish event messages without code, use the record creation functionality in a process or flow.

**Publish Event Messages Using Salesforce APIs**

sObject REST endpoint:
```json
/services/data/v45.0/sobjects/Cloud_News__e/
```
Request body for a POST request:
```json
{
   "Location__c" : "Mountain City",
   "Urgent__c" : true,
   "News_Content__c" : "Lake Road is closed due to mudslides."
}
```

### Subscribe to Platform Events

Learning Objectives
After completing this unit, you’ll be able to:
- Describe how to subscribe to platform event messages.
- Subscribe to an event on the platform and in external apps.
- Test platform events in an Apex test method.
- Subscribe to platform events via CometD.

Simply write an after insert Apex trigger on the event object to subscribe to incoming events. Triggers provide an autosubscription mechanism in Apex. No need to explicitly create and listen to a channel. Triggers receive event notifications from various sources—whether they’re published through Apex or APIs.

Platform events support only after insert triggers. The after insert trigger event corresponds to the time after a platform event is published. After an event message is published, the after insert trigger is fired.

The following example shows a trigger for the Cloud News event. It iterates through each event and checks whether the news is urgent through the `Urgent__c` field. If the news is urgent, the trigger creates a case to dispatch a news reporter and adds the event location to the case subject.
```Java
// Trigger for listening to Cloud_News events.
trigger CloudNewsTrigger on Cloud_News__e (after insert) {
    // List to hold all cases to be created.
    List<Case> cases = new List<Case>();
    // Get queue Id for case owner
    Group queue = [SELECT Id FROM Group WHERE Name='Regional Dispatch' AND Type='Queue'];
    // Iterate through each notification.
    for (Cloud_News__e event : Trigger.New) {
        if (event.Urgent__c == true) {
            // Create Case to dispatch new team.
            Case cs = new Case();
            cs.Priority = 'High';
            cs.Subject = 'News team dispatch to ' +
                event.Location__c;
            cs.OwnerId = queue.Id;
            cases.add(cs);
        }
   }
    // Insert all cases corresponding to events received.
    insert cases;
}
```

**Set Up Debug Logging**

Unlike triggers on standard or custom objects, triggers on platform events don’t execute in the same Apex transaction as the one that published the event. The trigger runs in its own process under the Automated Process entity, which is a system user. As a result, debug logs corresponding to the trigger execution are created by the Automated Process entity and aren’t available in the Developer Console. To collect platform event trigger logs, add a trace flag entry for the Automated Process entity in Setup.

1. From Setup, enter Debug Logs in the Quick Find box, then click Debug Logs.
1. Click New.
1. For Traced Entity Type, select Automated Process.
1. Select the start date and expiration date for the logs you want to collect.
1. For Debug Level, enter * and click Search.
1. Select a predefined debug level, such as SFDC_DevConsole or click New to create your own debug level.
1. Click Save.

# User Interface(23%) 13-14
