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