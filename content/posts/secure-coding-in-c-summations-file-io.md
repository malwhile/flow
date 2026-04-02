---
author: "Halvo (Human)"
title: "Concurrency: Summations of Secure Coding in C and C++"
date: 2023-06-29
tags:
  - file-io
  - secure-coding
  - c
  - cpp
  - permissions
  - least-privilege
draft: false
summary: |
  A breezy guide to keeping file operations safe: validate paths, lock down permissions, and never let a privileged process wander into a user’s temp folder.
---

## Introduction

Continuing summarizing the themes in "Secure Coding in C and C++" by Robert C. Seacord, we will discuss file I/O and how to prevent unauthorized access. File I/O is especially dangerous when a program is running under a privileged context and accesses files that unprivileged users can access. This can lead an attacker to read or even overwrite privileged files.

The tl;dr; here is, use proper file permissions, verify file paths, and use the principle of least privilege.

This post assumes basic knowledge of file system permissions and how paths are determined.

## Big Issues

There are several issues that can arise while attempting to access files on the system:

1. [User input providing paths they shouldn't have access to](#unauthorized-path-access)
1. [Setting bad file permissions](#bad-file-permissions)
1. [Accessing unpriveleged files from privileged process](#principle-of-least-privilege)

Without properly handling these three primary issues, a process could leak information or provide a path for an attacker to alter system files.

## Unauthorized Path Access

### Manipulated Paths

Similar to SQL injection, a user can manipulate a path to attempt to access locations they shouldn't otherwise be able to access. The classic example is using the `..` notation to go up a directory level. Using multiple `../../../../` will eventually reach the root of the system, allowing a malicious user to access the entire system.

There are other more subtle ways to perform these types of operations as well. While Unix based systems are generally case sensitive, Windows and certain programs are not, thus `../../../PRIVATE` is the same as `../../../private`. 

Some systems will also evaluate `.../......./` to `../../` bypassing some checks.

There are enough ways to perform directory traversal that it becomes difficult to filter all of them. Thus the solution is similar to SQL injection, don't filter. Simply sanitize the input. For SQL injection, that means escaping the input, for directory traversal, it's requesting the underlying system to give the absolute path.

By requesting the absolute path, all these tricks are flattened and returns a standard path. Then the program can verify it should be accessing that path.

## Bad File Permissions

On the surface this one is pretty simple. When creating a file, give it the most restrictive access possible for functionality to continue. By limiting access a malicious actor will have a harder time viewing and manipulating the data. And this should definitely be done, but there are some more subtle ways to keep things secure.

There are other file attributes that need to be considered. By checking and storing things like the inode number, link status, and device id, there is more assurance that this is the correct file and hasn't been replaced. 

## Principle of Least Privilege

Keep the program running as an unprivileged user and only request more privileges when needed. This is good advice for any program, but comes in handy for file IO.

In this case, when accessing a globally accessible file (such as in `/tmp`) the program should NOT be running privileged. This is because a malicious actor could replace the `/tmp` file with a symbolic link to a privileged file. The program will happily open that link and read or modify the contents.

If the program is running unprivileged when accessing these unprivileged files, it would get a file system error. This will prevent the program from accessing files out of scope.

## Conclusion

There are a few takeaways from exploring issues with File I/O. 

First (and this is true for everything) never trust user input. Anything that could be coming from the user should be verified, even if there are controls on the user interface. In context of File I/O, this means verifying file names and paths. Always expand the path, then verify that the path is valid. Un-expanded paths can have numerous ways to hide the actual path the OS will use.

Next, make sure any files created have the most restrictive permissions possible. This way only the process owner can read, write, and delete the file.

Finally, only run the process at the least amount of privileges, only escalating those privileges when absolutely necessary. This way if an attack attempts to trick the process to accessing privileged files, it will not be able to.

Using these three techniques should help stave off the worst of the File I/O issues.
