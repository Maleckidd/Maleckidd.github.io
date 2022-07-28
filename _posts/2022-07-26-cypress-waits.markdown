---
layout: post
title:  "How to cy.wait(enough)"
subtitle: "About waits in Cypress.io "
date:   2022-07-27 21:52:12 +0200
categories: Cypress
background: '/img/posts/01.jpg'
---
How many times you had a problems with page elements that don't appear, too fast assertions, or too fast performed action?  I hope this article will help you solve problems with waits and will allow you to make the first step to a better future and better tests 🙂  I want to take a look at aspects that should help you improve the stability of your tests and make them more atomic. 

**`cy.wait(0)`** <br>
 One of the basic command in cypress is wait with milliseconds. All of us who start our journey with test automatization, have used to use this way to pause the tests, and probably it would be hard to find a repo without it. Very rarely the waiting time will be specified accurately and waiting until declared time probably will be waste of time. Except in situations where you have a good explanation of using it, you should find another approach of waiting. 

>I have encountered problems in a few applications with submitting a completed form. All data was provided but after form confirmation I received an error about incompleted data or even worse that form was saved with missing data. These situations only occured when the confirmation button has been clicked immediately after filling the form out - impossible to reproduce manually (important to be sure this situation never occurs while normal interaction with the app). The solution was to add a 1-sec wait before using the confirmation button.


**`cy.wait(@)`** <br>
A second way of using the command cy.wait() is for waiting for @alias (or aliases). In that case, mostly you will use it in a combination with cy.intercept() command. Before performing action resulting in network communication you set cy.intercept() to spy network communication, search and assign an alias for a request which means that the backend has performed the expected action. Then cypress performs the action and waits till cy.intercept sign alias for expected request. 


<div style="background-color:lightgrey;">
{% highlight javascript %}
cy.intercept('/allFruits').as('getAllFruits')
cy.contains('Click for get all fruits').click()
cy.wait('@getAllFruits')
//You can wait for few aliases in one command cy.wait(['@getAllFruits', '@getAllVegs'])
{% endhighlight %}
</div>


**`cy.get(all)`** <br>
The first example of waiting without using cy.wait is simple cy.get. If you need to wait utill the element exists or not, is visible or covered, etc... 

<div style="background-color:lightgrey;">
{% highlight javascript %}
cy.get('el')
{% endhighlight %}
</div>

or

<div style="background-color:lightgrey;">
{% highlight javascript %}
cy.get('el').should('be.visible')
{% endhighlight %}
</div>

Possibly that element appears after cypress reached a timeout limit, what's now? 
There is an easy way to extend your default command timeout. You can use a timeout option. 

<div style="background-color:lightgrey;">
{% highlight javascript %}
cy.get('el', {timeout: 10000}).should('be.visible')
{% endhighlight %}
</div>

There is a possibility of changing default timeouts for all or selected tests but I'm not a fan of increasing timeouts globally - you should assume minimal, as low as possible, general timeout which will not affects tests. That assumption may be useful when the performance of your application will fall dramatically. Of course, you you might have some actions that takes a while longer, and that's why I present an increasing timeout for specific actions.

**`Cypress-recurse`** <br>
Sometimes, tests are faster than application. Visiting a page too early may cause fail of assertions or some error like 404, on another hand static wait may be the cause of a significant increase of test time overall. You can write a function with command and use recursion with the condition. But this is not a perfect solution, tests can stuck in infinite loop and you can't set timeouts for the whole operation. Adding some counters and more logic to limit the count of attempts is possible, but it makes code more complicated. 

<div style="background-color:lightgrey;">
{% highlight javascript %}
checkIsReady() {
    cy.request({
       url: checkIsReadyUrl,
       failOnStatusCode: false
    }).then((res) => {
            if(res.code !== 200) {
                cy.wait(1000)
                this.checkIsReady()
            }
       }
}
{% endhighlight %}
</div>

Another option more readable and customizable. One of Cypress creators developed an extension for it - <a href="https://github.com/bahmutov/cypress-recurse">cypress-recurse</a>. It allows re-running cypress commands till the condition is met. You can set a limit of iterations, the delay between repetitions, and timeouts. 

So, if you want to check if page is ready (status code is 200) for a maximum of 10 times and each iteration should go with a 1sec delay but all loop should take less than 15 seconds, here is your answer:

<div style="background-color:lightgrey;">
{% highlight javascript %}
recurse(
        () =>
          cy.request({
            url: checkIsReadyUrl,
            failOnStatusCode: false
          }),
        (res) => expect(res.status).to.be.eq.(200), { delay: 1000, limit: 10, timeout: 15000 },
      )
{% endhighlight %}
</div>

What if its response status code is not enough?  No problem! Simple logic let it works. 

<div style="background-color:lightgrey;">
{% highlight javascript %}
recurse(
        () =>
          cy.request({
            url: checkIsReadyUrl,
            failOnStatusCode: false
          }),
        (res) => expect(res.status).to.be.eq.(201) &&
                 expect(res.body).to.have.proprety('isReady', true), 
                { delay: 1000, limit: 10, timeout: 15000 },
      )
{% endhighlight %}
</div>

But please remember! Use re-tries in a responsible way. Don't abuse this, it shouldn't be a solution in situations where the application under test behaves in unpredictable ways. Used unproperly can reduce the effectiveness of your tests and black out the results.