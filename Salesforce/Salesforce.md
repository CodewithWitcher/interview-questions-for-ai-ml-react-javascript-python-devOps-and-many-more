This comprehensive guide provides an in-depth overview of fundamental Salesforce concepts, ranging from the basic architecture to advanced development and data management practices.

### 1. Basic Salesforce Concepts

#### What is Salesforce and why is it used?

Salesforce is a cloud-based customer relationship management (CRM) platform that provides a suite of applications for businesses to manage their sales, service, and marketing efforts. It is used to streamline business processes, gain customer insights, and build stronger customer relationships.

#### What are the different types of Salesforce clouds?

Salesforce offers a variety of clouds tailored to specific business functions. Some of the primary clouds include:

* **Sales Cloud:**

  Manages the entire sales process, from lead generation to closing deals.

* **Service Cloud:**

  Enables businesses to provide personalized customer service and support.

* **Marketing Cloud:**

  Automates and manages marketing campaigns and customer journeys.

* **Commerce Cloud:**

  Provides a platform for e-commerce and unified retail experiences.

* **Community Cloud (now Experience Cloud):**

  Creates branded online spaces for customers, partners, and employees to connect.

* **Analytics Cloud (Tableau):**

  Offers data visualization and business intelligence capabilities.

* **App Cloud:**

  Allows for the development and deployment of custom applications on the Salesforce platform.

#### What is an object in Salesforce?

In Salesforce, an object is a database table that stores data specific to an organization. Objects provide the structure for storing records and are analogous to tables in a traditional database.

#### What is the difference between Standard Objects and Custom Objects?

* **Standard Objects:**

  These are the default objects that come with a Salesforce organization, such as Account, Contact, Lead, and Opportunity. They are predefined by Salesforce to cover a wide range of common business needs. Standard objects cannot be deleted.

* **Custom Objects:**

  These are user-defined objects created to store information that is unique to a specific company or industry. They allow organizations to extend the standard functionality to meet their specific business requirements.

#### What are fields in Salesforce? Give examples of standard and custom fields.

Fields are the individual data points within an object, similar to columns in a database table. Each field has a specific data type.

* **Standard Fields:**

  These are pre-built fields on standard objects. Examples include the "Name" field on the Account object or the "Email" field on the Contact object.

* **Custom Fields:**

  These are fields created by users on either standard or custom objects to capture specific information. An example would be a "Customer ID" field on the Account object or a "Property Type" custom field on a custom "Property" object.

#### What are records in Salesforce?

Records are the individual instances of an object, representing a single entry of data. For example, a specific company like "Acme Corporation" would be a record in the "Account" object.

#### What is the AppExchange in Salesforce?

The AppExchange is Salesforce's online marketplace for applications, components, and consulting services built on the Salesforce platform. It allows businesses to extend the functionality of their Salesforce org by installing pre-built solutions.

#### What is a Salesforce Edition (Enterprise, Professional, etc.)?

Salesforce Editions are bundles of features and services that cater to different business needs and budgets. Common editions include:

* **Essentials:**

  Designed for small businesses.

* **Professional:**

  A complete CRM for teams of any size.

* **Enterprise:**

  A deeply customizable sales CRM for your business.

* **Unlimited:**

  The ultimate platform for limitless growth.

Each edition offers varying levels of functionality, customization, and support.

### 2. Data Model & Relationships

#### What are the different types of relationships in Salesforce?

Salesforce supports several types of relationships to connect objects, including:

* **Master-Detail Relationship:**

  A tightly coupled parent-child relationship where the master record controls the behavior of the detail record.

* **Lookup Relationship:**

  A loosely coupled relationship that links two objects together.

* **Many-to-Many Relationship:**

  Achieved through a junction object, allowing multiple records of one object to be related to multiple records of another.

* **Self-Relationship:**

  A relationship where an object has a lookup to itself, often used for hierarchical structures.

* **Hierarchical Relationship:**

  A special type of lookup relationship available only on the User object to create a user hierarchy.

* **External Lookup Relationship:**

  Connects a Salesforce object to an external data source.

* **Indirect Lookup Relationship:**

  Links a child external object to a parent standard or custom object.

#### Difference between Master-Detail and Lookup relationship?

| Feature | Master-Detail Relationship | Lookup Relationship |
| :--- | :--- | :--- |
| **Coupling** | Tightly coupled. | Loosely coupled. |
| **Dependency** | The detail (child) record cannot exist without the master (parent) record. Deleting the master also deletes the child (cascade delete). | Child records can exist independently of the parent. |
| **Ownership & Sharing** | The detail record inherits the sharing and security settings of the master record. | Child records have their own sharing settings. |
| **Roll-Up Summary Fields** | Supported on the master object to aggregate data from detail records. | Not supported by default. |
| **Mandatory Parent** | The parent record is always required on the child. | The parent record can be optional. |
| **Object Types** | Standard objects cannot be on the detail side of a relationship with a custom object. | Allows for more flexibility in relating standard and custom objects. |
| **Number of Relationships** | An object can have a maximum of two master-detail relationships. | An object can have up to 40 lookup relationships. |

#### What is a Junction Object and why is it used?

A junction object is a custom object with two master-detail relationships that is used to create a many-to-many relationship between two other objects. It sits between the two objects and allows a single record of one object to be linked to many records of the other, and vice versa.

#### Can we create a Master-Detail relationship on an existing record?

No, you cannot directly create a master-detail relationship if the lookup field on the child object already contains values. You must first populate the lookup field with the appropriate parent record IDs for all existing child records.

#### What is a Roll-up Summary field and when can it be used?

A Roll-up Summary field calculates values from related records, such as the count of detail records or the sum of a field on the detail records. It can only be created on the master object in a master-detail relationship.

#### What are formula fields? Can you reference other objects in a formula?

Formula fields are read-only fields that derive their value from a formula expression. They can reference fields on the same object or on a related parent object through a lookup or master-detail relationship.

### 3. Security & Access

#### What is a Profile in Salesforce?

A profile in Salesforce is a collection of settings and permissions that define what a user can do within the platform. It controls access to objects, fields, and functionalities, such as create, read, edit, and delete (CRED) permissions. Every user must be assigned a profile.

#### What is a Role in Salesforce?

A role in Salesforce defines a user's level of data visibility at the record level. Roles are typically organized in a hierarchy that mirrors an organization's structure, allowing users in higher roles to see records owned by users in roles below them.

#### Difference between Profiles and Permission Sets?

* **Profiles:**

  Define the baseline level of access for a user. A user can only have one profile.

* **Permission Sets:**

  Grant additional permissions and access to users on top of their assigned profile. A user can have multiple permission sets. This allows for more granular control over user access without creating numerous profiles.

#### What is the difference between OWD (Organization-Wide Defaults) and Sharing Rules?

* **OWD:**

  Sets the default level of access for all records of an object for all users in the organization. It is the most restrictive level of access.

* **Sharing Rules:**

  Are used to grant broader access to records than what is defined by the OWD. They allow you to share records based on record ownership or criteria with public groups, roles, or roles and subordinates.

#### What is the difference between Role Hierarchy and Sharing Rules?

* **Role Hierarchy:**

  Provides vertical access to records. Users in higher roles automatically get access to the records of users in roles below them.

* **Sharing Rules:**

  Provide lateral access to records. They allow you to share records with users who are not in the direct line of the role hierarchy.

#### How do you restrict or open access to records for users?

Access to records can be controlled through a combination of:

* **Organization-Wide Defaults (OWD):**

  To set the baseline access.

* **Role Hierarchy:**

  To grant access to managers.

* **Sharing Rules:**

  To extend access to other groups of users.

* **Manual Sharing:**

  To grant access to a single record to a specific user or group.

* **Profiles and Permission Sets:**

  To control object-level and field-level access.

#### What is Field-Level Security?

Field-Level Security controls which fields a user can see and edit on a record. It is configured in profiles and permission sets.

### 4. Automation Tools

#### What is a Workflow in Salesforce?

A Workflow in Salesforce is an automation tool that can trigger actions when a record is created or updated. These actions include sending an email, creating a task, updating a field, or sending an outbound message. *Note: Salesforce is retiring Workflow Rules and Process Builder, and it is recommended to use Flow for new automation.*

#### Difference between Workflow Rules and Process Builder?

* **Workflow Rules:**

  Can only perform four actions: create a task, send an email, update a field, or send an outbound message.

* **Process Builder:**

  Offers more actions than Workflow Rules, such as creating a record, posting to Chatter, and invoking a flow or Apex. It also allows for multiple criteria and actions in a single process.

#### What is Flow in Salesforce and why is it preferred over Process Builder?

Salesforce Flow is a powerful automation tool that can be used to build complex business processes with a declarative, point-and-click interface. It is preferred over Process Builder because it offers more advanced functionality, including the ability to create screens for user interaction, delete records, and handle more complex logic. Salesforce is actively investing in Flow and has announced the retirement of Workflow Rules and Process Builder.

#### Can Workflow update a related record?

No, a Workflow Rule can only update a field on the same record that triggered the rule or a related master record in a master-detail relationship.

#### What are Approval Processes in Salesforce?

An Approval Process is an automated process that your organization can use to approve records in Salesforce. It specifies the steps necessary for a record to be approved and who must approve it at each step.

#### When would you use Flow over Apex?

You would typically use Flow over Apex when the required automation can be achieved through declarative tools. Flow is generally faster to build and easier to maintain for non-developers. Apex should be used for more complex scenarios that require custom logic, such as complex calculations, web service callouts, or bulk data processing that exceeds Flow's limits.

### 5. Apex & Development Basics

#### What is Apex in Salesforce?

Apex is a strongly typed, object-oriented programming language that allows developers to execute flow and transaction control statements on the Salesforce platform. It has a syntax that is similar to Java. Apex is used to add custom business logic to system events, such as button clicks, record updates, and Visualforce pages.

#### Difference between a Trigger and a Class?

* **Trigger:**

  A piece of Apex code that executes before or after records of a specific object are inserted, updated, or deleted.

* **Class:**

  A blueprint or template from which objects are created. In Apex, classes can contain methods, variables, and other code that can be called from triggers or other classes.

#### What are Governor Limits in Salesforce? Give some examples.

Governor limits are runtime limits enforced by the Apex runtime engine to ensure that any one tenant's code does not monopolize shared resources in the multitenant environment. Examples include:

* Total number of SOQL queries issued (100 in synchronous Apex).
* Total number of DML statements issued (150).
* Total number of records retrieved by SOQL queries (50,000).
* Maximum CPU time on the Salesforce servers (10,000 milliseconds in synchronous Apex).

#### What is a SOQL query?

SOQL (Salesforce Object Query Language) is used to query records from the Salesforce database. It is similar in syntax to the standard SQL SELECT statement.

#### Difference between SOQL and SOSL?

* **SOQL (Salesforce Object Query Language):**

  Used to query records from a single object or multiple related objects. It is best used when you know which objects the data resides in.

* **SOSL (Salesforce Object Search Language):**

  A full-text search language that can be used to search for text, email, and phone fields across multiple objects. It is best used when you are unsure which object or field the data resides in.

#### What are different types of collections in Apex?

The primary types of collections in Apex are:

* **List:**

  An ordered collection of elements of the same data type.

* **Set:**

  An unordered collection of unique elements of the same data type.

* **Map:**

  A collection of key-value pairs where each unique key maps to a single value.

#### What is a Trigger context variable? Give examples.

Trigger context variables are special variables that are available in trigger code to provide information about the runtime context of the trigger. Examples include:

* **`Trigger.new`:**

  A list of the new versions of the records being processed.

* **`Trigger.old`:**

  A list of the old versions of the records being processed in an update or delete trigger.

* **`Trigger.isInsert`, `Trigger.isUpdate`, `Trigger.isDelete`:**

  Boolean variables that indicate the type of DML operation.

* **`Trigger.isBefore`, `Trigger.isAfter`:**

  Boolean variables that indicate if the trigger is running before or after the DML operation.

#### What are Test Classes in Salesforce and why are they important?

Test classes are Apex classes written to test the functionality of your Apex code. They are important for several reasons:

* **Code Quality:**

  They help ensure that your code works as expected and identify bugs early in the development process.

* **Deployment Requirements:**

  Salesforce requires at least 75% of your Apex code to be covered by tests before you can deploy it to a production environment.

* **System Integrity:**

  They help prevent regressions by ensuring that new code changes do not break existing functionality.

### 6. Lightning & UI

#### Difference between Salesforce Classic and Lightning?

* **Salesforce Classic:**

  The original user interface for Salesforce. It has a more traditional, text-based interface.

* **Salesforce Lightning:**

  A more modern, component-based user interface that is more visually appealing and provides a more dynamic and responsive user experience. It offers features like the Lightning App Builder and customizable dashboards.

#### What is a Lightning Web Component (LWC)?

Lightning Web Components (LWC) is a modern framework for building Lightning components on the Salesforce platform. It uses standard web technologies like HTML, JavaScript, and CSS, making it easier for developers with web development experience to build on Salesforce.

#### What are Aura Components and how are they different from LWC?

Aura Components are the original framework for building components in the Lightning Experience. The key differences are:

* **Performance:**

  LWC generally offers better performance as it is built on modern web standards and has less overhead.

* **Standards:**

  LWC is aligned with modern web standards, while Aura has its own proprietary component model.

* **Ease of Development:**

  Developers familiar with modern web development frameworks often find LWC easier to learn and use.

#### What is the use of the Lightning App Builder?

The Lightning App Builder is a point-and-click tool that allows you to create custom pages for the Lightning Experience and the Salesforce mobile app. You can use it to build single-page apps, dashboard-style apps, and "point" apps to solve a particular task.

#### How do you add a custom LWC to a Lightning Page?

You can add a custom LWC to a Lightning Page by editing the page in the Lightning App Builder. Your custom LWC will appear in the "Custom" components section, and you can drag and drop it onto the page canvas.

### 7. Data Management

#### What are Data Import Wizard and Data Loader?

* **Data Import Wizard:**

  A web-based tool available within Salesforce for importing data for common standard objects and custom objects. It can process up to 50,000 records at a time.

* **Data Loader:**

  A client application for the bulk import or export of data. It can handle up to 5 million records and supports all standard and custom objects.

#### When should you use Data Import Wizard vs Data Loader?

* **Use Data Import Wizard when:**

  * You need to load fewer than 50,000 records.

  * The object you need to import is supported by the wizard.

  * You want to prevent duplicates by checking for existing records.

  * You prefer a simple, guided interface.

* **Use Data Loader when:**

  * You need to load between 50,000 and 5 million records.

  * You need to load data into an object that is not supported by the Data Import Wizard (like Opportunities or Cases).

  * You need to export or delete data.

  * You need to schedule regular data loads.

#### What is the Recycle Bin in Salesforce?

The Recycle Bin is a temporary storage area for deleted records. It allows users to view and restore recently deleted records.

#### Can we restore deleted records?

Yes, records in the Recycle Bin can be restored for 15 days before they are permanently deleted. Salesforce administrators can see all deleted data across the entire organization.

#### What are Duplicate Rules and Matching Rules?

* **Matching Rules:**

  Define how duplicate records are identified by comparing field values.

* **Duplicate Rules:**

  Determine what happens when a user tries to save a record that is identified as a duplicate. It can either block the user from saving the record or allow it and create an alert.

### 8. General / Scenario-Based

#### If two users need the same access but belong to different departments, how will you handle it?

You can assign them the same **Profile** to give them the same object and field-level permissions. Their department affiliation and record visibility can be managed through their respective **Roles** in the role hierarchy. If they require additional specific permissions, you can use **Permission Sets**.

#### If you want to give access to a record to a user outside the role hierarchy, how can you achieve it?

You can use **Sharing Rules** to extend access to users in different public groups, roles, or roles and subordinates. Alternatively, for a single record, you can use **Manual Sharing**.

#### You need to send an automatic email when a record is updated—how would you do it?

You can use a **Flow** to trigger an "Email Alert" action when a record is updated and meets specific criteria.

#### How would you design a system where an Opportunity automatically creates a Task?

You can use a **Flow** that is triggered when an Opportunity is created or updated to a certain stage. The Flow would then have a "Create Records" element to generate a new Task record and associate it with the Opportunity.

#### How do you ensure bulk data operations don’t hit governor limits?

To avoid hitting governor limits during bulk data operations, you should follow best practices such as:

* **Bulkifying your code:**

  Write code that can process multiple records at a time.

* **Using SOQL `FOR` Loops:**

  This is an efficient way to query and process large numbers of records.

* **Avoiding DML and SOQL inside loops:**

  Perform database operations outside of loops to minimize the number of queries and statements.

* **Using collections:**

  Use Maps and Sets to efficiently process and store data.

* **Using `@future` or Batch Apex for long-running processes:**

  These allow you to perform asynchronous processing for operations that might exceed governor limits in a synchronous context.

