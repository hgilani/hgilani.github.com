---
layout: post
title: "Introduction to LDAP"
date: 2013-01-12 01:53
comments: true
categories: ["LDAP", "DIT"] 
---
LDAP is definitely not a buzz word technology these days, however a lot of organizations are still using LDAP. So, at some point in time you may be required to develop or maintain projects using LDAP. But since LDAP is an archaic technology, it is tough to discover right information about it on the web. Whenever I searched for it on the web, I found the right information filled with technical jargon and scattered across various articles. This post is my attempt at explaining everything you need to know to understand how LDAP protocol works.

<!-- more -->

## What is LDAP?

LDAP stands for **Lightweight Directory Access Protocol**. It is an Internet protocol for accessing distributed directory services. Using LDAP organizations can organize their users and resources into a hierarchical directory (or tree) structure. Benefits of this organization is that it can act as a centralized source of information for the organization there by providing following services

1. Organize contact information of users into directories. Think of this as an address book. 
2. Resources like printers, scanners, meeting rooms , etc can also be organized in LDAP.
3. LDAP supports ACLs i.e. you can control what can a user access in the directory.
4. LDAP can store login credentials of users, there by acting as a company wide authentication and authorization database. And as such a lot of organizations use LDAP with their Single Sign On (SSO) integration. 

## Common LDAP Attributes and Acronyms

Now that we understand why an organization would use LDAP, lets get familiar with some of the common LDAP Acronyms and Attributes so that we can discuss LDAP easily.

### LDAP Acronyms

1. DIT: Directory Information Tree - A DIT is hierarchical tree structure which has Distinguished Names (DNs) for directory service entries i.e. users, resources, departments, etc. 
2. LDIF: LDAP Data Interchange Format - A simple data format that can be used for representing information stored in DIT. LDIF is commonly used for export and import operations on DIT.
3. DN: Distinguished NAME - An unique identifier for each entry in the DIT. A DN is composed of series of RDNs found by walking up the directory tree.
4. RDN: Relative Distinguished Name - A relative distinguished name (RDN) represents a single node of a DN. Thus if you combine RDNs walking up DIT you can form DN.

### LDAP Attributes

Each node in DIT can use different type of attributes for storing informations. Following are some of the frequently used attributes

1. dc: domain component    
2. o: organization
3. ou: organizational Unit
4. cn: common Name
5. sn: surname
6. uid: userid    
7. l: location    
8. st: status    
9. c: country

## DIT nodes, attributes, and objectclass

To Understand how a DIT is represented look at the following hypothetical tree. 

{% img /images/dit.png [Directory Information Tree [Directory Information Tree]] %}

Observe how the DIT is organized starting at the "domain component(dc)" at the root level, followed by "organizational unit(out)" i.e. departments. Departments hold persons which can be described using "common name(cn)" attributes. Following illustration shows the "dn of each level in the DIT. Notice how each node includes "rdn" of the parent in its "dn".

{% img /images/dn.png [LDAP Distinguished Name [LDAP Distinguished Name]] %}

In the above illustration we used only handful of attributes, but any number of attributes can be used to store information at any node. Some of the commonly used attributes are sn, uid, l, st, and c. 

In addition to standard attributes, LDAP also has the concept of "objectclass" i.e. schema. An object class specifies what set of mandatory and optional attributes can be used to describe a node in DIT. For example, if you create an object class printer then it could contain attributes that can define printer such as printerid, printername, ip, and modelno. An object class can inherit from other object classes and and all object classes inherit from the abstract object class top. In essence, objectclass is really there to enforce consistency and maintain integrity of the DIT. For more information on objectclass read [Object Classes](http://publib.boulder.ibm.com/infocenter/iseries/v5r3/index.jsp?topic=%2Frzahy%2Frzahyobjectclass.htm)

{% img /images/objectclass.png [LDAP Object Class [LDAP Object class]] %}

LDIF is the data interchange format that can be used to represent a DIT. In this format our example DIT will look like as below

    # domain component level
    dn: dc=mementodb,dc=com
    objectclass: organization
    objectclass: dcObject

    # organization unit level
    dn: ou=IT, dc=mementodb,dc=com
    objectclass: organizationalUnit
    ou: IT

    dn: ou=SALES, dc=mementodb,dc=com
    objectclass: organizationalUnit 
    ou: SALES

    # person level
    dn: cn=Bob Jones, ou=IT, dc=mementodb,dc=com
    objectclass: person
    cn: Bob Jones

    dn: cn=Paul Hunt, ou=IT, dc=mementodb,dc=com
    objectclass: person
    cn: Paul Hunt

For more information on DIT and LDAP organization refer to [Introduction to LDAP](http://coewww.rutgers.edu/www1/linuxclass2003/lessons/lecture8.html) and [Introduction to LDAP](http://quark.humbug.org.au/publications/ldap/ldap_tut.html)

## Working with LDAP

LDAP is a connected protocol. What this means is that you first need to create a socket connect to the LDAP server before invoking any operation. Following is a typical sequence of operations when working with LDAP

1. connect: Create a socket connection
2. bind: Set a base DN, all subsequent operations will be performed relative to this DN. For binding to a DN you also need to authenticate. Please note that you can authenticate anonymously as well. Authenticating creates a session.
3. execute LDAP operations
    - create or add
	- delete
	- modify
	- search or filter
4. unbind - remove session
5. disconnect - Release the socket connection 

For information on how to perform these operations using Java language refer to [Five Minutes Tutorial](http://directory.apache.org/api/five-minutes-tutorial.html)

If you have any thoughts or any concept that you would like me to incorporate in this tutorial then please feel free to leave a comment.

Follow me on twitter [here](http://twitter.com/hgilani)