## Objective: 
To understand how path traversal attacks are carried out and carry one out in a controlled environment

## Introduction: 
File path traversal is an attack method where an attacker manipulates a file path input to access files outside the directory an application intends to expose. This can include application source code, configuration files or credentials. In this walkthrough, I’ll demonstrate a simple path traversal exploit against a PortSwigger lab, with the steps taken to identify and exploit the vulnerability.

## Initial Reconnaissance:
1.	Open the lab
2.	Browse the posts
3.	Right click on an image and select inspect
4.	Locate the <img> tag in the HTML and observe how the’ src attribute reveals an internal file path
5.	Identify the ‘filename’ parameter as untrusted input used to build a file path
6.	Send the image request to Burp Suite and forward it to Repeater
 
 ## Exploitation Steps:
1.	Open a new lab instance (Note: PortSwigger lab instances can time out and cause previously captured requests to stop returning valid responses. Hence, a fresh lab instance is opened)
2.	Click an image of a post
3.	Locate the request for the image in the HTTP history tab on Burp Suite and send to repeater
4.	Replace the value of the filename parameter with ‘/../../../etc/passwd’

## Successful Payload: 
‘/../../../etc/passwd’

## Why Payload Worked: 
../ represents navigating one directory level up in the file system. By repeating this sequence, the requested path traverses beyond the intended image directory. //etc/passwd’ specifies the target file the attacker attempts to access.

## Outcome: 
•	The Repeater response returned the contents of /etc/passwd, exposing system account information, including usernames and associated home directories.

•	This demonstrated that the application allowed access to a file outside its intended image directory.

•	The PortSwigger lab was marked as Solved

## Key Learning Points:
Through this lab I learned that many common validation strategies fail to fully prevent path traversal attacks.

•	No validation at all offers no protection

•	Validating a filename before decoding it means dangerous input can appear only after the check is already done, letting it slip through unnoticed.

•	Requiring a path to start in an approved directory doesn’t solve the problem as the attacker can still modify directories and filenames afterwards

•	Blocking known directory traversal patterns can still allow absolute paths through

•	Non-recursive stripping of certain characters in a file name will let dangerous file paths form after the first stripping

•	Requiring a filename to end in a particular extension can be bypassed on older or unsafe systems that interpret termination characters differently

## Mitigation:
•	Validate user given filenames against an allowlist of permitted values.

•	Canonicalize and normalize file paths before performing validation.

•	Verify resolved file path remains within intended directory.
