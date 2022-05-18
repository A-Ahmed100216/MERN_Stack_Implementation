# MERN Stack Implementation

A MERN webstack consists of the following componenets:
1. MongoDB - NoSQL database, stores application data as documents 
2. ExpressJS - Sever side web app framework for Node.js
3. ReactJS - Frontend framework based on JavaScript. Used to build User Interface.
4. Node.js - JavaScript runtime environment. Used to run JavaScript on a machine rather than in a browser.


## Database Management Systems (DBMS)
* Software systems used to store, retrieve and query data.
* Act as an interface between a user and data, allowing the user to create, read, update, and delete (CRUD) data. 
* A DBMS manages data, the database engine, and the database schema. 
* Data is organised by following normalisation. This is a database schema design technique which splits large tables into smaller tables reducing redundant columns. 
* DBMS offer flexibility and complex backup systems providing an advantage over traditional filing systems. 
* There are various different types of DBMS. The most popular include:
1. Distributed - Set of logically interrelated databased distributed through a network but managed by a central database application. Data is synchronised and periodically and changes are updated throughout the network. 
2. Hierarchical - Data organised in a tree-like structure. Data storage represented using parent-child relationship. 
3. Network - Addresses complex relationships - each child can have multiple parents. Entities organised in a graph. 
4. Relational - Most popular model because of its user friendly interface. Based on normalising data. Offers scalability, flexibility, and the ability to manage a lot of informations. 
5. Object-oriented - Stores data in objects instead of rows and columns. Based on OOP and allows objects to have fields, properties. and methods. 
* Popular DBMS include:
1. Oracle - Commercial relational DBMS. Can be stored in cloud or on-prem. 
2. MySQL - Relational DBMS. Used by Facebook, Twitter, and Youtube.
3. SQL Server - Relational DBMS built upon SQL. Developed by Microsoft.  
* The term NoSQL is often used to describe any non-relational database. 

## Web Application Frameworks
* Software Framework designed to support development of web apps (web services, resources and APIs)
* Libraries to help develop web apps. 
* Many frameworks available in a range of languages:
1. Ruby on Rails - productive web app framework for Ruby. Used by UrbanDictionary, AirBnb, and GitHub.
2. Django - Fast, secure, scalable, and versatile framework for Python. Used by Pinterest, Instagram, and Quora. 
3. Angular (Angular JS) - Framework by Google for JavaScript. Used by Netflix.
* Server-side Frameworks typically consist of a programming language and a compiler. They run on a web server such as Node, or PHP.
* Client-side frameworks are usually a JavaScript framework which runs in a web browser i.e. React, or Angular. 
### REST API
* Application Programming Interface (API) which conforms to REST Architecture.
* REST - representational state transfer created by Roy Fielding. 
* In order to be considered REST, must adhere to the following requirements:
    * Client server architecture consists of clients, servers, and resources. Requests managed by HTTP
    * Stateless client-server communication - each request is separate and unconnected. 
    * Cacheable data
    * Uniform interface between components.
    * Layered system that organises each type of server.  



## References

[Appdynamics](https://www.appdynamics.com/topics/database-management-systems#~2-examples-of-dbms)
[GeeksforGeeks](https://www.geeksforgeeks.org/top-10-frameworks-for-web-applications/)
[Rosencrance L, 2019](https://www.techtarget.com/searchapparchitecture/definition/client-side-framework)
[Red Hat, 2020](https://www.redhat.com/en/topics/api/what-is-a-rest-api)