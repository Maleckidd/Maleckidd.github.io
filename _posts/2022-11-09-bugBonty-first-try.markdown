---
layout: post
title:  "Hacking of e-commerce platform"
subtitle: "About my 1st bug bounty report"
date:   2022-11-09 22:45:00 +0200
categories: Security
background: '/img/posts/02.JPG'
---
On a wave of increased interest and hype for security topics, I decided to try my hand at the bug bounty program, a web application for e-commerce sellers. As a QA, I have found some security flaws, so based on my experience, I was pretty optimistic to find some bugs, maybe not security ones but still. Finally I will get some new experience and also get rid of some bugs from the internet (of course if they will be worth fixing) 💪

**`Target`** <br>
The product which I was tasting, was for sellers to create their own shop, manage orders, clients and stock. Also on the opposite side was the client's point of view like cart, purchases and managing their own account data. 

**`Tools`** <br>
The only tools I was using while testing was browsers chrome/firefox and Burp Suite Community Edition.

**`Preparations and tests`** <br>
I made a general overview of functionalities and exploratory tests. I decided to choose a limited scope and time boxes dedicated to each of the functionalities. When flaws began to surface, finally I focused on administrators and clients account management. 

**`Report`** <br>
<b>1. Administrator panel (x/admin/edit/id/1) - changing the password in the account settings, does not expire active sessions.</b>
		
1. I logged into two separate active sessions
2. On one of them I changed the administrator password
3. The other session was still active.

If the password is changed, other sessions should be extinguished, if the password/session is stolen, despite the password change, the unwanted session is still active.

<b>2. Administrator panel - administrator password reminder does not expire active sessions</b>
		
1. I logged into the administrator account.
2. I performed a password reset (x/admin/auth/reminder) on an independent browser,
3. The session from before the password reminder (Step 1) was still active.

If the password is changed, other sessions should be extinguished, if the password/session is stolen, despite the password change, the unwanted session is still active.

<b>3. Admin Panel - Password reminder (admin/auth/reminder) -  Admin emails enumeration.</b>
		
1. Using the password reminder option (x/admin/auth/reminder) I enter an email that does not exist in the database (expl@exmpl.pl)
2. The application returns a message about an invalid e-mail ("The specified e-mail is invalid")
3. Then I enter an e-mail address that exists in the database(existing@email.com)
4. The application returns the information that the link has been sent ("The email with the link to change the password has been sent")

The password reminder option can contain a single message with information about the link being sent correctly, even if an email was incorrect. This ensures that valuable information is not passed on to an attacker trying to break into the account.

<b>4. Admin Panel - Edit basic data - changing email address does not require confirmation of the new email address (required at registration).</b>

When registering, confirmation of the email address is required, but when changing the email from the admin panel (x/admin/edit/id/1), the administrator does not need to confirm the new address.

<b>5. Customers - changing the password in Profile Edit (x/panel/edit) does not cause active sessions to expire (if the password/session is stolen despite the password change, the unwanted session is still active)</b>
	  
1. I logged into two separate active sessions.
2. On one of them I changed the client's password,
3. The second session was still active.

If you change the password, other sessions should be extinguished, if the password/session is stolen, despite the password change, the unwanted session is still active.

<b>6. Customers - password reminder does not expire active sessions
(if the password/session is stolen, despite changing the password, the unwanted session is still active)</b>
		
1. I logged into the client's account
2. On an independent browser, I performed a password reset (x/passremind),
3. The session from before the password reminder was still active.

When changing the password, other sessions should be extinguished, if the password/session is stolen, despite the password change, the unwanted session is still active.

<b>7. Customers - Editing email in customer account - Ability to enumerate store customers' emails.</b>
		
1. When editing an email (x/panel/edit). 
2. the user enters the email that exists in the customer database. 
3. the application responds with the information "There is already an entry email@gmail.com in the store."

It is good practice to avoid revealing information about the existence of accounts with a given email address.

<b>8. Customers - Password reminder (x/passremind)- Ability to enumerate store customers' emails</b>
		
1. Using the password reminder option I enter an email that does not exist in the database, (exmpl@exmpl.exmpl)
2. The application returns information about an invalid e-mail ("Such e-mail address was not found in the database.").
3. Then I enter an e-mail address found in the database, 
4. The application returns information that the link has been sent ("The email has been sent").

The password reminder option can contain a single message with information about the link being sent correctly, even if an email was incorrect. This ensures that valuable information is not passed on to an attacker trying to break into the account.

<b>9. Customers - Registration (x/passremind) - Ability to enumerate emails of customers of the store.</b>
		
1. When registering a new customer, I enter the email address of an existing customer,
2. the application returns the message "There is already an entry existing@gmail.com in the store".

It is worth changing the message to not one significant, such as "The email address is invalid or has already been used.", so that it does not simply confirm its presence in the database - making it difficult to attack 

<b>10. Customers - edit profile (x/panel/edit) - change email address - lack of confirmation of email address (required at registration)</b>
		
When registering, confirmation of the email address is required, while changing the email from Profile Edit (x/panel/edit), the customer does not need to confirm the new address.

**`PRICE`** <br>
None of the reported bugs was registered as prize-worthy. Person person responding to my report thanked me and add a 1-year subscription with an invitation for further tests. 

I haven't back to tests of this app, the subscription is still active, so maybe I will have the opportunity to write new posts about the next reports.