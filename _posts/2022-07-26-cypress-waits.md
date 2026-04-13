---
layout: post
title: "How to cy.wait(enough)"
date: 2022-07-27 21:52:12 +0200
description: "Practical guide to waits in Cypress — from static waits to smart retries with cypress-recurse."
tags: [cypress, test-automation]
giscus_comments: false
related_posts: true
thumbnail: assets/img/posts/01.jpg
---

How many times have you experienced problems with page elements not appearing, assertions executing too quickly, or actions being performed too fast? I hope this article will help you solve these issues with waits and take the first step towards a better future with more stable and atomic tests. Let's take a closer look at aspects that can help improve test stability and make them more reliable.

**`cy.wait(0)`**

One of the basic commands in Cypress is "wait" with milliseconds. As beginners in test automation, we often use this method to pause tests, and it's likely that we won't find a repo without it. However, the waiting time is very rarely specified accurately, and waiting until a declared time may be a waste of time. Unless there is a good reason to use it, you should look for another approach to waiting.

**`cy.wait(@)`**

Another way to use the command `cy.wait()` is to wait for `@alias` (or aliases). In this case, you will mostly use it in combination with the `cy.intercept()` command. Before taking any action that involves network communication, you set `cy.intercept()` to spy on network communication, search for and assign an alias for a request that confirms that the backend has performed the expected action. Then, Cypress performs the action and waits for the `cy.intercept` command to sign the alias for the expected request.

```javascript
cy.intercept('/allFruits').as('getAllFruits')
cy.contains('Click for get all fruits').click()
cy.wait('@getAllFruits')
//You can wait for few aliases in one command cy.wait(['@getAllFruits', '@getAllVegs'])
```

**`cy.get(all)`**

The first example of waiting without using `cy.wait` is with `cy.get`. If you need to wait until the element exists, is visible or uncovered, etc...

```javascript
cy.get('el')
```

or

```javascript
cy.get('el').should('be.visible')
```

What if the element appears after Cypress reaches a timeout limit? There is an easy solution — you can extend your default command timeout by using a timeout option.

```javascript
cy.get('el', {timeout: 10000}).should('be.visible')
```

It's possible to change default timeouts for all or selected tests, but I'm not a fan of increasing timeouts globally. You should assume a minimal, as low as possible, general timeout that will not affect tests. This assumption may be useful when the performance of your application falls dramatically. However, you might have some actions that take a while longer, and that's why I suggest increasing the timeout for specific actions.

**`Cypress-recurse`**

Sometimes tests run faster than the application they are testing. If you visit a page too early, it can cause assertions to fail or errors like 404. However, using a static wait can significantly increase overall test time.

A better option is to use an extension developed by one of the creators of Cypress — [cypress-recurse](https://github.com/bahmutov/cypress-recurse). It allows for re-running Cypress commands until a certain condition is met. It offers the ability to set limits on the number of iterations, the delay between repetitions, and timeouts.

For example, if you want to check if a page is ready (status code 200) up to a maximum of 10 times with a 1-second delay between each iteration, but want the entire loop to take less than 15 seconds, this extension can provide the solution.

```javascript
recurse(
  () =>
    cy.request({
      url: checkIsReadyUrl,
      failOnStatusCode: false
    }),
  (res) => expect(res.status).to.be.eq.(200),
  { delay: 1000, limit: 10, timeout: 15000 },
)
```

What if its response status code is not enough? No problem! Simple logic makes it work.

```javascript
recurse(
  () =>
    cy.request({
      url: checkIsReadyUrl,
      failOnStatusCode: false
    }),
  (res) =>
    expect(res.status).to.be.eq.(201) &&
    expect(res.body).to.have.property('isReady', true),
  { delay: 1000, limit: 10, timeout: 15000 },
)
```

But please remember, use retries in a responsible manner. Don't abuse it as a solution for situations where the application under test behaves unpredictably. Improper use can reduce the effectiveness of your tests and obscure the results.
