---
layout: post
title: "Hacking of e-commerce platform"
date: 2022-11-09 22:45:00 +0200
description: "My first bug bounty report: 10 security findings in an e-commerce platform and what I learned from the process."
tags: [security, bug-bounty]
giscus_comments: false
related_posts: true
thumbnail: assets/img/posts/02.jpg
---

On the wave of increased interest and hype surrounding security topics, I decided to try my hand at the bug bounty program for an e-commerce web application aimed at sellers. As a QA, I had already found some security flaws, so based on my experience, I was optimistic about finding some bugs, maybe not security-related, but still significant. I was excited to gain new experience and help eliminate some bugs from the internet (if they were worth fixing).

**`Target`**

The product I was testing allowed sellers to create their own shops, manage orders, clients, and stock. On the opposite side, clients could manage their own accounts, make purchases, and access their cart.

**`Tools`**

The only tools I used while testing were Chrome/Firefox browsers and Burp Suite Community Edition.

**`Preparations and tests`**

I conducted a general overview of functionalities and exploratory tests. I chose a limited scope and time box for each functionality. When flaws began to surface, I focused on the account management for administrators and clients.

**`Report`**

**1. Administrator panel (x/admin/edit/id/1) — changing the password in the account settings does not expire active sessions.**

1. I logged into two separate active sessions
2. On one of them I changed the administrator password
3. The other session was still active.

If the password is changed, other sessions should be invalidated. If the password/session is stolen, despite the password change, the unwanted session is still active.

**2. Administrator panel — administrator password reminder does not expire active sessions.**

1. I logged into the administrator account.
2. I performed a password reset (x/admin/auth/reminder) on an independent browser.
3. The session from before the password reminder (Step 1) was still active.

If the password is changed, other sessions should be invalidated.

**3. Admin Panel — Password reminder (admin/auth/reminder) — Admin email enumeration.**

1. Using the password reminder option (x/admin/auth/reminder) I enter an email that does not exist in the database (expl@exmpl.pl)
2. The application returns a message about an invalid e-mail ("The specified e-mail is invalid")
3. Then I enter an e-mail address that exists in the database (existing@email.com)
4. The application returns the information that the link has been sent ("The email with the link to change the password has been sent")

The password reminder option should return a single neutral message regardless of whether the email exists in the database.

**4. Admin Panel — Edit basic data — changing email address does not require confirmation of the new email address (required at registration).**

When registering, confirmation of the email address is required, but when changing the email from the admin panel (x/admin/edit/id/1), the administrator does not need to confirm the new address.

**5. Customers — changing the password in Profile Edit (x/panel/edit) does not expire active sessions.**

1. I logged into two separate active sessions.
2. On one of them I changed the client's password.
3. The second session was still active.

If the password is changed, other sessions should be invalidated.

**6. Customers — password reminder does not expire active sessions.**

1. I logged into the client's account.
2. On an independent browser, I performed a password reset (x/passremind).
3. The session from before the password reminder was still active.

**7. Customers — Editing email in customer account — ability to enumerate store customers' emails.**

1. When editing an email (x/panel/edit).
2. The user enters the email that exists in the customer database.
3. The application responds with the information "There is already an entry email@gmail.com in the store."

It is good practice to avoid revealing information about the existence of accounts with a given email address.

**8. Customers — Password reminder (x/passremind) — ability to enumerate store customers' emails.**

1. Using the password reminder option I enter an email that does not exist in the database.
2. The application returns information about an invalid e-mail ("Such e-mail address was not found in the database.").
3. Then I enter an e-mail address found in the database.
4. The application returns information that the link has been sent ("The email has been sent").

**9. Customers — Registration (x/passremind) — ability to enumerate emails of store customers.**

1. When registering a new customer, I enter the email address of an existing customer.
2. The application returns the message "There is already an entry existing@gmail.com in the store".

A more neutral message like "The email address is invalid or has already been used" would prevent confirming its presence in the database.

**10. Customers — edit profile (x/panel/edit) — change email address — lack of confirmation of email address (required at registration).**

When registering, confirmation of the email address is required, while changing the email from Profile Edit (x/panel/edit), the customer does not need to confirm the new address.

**`PRICE`**

None of the reported bugs were considered worthy of a prize. However, the person responding to my report thanked me and offered a 1-year subscription with an invitation for further testing.

Although I haven't returned to testing this application, my subscription is still active. Therefore, I may have the opportunity to write new posts about future reports.
