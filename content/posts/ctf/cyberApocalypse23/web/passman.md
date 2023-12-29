---
title: "Web"
date: 2023-01-26
layout: "simple"
---

### Passman

> **Name**: Passman
> 
> **Dificulty:** Easy
{: .prompt-info }

This one was a tricky one. I noticed that it was using graphql when I logged in, register registered a user. Not to familiar with this database but I used 2 things to help me understand how the database was setup. I used **[GraphlQLMap](https://github.com/swisskyrepo/GraphQLmap)** and **[Hacktricks GraphQL](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql)**. This is the output of GraphqlMap: 

```bash 
solidshadw@pop-os:~/Downloads/web_passman$ graphqlmap -u "http://138.68.162.218:30735/graphql" --proxy http://127.0.0.1:8080
   _____                 _      ____  _                            
  / ____|               | |    / __ \| |                           
 | |  __ _ __ __ _ _ __ | |__ | |  | | |     _ __ ___   __ _ _ __  
 | | |_ | '__/ _` | '_ \| '_ \| |  | | |    | '_ ` _ \ / _` | '_ \ 
 | |__| | | | (_| | |_) | | | | |__| | |____| | | | | | (_| | |_) |
  \_____|_|  \__,_| .__/|_| |_|\___\_\______|_| |_| |_|\__,_| .__/ 
                  | |                                       | |    
                  |_|                                       |_|    
                              Author: @pentest_swissky Version: 1.1 
GraphQLmap > help
[+] dump_via_introspection : dump GraphQL schema (fragment+FullType)
[+] dump_via_fragment      : dump GraphQL schema (IntrospectionQuery)
[+] nosqli      : exploit a nosql injection inside a GraphQL query
[+] postgresqli : exploit a sql injection inside a GraphQL query
[+] mysqli      : exploit a sql injection inside a GraphQL query
[+] mssqli      : exploit a sql injection inside a GraphQL query
[+] exit        : gracefully exit the application
GraphQLmap > dump_via_introspection
============= [SCHEMA] ===============
e.g: name[Type]: arg (Type!)

00: Query
	getPhraseList[Phrases]: 
01: Phrases
	id[]: 
	owner[]: 
	type[]: 
	address[]: 
	username[]: 
	password[]: 
	note[]: 
04: Mutation
	RegisterUser[]: email (String!), username (String!), password (String!), 
	LoginUser[]: username (String!), password (String!), 
	UpdatePassword[]: username (String!), password (String!), 
	AddPhrase[]: recType (String!), recAddr (String!), recUser (String!), recPass (String!), recNote (String!), 
05: Response
	message[]: 
	token[]: 
07: __Schema
08: __Type
10: __Field
11: __InputValue
12: __EnumValue
13: __Directive
GraphQLmap > 
```

I'm passing it through my proxy, so that it inherits the session/cookies of the user I had created.

I tried to see if I could register over the admin user but got no luck. I then procedeed to uset the `UpdatePassword` mutation. This was the query I sent over with Burp.

![image](/assets/img/20230321221336.png)

I then saw "Password updated successfully". I changed the admins password. I then proceed to login and extracted the flag.

![image2](/assets/img/20230321221449.png)

```bash
HTB{1d0r5_4r3_s1mpl3_4nd_1mp4ctful!!}
```