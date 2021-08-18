title: Salesforce notes(2021/06/21 updated 1/26)
author: zansy
tags:
  - Salesforce
categories:
  - 唯有爱与工作不可辜负
date: 2021-06-21 09:55:00
---
Some simplefor salesforce learning. Mainly on salesforce trailhead.
<!--more-->
## Asynchronous Apex 
### Use Future Methods

#### Learning Objectives
After completing this unit, you’ll know:

- When to use future methods.
- The limitations of using future methods.
- How to use future methods for callouts.
- Future method best practices.

#### Future Apex
Future Apex is used to run processes in a separate thread, at a later time when system resources become available.

Future methods are typically used for:
- Callouts to external Web services. If you are making callouts from a trigger or after performing a DML operation, you must use a future or queueable method. A callout in a trigger would hold the database connection open for the lifetime of the callout and that is a "no-no" in a multitenant environment.
- Operations you want to run in their own thread, when time permits such as some sort of resource-intensive calculation or processing of records.
Isolating DML operations on different sObject types to prevent the mixed DML error. This is somewhat of an edge-case but you may occasionally run across this issue. 

#### Future Method Syntax
Future methods must be static methods, and can only return a void type. The specified parameters must be primitive data types, arrays of primitive data types, or collections of primitive data types. Notably, future methods can’t take standard or custom objects as arguments. A common pattern is to pass the method a List of record IDs that you want to process asynchronously.
```Java
global class SomeClass {
  @future
  public static void someFutureMethod(List<Id> recordIds) {
    List<Account> accounts = [Select Id, Name from Account Where Id IN :recordIds];
    // process account records to do awesome stuff
  }
}
```
It’s important to note that **future methods are not guaranteed to execute in the same order as they are called**. Again, future methods are not guaranteed to execute in the same order as they are called. When using future methods, it’s also possible that two future methods could run concurrently, which could result in record locking and a nasty runtime error if the two methods were updating the same record.

#### Sample Callout Code
To make a Web service callout to an external service or API, you create an Apex class with a future method that is marked with (callout=true). The class below has methods for making the callout both synchronously and asynchronously where callouts are not permitted. We insert a record into a custom log object to track the status of the callout simply because logging is always fun to do!
```Java
public class SMSUtils {
    // Call async from triggers, etc, where callouts are not permitted.
    @future(callout=true)
    public static void sendSMSAsync(String fromNbr, String toNbr, String m) {
        String results = sendSMS(fromNbr, toNbr, m);
        System.debug(results);
    }
    // Call from controllers, etc, for immediate processing
    public static String sendSMS(String fromNbr, String toNbr, String m) {
        // Calling 'send' will result in a callout
        String results = SmsMessage.send(fromNbr, toNbr, m);
        insert new SMS_Log__c(to__c=toNbr, from__c=fromNbr, msg__c=results);
        return results;
    }
}
```

#### Test Classes
Testing future methods is a little different than typical Apex testing. To test future methods, enclose your test code between the startTest and stopTest test methods. The system collects all asynchronous calls made after the startTest. When stopTest is executed, all these collected asynchronous processes are then run synchronously. You can then assert that the asynchronous call operated properly.

>Test code cannot actually send callouts to external systems, so you’ll have to ‘mock’ the callout for test coverage.

Here’s our mock callout class used for testing. The Apex testing framework utilizes this ‘mock’ response instead of making the actual callout to the REST API endpoint.
```Java
@isTest
global class SMSCalloutMock implements HttpCalloutMock {
    global HttpResponse respond(HttpRequest req) {
        // Create a fake response
        HttpResponse res = new HttpResponse();
        res.setHeader('Content-Type', 'application/json');
        res.setBody('{"status":"success"}');
        res.setStatusCode(200);
        return res;
    }
}
```
The test class contains a single test method, which tests both the asynchronous and synchronous methods as the former calls the latter.
```Java
@IsTest
private class Test_SMSUtils {
  @IsTest
  private static void testSendSms() {
    Test.setMock(HttpCalloutMock.class, new SMSCalloutMock());
    Test.startTest();
      SMSUtils.sendSMSAsync('111', '222', 'Greetings!');
    Test.stopTest();
    // runs callout and check results
    List<SMS_Log__c> logs = [select msg__c from SMS_Log__c];
    System.assertEquals(1, logs.size());
    System.assertEquals('success', logs[0].msg__c);
  }
}
```

#### Best Practices
Since every future method invocation adds one request to the asynchronous queue, avoid design patterns that add large numbers of future requests over a short period of time. If your design has the potential to add 2000 or more requests at a time, requests could get delayed due to flow control. Here are some best practices you want to keep in mind:
- Ensure that future methods execute as fast as possible.
- If using Web service callouts, try to bundle all callouts together from the same future method, rather than using a separate future method for each callout.
- Conduct thorough testing at scale. Test that a trigger enqueuing the @future calls is able to handle a trigger collection of 200 records. This helps determine if delays may occur given the design at current and future volumes.
- Consider using Batch Apex instead of future methods to process large number of records asynchronously. This is more efficient than creating a future request for each record.

#### Things to Remember
Future methods are a great tool, but with great power comes great responsibility. Here are some things to keep in mind when using them:
- Methods with the future annotation must be static methods, and can only return a void type.
- The specified parameters must be primitive data types, arrays of primitive data types, or collections of primitive data types; future methods can’t take objects as arguments.
- Future methods won’t necessarily execute in the same order they are called. In addition, it’s possible that two future methods could run concurrently, which could result in record locking if the two methods were updating the same record.
- Future methods can’t be used in Visualforce controllers in getMethodName(), setMethodName(), nor in the constructor.
- You can’t call a future method from a future method. Nor can you invoke a trigger that calls a future method while running a future method. See the link in the Resources for preventing recursive future method calls.
- The getContent() and getContentAsPDF() methods can’t be used in methods with the future annotation.
- You’re limited to 50 future calls per Apex invocation, and there’s an additional limit on the number of calls in a 24-hour period. For more information on limits, see the link below.

#### Challenge
>Create an Apex class that uses the @future annotation to update Account records.
Create an Apex class with a future method that accepts a List of Account IDs and updates a custom field on the Account object with the number of contacts associated to the Account. Write unit tests that achieve 100% code coverage for the class. Every hands-on challenge in this module asks you to create a test class.

Create a field on the Account object:
Label: Number Of Contacts
Name: Number_Of_Contacts
Type: Number
This field will hold the total number of Contacts for the Account

Create an Apex class:
Name: AccountProcessor
Method name: countContacts
The method must accept a List of Account IDs
The method must use the @future annotation
The method counts the number of Contact records associated to each Account ID passed to the method and updates the 'Number_Of_Contacts__c' field with this value

Create an Apex test class:
Name: AccountProcessorTest
The unit tests must cover all lines of code included in the AccountProcessor class, resulting in 100% code coverage.
Before verifying this challenge, run your test class at least once using the Developer Console Run All feature

```Java
public class AccountProcessor {
	@future
    public static void countContacts(List<Id> accountIds){
        List<Account> accounts = [Select Id, Name from Account Where Id IN : accountIds];
        List<Account> updatedAccounts = new List<Account>();
        for(Account account : accounts){
            account.Number_of_Contacts__c = [Select count() from Contact Where AccountId =: account.Id];
             System.debug('No Of Contacts = ' + account.Number_of_Contacts__c);
             updatedAccounts.add(account);
         }
         update updatedAccounts;
    }
}
```
```Java
@isTest
public class AccountProcessorTest {
    @isTest
    public static void testNoOfContacts(){
        Account a = new Account();
        a.Name = 'Test Account';
        insert a;

        Contact c = new Contact();
        c.FirstName = 'Bob';
        c.LastName =  'Willie';
        c.AccountId = a.Id;
        
        Contact c2 = new Contact();
        c2.FirstName = 'Tom';
        c2.LastName = 'Cruise';
        c2.AccountId = a.Id;
        
        List<Id> acctIds = new List<Id>();
        acctIds.add(a.Id);
        
        Test.startTest();
        AccountProcessor.countContacts(acctIds);
        Test.stopTest();
    }
}
```

### Use Batch Apex

#### Learning Objectives
After completing this unit, you’ll know:
- Where to use Batch Apex.
- The higher Apex limits when using batch.
- Batch Apex syntax.
- Batch Apex best practices.

#### Batch Apex
Batch Apex is used to run large jobs (think thousands or millions of records!) that would exceed normal processing limits. Using Batch Apex, you can process records asynchronously in batches (hence the name, “Batch Apex”) to stay within platform limits. If you have a lot of records to process, for example, data cleansing or archiving, Batch Apex is probably your best solution.

#### Batch Apex Syntax
To write a Batch Apex class, your class must implement the Database.Batchable interface and include the following three methods:

**start**
Used to collect the records or objects to be passed to the interface method execute for processing. This method is called once at the beginning of a Batch Apex job and returns either a Database.QueryLocator object or an Iterable that contains the records or objects passed to the job.

Most of the time a QueryLocator does the trick with a simple SOQL query to generate the scope of objects in the batch job. But if you need to do something crazy like loop through the results of an API call or pre-process records before being passed to the execute method, you might want to check out the Custom Iterators link in the Resources section.

With the QueryLocator object, the governor limit for the total number of records retrieved by SOQL queries is bypassed and you can query up to 50 million records. However, with an Iterable, the governor limit for the total number of records retrieved by SOQL queries is still enforced.

**execute**
Performs the actual processing for each chunk or “batch” of data passed to the method. The default batch size is 200 records. Batches of records are not guaranteed to execute in the order they are received from the start method.

This method takes the following:
- A reference to the Database.BatchableContext object.
- A list of sObjects, such as List<sObject>, or a list of parameterized types. If you are using a Database.QueryLocator, use the returned list.
**finish**
Used to execute post-processing operations (for example, sending an email) and is called once after all batches are processed.

```Java
public class MyBatchClass implements Database.Batchable<sObject> {
    public (Database.QueryLocator | Iterable<sObject>) start(Database.BatchableContext bc) {
        // collect the batches of records or objects to be passed to execute
    }
    public void execute(Database.BatchableContext bc, List<P> records){
        // process each batch of records
    }
    public void finish(Database.BatchableContext bc){
        // execute any post-processing operations
    }
}
```

#### Invoking a Batch Class
To invoke a batch class, simply instantiate it and then call Database.executeBatch with the instance:
```Java
MyBatchClass myBatchObject = new MyBatchClass();
Id batchId = Database.executeBatch(myBatchObject);
```
You can also optionally pass a second scope parameter to specify the number of records that should be passed into the execute method for each batch. Pro tip: you might want to limit this batch size if you are running into governor limits.
```Java
Id batchId = Database.executeBatch(myBatchObject, 100);
```
Each batch Apex invocation creates an AsyncApexJob record so that you can track the job’s progress. You can view the progress via SOQL or manage your job in the Apex Job Queue. We’ll talk about the Job Queue shortly.
```Java
AsyncApexJob job = [SELECT Id, Status, JobItemsProcessed, TotalJobItems, NumberOfErrors FROM AsyncApexJob WHERE ID = :batchId ];
```

#### Using State in Batch Apex
Batch Apex is typically stateless. Each execution of a batch Apex job is considered a discrete transaction. For example, a batch Apex job that contains 1,000 records and uses the default batch size is considered five transactions of 200 records each.

If you specify Database.Stateful in the class definition, you can maintain state across all transactions. When using Database.Stateful, only instance member variables retain their values between transactions. Maintaining state is useful for counting or summarizing records as they’re processed. In our next example, we’ll be updating contact records in our batch job and want to keep track of the total records affected so we can include it in the notification email.

#### Sample Batch Apex Code
Let’s say you have a business requirement that states that all contacts for companies in the USA must have their parent company’s billing address as their mailing address. Unfortunately, users are entering new contacts without the correct addresses! Will users never learn?! Write a Batch Apex class that ensures that this requirement is enforced.

The following sample class finds all account records that are passed in by the start() method using a QueryLocator and updates the associated contacts with their account’s mailing address. Finally, it sends off an email with the results of the bulk job and, since we are using Database.Stateful to track state, the number of records updated.
```Java
public class UpdateContactAddresses implements
    Database.Batchable<sObject>, Database.Stateful {
    // instance member to retain state across transactions
    public Integer recordsProcessed = 0;
    public Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator(
            'SELECT ID, BillingStreet, BillingCity, BillingState, ' +
            'BillingPostalCode, (SELECT ID, MailingStreet, MailingCity, ' +
            'MailingState, MailingPostalCode FROM Contacts) FROM Account ' +
            'Where BillingCountry = \'USA\''
        );
    }
    public void execute(Database.BatchableContext bc, List<Account> scope){
        // process each batch of records
        List<Contact> contacts = new List<Contact>();
        for (Account account : scope) {
            for (Contact contact : account.contacts) {
                contact.MailingStreet = account.BillingStreet;
                contact.MailingCity = account.BillingCity;
                contact.MailingState = account.BillingState;
                contact.MailingPostalCode = account.BillingPostalCode;
                // add contact to list to be updated
                contacts.add(contact);
                // increment the instance member counter
                recordsProcessed = recordsProcessed + 1;
            }
        }
        update contacts;
    }
    public void finish(Database.BatchableContext bc){
        System.debug(recordsProcessed + ' records processed. Shazam!');
        AsyncApexJob job = [SELECT Id, Status, NumberOfErrors,
            JobItemsProcessed,
            TotalJobItems, CreatedBy.Email
            FROM AsyncApexJob
            WHERE Id = :bc.getJobId()];
        // call some utility to send email
        EmailUtils.sendMessage(job, recordsProcessed);
    }
}
```
The code should be fairly straightforward but can be a little abstract in reality. Here’s what’s going on in more detail:
- The start method provides the collection of all records that the execute method will process in individual batches. It returns the list of records to be processed by calling Database.getQueryLocator with a SOQL query. In this case we are simply querying for all Account records with a Billing Country of ‘USA’.
- Each batch of 200 records is passed in the second parameter of the execute method. The execute method sets each contact’s mailing address to the accounts’ billing address and increments recordsProcessed to track the number of records processed.
- When the job is complete, the finish method performs a query on the AsyncApexJob object (a table that lists information about batch jobs) to get the status of the job, the submitter’s email address, and some other information. It then sends a notification email to the job submitter that includes the job info and number of contacts updated.

#### Testing Batch Apex
```Java
@isTest
private class UpdateContactAddressesTest {
    @testSetup
    static void setup() {
        List<Account> accounts = new List<Account>();
        List<Contact> contacts = new List<Contact>();
        // insert 10 accounts
        for (Integer i=0;i<10;i++) {
            accounts.add(new Account(name='Account '+i,
                billingcity='New York', billingcountry='USA'));
        }
        insert accounts;
        // find the account just inserted. add contact for each
        for (Account account : [select id from account]) {
            contacts.add(new Contact(firstname='first',
                lastname='last', accountId=account.id));
        }
        insert contacts;
    }
    @isTest static void test() {
        Test.startTest();
        UpdateContactAddresses uca = new UpdateContactAddresses();
        Id batchId = Database.executeBatch(uca);
        Test.stopTest();
        // after the testing stops, assert records were updated properly
        System.assertEquals(10, [select count() from contact where MailingCity = 'New York']);
    }
}
```
>Make sure that the number of records inserted is less than or equal to the batch size of 200 because test methods can execute only one batch. You must also ensure that the Iterable returned by the start method matches the batch size.

#### Challenge
>Create an Apex class that uses Batch Apex to update Lead records.
Create an Apex class that implements the Database.Batchable interface to update all Lead records in the org with a specific LeadSource.

Create an Apex class:
Name: LeadProcessor
Interface: Database.Batchable
Use a QueryLocator in the start method to collect all Lead records in the org
The execute method must update all Lead records in the org with the LeadSource value of Dreamforce

Create an Apex test class:
Name: LeadProcessorTest
In the test class, insert 200 Lead records, execute the LeadProcessor Batch class and test that all Lead records were updated correctly

The unit tests must cover all lines of code included in the LeadProcessor class, resulting in 100% code coverage
Before verifying this challenge, run your test class at least once using the Developer Console Run All feature
```Java
public class LeadProcessor implements Database.Batchable<SObject>, Database.Stateful{
    public Integer recordProcessed = 0;
    public Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator([Select LeadSource From Lead ]);
    }
    public void execute(Database.BatchableContext bc, List<Lead> scope) {
        for (Lead lead : scope) {
            lead.LeadSource = 'Dreamforce';
        }
        update scope;
    }
    public void finish(Database.BatchableContext bc){

    }
}
```
```Java
@isTest
public class LeadProcessorTest {
    @isTest
    static void test(){
        List<Lead> leads = new List<Lead>();
        for (Integer i = 0; i < 200; i++) {
            Lead lead = new Lead();
            lead.FirstName = 'testF';
            lead.LastName = 'testL' + i;
            lead.Company = 'testCompany' + i;
            leads.add(lead);
        }
        insert leads;
        Test.startTest();
        LeadProcessor leadProcessor = new LeadProcessor();
        Database.executeBatch(leadProcessor);
        Test.stopTest();
        System.assertEquals(200, [select count() from Lead where LeadSource = 'Dreamforce' ]);
    }
}
```

### Control Processes with Queueable Apex
#### Learning Objectives
After completing this unit, you’ll know:
- When to use the Queueable interface.
- The differences between queueable and future methods.
- Queueable Apex syntax.
- Queueable method best practices.

#### Queueable Apex
We took the simplicity of future methods and the power of Batch Apex and mixed them together to form Queueable Apex! It gives you a class structure that the platform serializes for you, a simplified interface without start and finish methods and even allows you to utilize more than just primitive arguments! It is called by a simple System.enqueueJob() method, which returns a job ID that you can monitor. It beats sliced bread hands down!

Queueable Apex allows you to submit jobs for asynchronous processing similar to future methods with the following additional benefits:
- Non-primitive types: Your Queueable class can contain member variables of non-primitive data types, such as sObjects or custom Apex types. Those objects can be accessed when the job executes.
- Monitoring: When you submit your job by invoking the System.enqueueJob method, the method returns the ID of the AsyncApexJob record. You can use this ID to identify your job and monitor its progress, either through the Salesforce user interface in the Apex Jobs page, or programmatically by querying your record from AsyncApexJob.
- Chaining jobs: You can chain one job to another job by starting a second job from a running job. Chaining jobs is useful if you need to do some sequential processing.

#### Queueable Versus Future
use future methods instead of queueable is when your functionality is sometimes executed synchronously, and sometimes asynchronously. It’s much easier to refactor a method in this manner than converting to a queueable class. This is handy when you discover that part of your existing code needs to be moved to async execution. You can simply create a similar future method that wraps your synchronous method like so:
```Java
@future
static void myFutureMethod(List<String> params) {
    // call synchronous method
    mySyncMethod(params);
}
```

#### Sample Code
The following code takes a collection of Account records, sets the parentId for each record, and then updates the records in the database.
```Java
public class UpdateParentAccount implements Queueable {
    private List<Account> accounts;
    private ID parent;
    public UpdateParentAccount(List<Account> records, ID id) {
        this.accounts = records;
        this.parent = id;
    }
    public void execute(QueueableContext context) {
        for (Account account : accounts) {
          account.parentId = parent;
          // perform other processing or callout
        }
        update accounts;
    }
}
```
To add this class as a job on the queue, execute the following code:
```Java
// find all accounts in ‘NY’
List<Account> accounts = [select id from account where billingstate = ‘NY’];
// find a specific parent account for all records
Id parentId = [select id from account where name = 'ACME Corp'][0].Id;
// instantiate a new instance of the Queueable class
UpdateParentAccount updateJob = new UpdateParentAccount(accounts, parentId);
// enqueue the job for processing
ID jobID = System.enqueueJob(updateJob);
```
You can use the new job ID to monitor progress, either through the Apex Jobs page or programmatically by querying AsyncApexJob:
```Java
SELECT Id, Status, NumberOfErrors FROM AsyncApexJob WHERE Id = :jobID
```
#### Testing Queueable Apex
```Java
@isTest
public class UpdateParentAccountTest {
    @testSetup
    static void setup() {
        List<Account> accounts = new List<Account>();
        // add a parent account
        accounts.add(new Account(name='Parent'));
        // add 100 child accounts
        for (Integer i = 0; i < 100; i++) {
            accounts.add(new Account(
                name='Test Account'+i
            ));
        }
        insert accounts;
    }
    static testmethod void testQueueable() {
        // query for test data to pass to queueable class
        Id parentId = [select id from account where name = 'Parent'][0].Id;
        List<Account> accounts = [select id, name from account where name like 'Test Account%'];
        // Create our Queueable instance
        UpdateParentAccount updater = new UpdateParentAccount(accounts, parentId);
        // startTest/stopTest block to force async processes to run
        Test.startTest();
        System.enqueueJob(updater);
        Test.stopTest();
        // Validate the job ran. Check if record have correct parentId now
        System.assertEquals(100, [select count() from account where parentId = :parentId]);
    }
}
```

#### Chaining Jobs
One of the best features of Queueable Apex is job chaining. If you ever need to run jobs sequentially, Queueable Apex could make your life much easier. To chain a job to another job, submit the second job from the execute() method of your queueable class. You can add only one job from an executing job, which means that only one child job can exist for each parent job. For example, if you have a second class called SecondJob that implements the Queueable interface, you can add this class to the queue in the execute() method as follows:
```Java
public class FirstJob implements Queueable {
    public void execute(QueueableContext context) {
        // Awesome processing logic here
        // Chain this job to next job by submitting the next job
        System.enqueueJob(new SecondJob());
    }
}
```
Once again, testing has a slightly different pattern. You can’t chain queueable jobs in an Apex test, doing so results in an error. To avoid nasty errors, you can check if Apex is running in test context by calling Test.isRunningTest() before chaining jobs.

#### Things to Remember
Queueable Apex is a great new tool but there are a few things to watch out for:
- The execution of a queued job counts once against the shared limit for asynchronous Apex method executions.
- You can add up to 50 jobs to the queue with System.enqueueJob in a single transaction.
- When chaining jobs, you can add only one job from an executing job with System.enqueueJob, which means that only one child job can exist for each parent queueable job. Starting multiple child jobs from the same queueable job is a no-no.
- No limit is enforced on the depth of chained jobs, which means that you can chain one job to another job and repeat this process with each new child job to link it to a new child job. However, for Developer Edition and Trial orgs, the maximum stack depth for chained jobs is 5, which means that you can chain jobs four times and the maximum number of jobs in the chain is 5, including the initial parent queueable job.

#### Challenge
>Create an Queueable Apex class that inserts Contacts for Accounts.Create a Queueable Apex class that inserts the same Contact for each Account for a specific state.

Create an Apex class:
Name: AddPrimaryContact
Interface: Queueable
Create a constructor for the class that accepts as its first argument a Contact sObject and a second argument as a string for the State abbreviation
The execute method must query for a maximum of 200 Accounts with the BillingState specified by the State abbreviation passed into the constructor and insert the Contact sObject record associated to each Account. Look at the sObject clone() method.

Create an Apex test class:
Name: AddPrimaryContactTest
In the test class, insert 50 Account records for BillingState NY and 50 Account records for BillingState CA
Create an instance of the AddPrimaryContact class, enqueue the job, and assert that a Contact record was inserted for each of the 50 Accounts with the BillingState of CA

The unit tests must cover all lines of code included in the AddPrimaryContact class, resulting in 100% code coverage
Before verifying this challenge, run your test class at least once using the Developer Console Run All feature
```Java
public class AddPrimaryContact implements Queueable {
    private Contact contact;
    private String state;
    public AddPrimaryContact(Contact contact, String state){
        this.contact = contact;
        this.state = state;
    }
    public void execute(QueueableContext context){
        List<Account> accounts  = [select ID, Name, (select id, FirstName, LastName from contacts) from Account where BillingState = :state limit 200 ];
        List<Contact> contacts = new List<Contact>();
        for (Account account : accounts) {
            Contact temp = contact.clone(false, false, false, false);
            temp.AccountId = account.Id;
            contacts.add(temp);
        }

        if (contacts.size() > 0) {
            insert contacts;
        }
    }

}
```
```Java
@isTest
public class AddPrimaryContactTest {
    @testSetup
    static void setup(){
        List<Account> accounts = new List<Account>();
        for (Integer i = 0; i < 50; i++) {
            accounts.add(new Account(Name='NY'+i, billingstate='NY'));
        }
        for (Integer i = 0; i < 50; i++) {
            accounts.add(new Account(Name='CA'+i, billingstate='CA'));
        }
        insert accounts;
    }
    static testmethod void testQueueable(){
        Contact contact = new Contact(FirstName = 'AddPrimaryContactTest', LastName = 'Queueable');
        Test.startTest();
        AddPrimaryContact addPrimaryContact = new AddPrimaryContact(contact, 'CA');
        System.enqueueJob(addPrimaryContact);
        Test.stopTest();

        System.assertEquals(50, [select count() from Contact where AccountId in (select Id from Account where BillingState = 'CA')]);
    }
}
```

### Schedule Jobs Using the Apex Scheduler

#### Learning Objectives
After completing this unit, you’ll know:
- When to use scheduled Apex.
- How to monitor scheduled jobs.
- Scheduled Apex syntax.
- Scheduled method best practices.

#### Scheduled Apex
The Apex Scheduler lets you delay execution so that you can run Apex classes at a specified time. This is ideal for daily or weekly maintenance tasks using Batch Apex.

#### Sample Code
This class queries for open opportunities that should have closed by the current date, and creates a task on each one to remind the owner to update the opportunity.
```Java
global class RemindOpptyOwners implements Schedulable {
    global void execute(SchedulableContext ctx) {
        List<Opportunity> opptys = [SELECT Id, Name, OwnerId, CloseDate
            FROM Opportunity
            WHERE IsClosed = False AND
            CloseDate < TODAY];
        // Create a task for each opportunity in the list
        TaskUtils.remindOwners(opptys);
    }
}
```
#### Using the System.Schedule Method
After you implement a class with the Schedulable interface, use the System.Schedule method to execute it. The System.Schedule method uses the user's timezone for the basis of all schedules, but runs in system mode—all classes are executed, whether or not the user has permission to execute the class.

The System.Schedule method takes three arguments: a name for the job, a CRON expression used to represent the time and date the job is scheduled to run, and the name of the class.
```Java
RemindOpptyOwners reminder = new RemindOpptyOwners();
// Seconds Minutes Hours Day_of_month Month Day_of_week optional_year
String sch = '20 30 8 10 2 ?';
String jobID = System.schedule('Remind Opp Owners', sch, reminder);
```
#### Scheduling a Job from the UI
You can also schedule a class using the user interface.
- From Setup, enter Apex in the Quick Find box, then select Apex Classes.
- Click Schedule Apex.
- For the job name, enter something like Daily Oppty Reminder.
- Click the lookup button next to Apex class and enter * for the search term to get a list of all classes that can be scheduled. In the search results, click the name of your scheduled class.
- Select Weekly or Monthly for the frequency and set the frequency desired.
- Select the start and end dates, and a preferred start time.
- Click Save.

#### Testing Scheduled Apex
```Java
@isTest
private class RemindOppyOwnersTest {
    // Dummy CRON expression: midnight on March 15.
    // Because this is a test, job executes
    // immediately after Test.stopTest().
    public static String CRON_EXP = '0 0 0 15 3 ? 2022';
    static testmethod void testScheduledJob() {
        // Create some out of date Opportunity records
        List<Opportunity> opptys = new List<Opportunity>();
        Date closeDate = Date.today().addDays(-7);
        for (Integer i=0; i<10; i++) {
            Opportunity o = new Opportunity(
                Name = 'Opportunity ' + i,
                CloseDate = closeDate,
                StageName = 'Prospecting'
            );
            opptys.add(o);
        }
        insert opptys;
        // Get the IDs of the opportunities we just inserted
        Map<Id, Opportunity> opptyMap = new Map<Id, Opportunity>(opptys);
        List<Id> opptyIds = new List<Id>(opptyMap.keySet());
        Test.startTest();
        // Schedule the test job
        String jobId = System.schedule('ScheduledApexTest',
            CRON_EXP,
            new RemindOpptyOwners());
        // Verify the scheduled job has not run yet.
        List<Task> lt = [SELECT Id
            FROM Task
            WHERE WhatId IN :opptyIds];
        System.assertEquals(0, lt.size(), 'Tasks exist before job has run');
        // Stopping the test will run the job synchronously
        Test.stopTest();
        // Now that the scheduled job has executed,
        // check that our tasks were created
        lt = [SELECT Id
            FROM Task
            WHERE WhatId IN :opptyIds];
        System.assertEquals(opptyIds.size(),
            lt.size(),
            'Tasks were not created');
    }
}
```
#### Things to Remember
Scheduled Apex has a number of items you need to be aware of (see Apex Scheduler in the Resources section for a complete list when you have time), but in general:
- You can only have 100 scheduled Apex jobs at one time and there are maximum number of scheduled Apex executions per a 24-hour period. See Execution Governors and Limits in the Resources section for details.
- Use extreme care if you’re planning to schedule a class from a trigger. You must be able to guarantee that the trigger won’t add more scheduled jobs than the limit.
- Synchronous Web service callouts are not supported from scheduled Apex. To be able to make callouts, make an asynchronous callout by placing the callout in a method annotated with @future(callout=true) and call this method from scheduled Apex. However, if your scheduled Apex executes a batch job, callouts are supported from the batch class.

#### Challenge
>Create an Apex class that uses Scheduled Apex to update Lead records.Create an Apex class that implements the Schedulable interface to update Lead records with a specific LeadSource. (This is very similar to what you did for Batch Apex.)

Create an Apex class:
Name: DailyLeadProcessor
Interface: Schedulable
The execute method must find the first 200 Leads with a blank LeadSource field and update them with the LeadSource value of Dreamforce

Create an Apex test class:
Name: DailyLeadProcessorTest
In the test class, insert 200 Lead records, schedule the DailyLeadProcessor class to run and test that all Lead records were updated correctly

The unit tests must cover all lines of code included in the DailyLeadProcessor class, resulting in 100% code coverage.
Before verifying this challenge, run your test class at least once using the Developer Console Run All feature

```Java
public class DailyLeadProcessor implements Schedulable {
    public void execute(SchedulableContext context){
        List<Lead> leads = [select Id, LeadSource from Lead where LeadSource = null];

        for (Lead lead : leads) {
            lead.LeadSource = 'Dreamforce';
        }
        update leads;
    }
}
```
```Java
@isTest
public class DailyLeadProcessorTest {
    static @IsTest
    void testScheduledJob(){
        List<Lead> leads = new List<Lead>();
        for (Integer i = 0; i < 200; i++) {
            leads.add(new Lead(LastName = 'Dreamforce'+i, Company = 'test'+i));
        }
        insert leads;

        Map<Id,Lead> leadMap = new Map<Id,Lead>(leads);
        List<Id> leadsId = new List<Id>(leadMap.keySet());
        Test.startTest();
        System.schedule('DailyLeadProcessor', '20 30 8 10 2 ?', new DailyLeadProcessor());
        Test.stopTest();
        System.assertEquals(200, [select count() from Lead where LeadSource = 'Dreamforce' and Id in :leadsId]);
    }
}
```

### Monitor Asynchronous Apex

#### Learning Objectives
- After completing this unit, you’ll know:
- How to monitor the different types of jobs.
- How to use the flex queue.

#### Monitoring Asynchronous Jobs
You can monitor the status of all jobs in the Salesforce user interface. From Setup, enter Jobs in the Quick Find box, then select **Apex Jobs**.

The Apex Jobs page shows all asynchronous Apex jobs with information about each job’s execution. The following screenshot shows one future method job and two completed batch jobs for the same Batch Apex class.

#### Monitoring Future Jobs
Future jobs show up on the Apex Jobs page like any other jobs. However, future jobs are not part of the flex queue currently.

#### Monitoring Queued Jobs with SOQL
To query information about your submitted job, perform a SOQL query on AsyncApexJob by filtering on the job ID that the System.enqueueJob method returns.
```Java
AsyncApexJob jobInfo = [SELECT Status, NumberOfErrors
    FROM AsyncApexJob WHERE Id = :jobID];
```

#### Monitoring Queue Jobs with the Flex Queue
The Apex Flex queue enables you to submit up to 100 batch jobs for execution. Any jobs that are submitted for execution are in holding status and are placed in the Apex Flex queue. Up to 100 batch jobs can be in the holding status.

Jobs are processed first-in first-out—in the order in which they’re submitted. You can look at the current queue order and shuffle the queue, so that you could move an important job to the front, or less important ones to the back.

When system resources become available, the system picks up the next job from the top of the Apex Flex queue and moves it to the batch job queue. The system can process up to five queued or active jobs simultaneously for each organization. The status of these moved jobs changes from Holding to Queued. Queued jobs get executed when the system is ready to process new jobs. Like other jobs, you can monitor queued jobs in the Apex Jobs page.

#### Monitoring Scheduled Jobs
After an Apex job has been scheduled, you can obtain more information about it by running a SOQL query on CronTrigger. The following sample queries the number of times the job has run, and the date and time when the job is scheduled to run again. It uses a jobID variable which is returned from the System.schedule method.
```Java
CronTrigger ct = [SELECT TimesTriggered, NextFireTime FROM CronTrigger WHERE Id = :jobID];
```
If you’re performing this query inside the execute method of your schedulable class, you can obtain the ID of the current job by calling getTriggerId on the SchedulableContext argument variable.
```Java
global class DoAwesomeStuff implements Schedulable {
    global void execute(SchedulableContext sc) {
        // some awesome code
        CronTrigger ct = [SELECT TimesTriggered, NextFireTime FROM CronTrigger WHERE Id = :sc.getTriggerId()];
    }
}
```
You can also get the job’s name and the job’s type from the CronJobDetail record associated with the CronTrigger record. To do so, use the CronJobDetail relationship when performing a query on CronTrigger. This example retrieves the most recent CronTrigger record with the job name and type from CronJobDetail.
```Java
CronTrigger job = [SELECT Id, CronJobDetail.Id, CronJobDetail.Name, CronJobDetail.JobType FROM CronTrigger ORDER BY CreatedDate DESC LIMIT 1];
```
Alternatively, you can query CronJobDetail directly to get the job’s name and type. The following example gets the job’s name and type for the CronTrigger record queried in the previous example. The corresponding CronJobDetail record ID is obtained by the CronJobDetail.Id expression on the CronTrigger record.
```Java
CronJobDetail ctd = [SELECT Id, Name, JobType FROM CronJobDetail WHERE Id = :job.CronJobDetail.Id];
```
And lastly, to obtain the total count of all Apex scheduled jobs, excluding all other scheduled job types, perform the following query. Note the value '7' is specified for the job type, which corresponds to the scheduled Apex job type. See CronJobDetail in the Resources section for a list of all types.
```Java
SELECT COUNT() FROM CronTrigger WHERE CronJobDetail.JobType = '7’
```

#### Quiz
> What type of jobs do not show up in the Apex Flex Queue.

Future Method Jobs
> Which statement is true regarding the Flex Queue.

Jobs are processed first-in first-out.