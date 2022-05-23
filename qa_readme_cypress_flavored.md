# Wild and Wooly World™ of ${project_name} QA development

Hello.

And welcome.

//edit insert business/project specific intro here

---

## General Overview

Once you get settled in and acclimated to ${project_name} as a whole your time will be split between two main testing phases:

1.  Manual testing
2.  Cypress testing

---
## Manual Testing

Manual testing is exactly what it sounds like - manually testing features/bug fixes to validate their performance against the business's
[acceptance criteria](https://en.wikipedia.org/wiki/Acceptance_testing) and then going beyond the confines of the [happy path](https://en.wikipedia.org/wiki/Happy_path)
in an attempt to identify broken edge case scenarios. This testing is generally limited to the scope of the feature/bug fix but can
expand to obviously related features that may or may not have been directly touched in the code itself (for example, if there is
a change made to ${project_feature} of ${page}, might not be a bad idea to check other areas ${project_feature} is used).

Say a feature card introduces a Whole Positive Number Addition Page. 
The AC says: 
> 1. On the Whole Positive Number Addition Page there are two user input fields, a submit button, and a result field.
>    1. When users enter whole positive numbers into the input fields and presses 'Submit'
>    2. The two numbers will be added and the result will display in the result.

You load up the page

    [  2  ]  +   [  3  ]  =  [  5  ]

Seems easy enough, how could anyone mess this up?

Let us count the ways...


    [  two  ]  +   [  three  ]  =  [  error  ]
    [  two  ]  +   [  3  ]  =  [  error  ]
    [  2.2  ]  +   [  3  ]  =  [  error  ]
    [  2  ]  +   [  -3  ]  =  [  error  ]
    [  null  ]  +   [  3  ]  =  [  error  ]
    [  2  ]  +   [  3%  ]  =  [  error  ]
    [  2  ]  +   [  1/3  ]  =  [  error  ]
    [  2 + 3  ]  +   [  null  ]  =  [  error  ]
    [ www.virus.com/exe ] + [  3  ] = [  error  ]

And every combination of the above plus probably 10 more I haven't thought of. The point is - Your goal is to ensure what the client wants to happen is happening and
what they don't want isn't. Now after spending 5 hours of your time (and the client's money) testing and documenting every possible combination of errant inputs, 
you proudly go to your dev team with the results.

Don't get me wrong, the exhaustive list is impressive, but you could have saved a lot of your time and energy by instead saying at the beginning "Hey, we should probably 
implement regex validation on the input so it only accepts whole positive integers and nothing funky". Being on the lookout for solutions to problems is yet another skill to 
develop for your toolbox.
### Reporting a Bug

The most important aspect of bug reporting is having clear, concise, detailed information. Giving the dev as much context as possible will help
expedite the understanding, diagnosing, and solving of a given issue. Make a habit of always browsing with your console or network tabs open
to give you better insight into the machinations of the project as well as catch any silent failures. 

Grabbing screenshots of [console errors](https://developer.chrome.com/docs/devtools/console/log/) and [copying cURLs](https://everything.curl.dev/usingcurl/copyas) 
from network failures should be go-to tools in your QA toolbelt. You should always be reducing the time it takes for the dev to understand, and ultimately fix, a bug.

As your comfort and familiarity with debugging tools and the project increases so too will your ability to provide details.
The progress of your reports may go something like this...


Day 0:
> I pressed the 'Submit' button and the page broke.

Day 1 (after reading this amazing readme):
> I pressed the 'Submit' button and the page broke. Here's a screenshot of the console error and copy of the cURL.

Day 15:
> I pressed the 'Submit' button and the page broke. Here's a screenshot of the console error and copy of the cURL. I ran the project locally and inserted 
> some console.log()s, it looks like 'user_type_id' is undefined in the user detail page.

Day 30: 
> I pressed the 'Submit' button and the page broke. Here's a screenshot of the console error and copy of the cURL. I ran the project locally and inserted 
> some console.log()s, it looks like 'user_type_id' is undefined in the user detail page. I also looked at the API it it's expecting 'user_types_id' 
> not 'user_type_id'.

Day 60:
> I pressed the 'Submit' button and the page broke. Here's a screenshot of the console error and copy of the cURL. I ran the project locally and inserted 
> some console.log()s, it looks like 'user_type_id' is undefined in the user detail page. I also looked at the API it it's expecting 'user_types_id' 
> not 'user_type_id'. Here's a PR for a fix, check it out when you get a chance.

At any stage of your progress an integral part of communicating an issue is giving important contextual information. The bug you've found may happening universally or it may
be limited to one specific set of variables and relaying that to the devs is crucial for quick rememdy.

Context to consider:
- What user were you logged in as?
    - Is this happening with other users?
- What were you doing before it occured?
    - Does it happen if you navigate to the page directly via url? From other pages? 
- Can you reliably reproduce the issue or is it intermittent?
    - If it is intermittent, are there any common denominators among the instances?
- Is this mock data or data pulled from production?
    - Has there recently been a database schema change that historical data may be missing?
- Which variables seem to be genuinely related and which are red herrings?

Excluding rare circumstances (ie finding a showstopper bug the day of a deployment, in which case you would want to sound the alarms immediately) you will
want to take time to explore and understand a bug before reporting it. The more information you can gather (and questions you have answered) before 
reporting it to the dev, the better. Telling a dev "hey, the page is broken" and responding "...uh, hold on, let me check" when they ask a basic follow up question
is a situation we want to avoid.

Another powerful tool for verifying where and when a bug was introduced is cross referencing development environments. Is this happening in every
environment or only dev? Is it in staging? Production? You can also do this if you're ever unsure of what you're supposed to be seeing in a given
feature card by referencing Dev against Staging/Production and seeing what changes there are.

---
## Cypress Testing


If you're setting up Cypress for the first time, follow [these instructions](https://docs.cypress.io/guides/getting-started/installing-cypress).
Otherwise, Cypress will be installed during the typical `npm install` shenanigans.

Take some time to poke around the [cypress.json configuration documentation](https://docs.cypress.io/guides/references/configuration) to see what options may be applicable for this project.

//edit insert info about test database setup here

Follow [these instructions](https://docs.cypress.io/guides/guides/command-line) to setup custom aliases for running Cypress in the command line.

1.  `npm run cypress`
    - This will headlessly run the suite as a whole, top to bottom, start to finish, the whole enchilada, all ${number_of_tests} tests. This takes roughly ${minutes} minutes and
      will give you real time results as the tests are ran. In this mode, upon failure, a test will automatically [rerun up to 3 times](https://docs.cypress.io/guides/guides/test-retries#Configure-Test-Retries) before truly failing (this is to prevent false failures due to race cases, rendering issues, etc). At the end you'll get a handydandy readout of all the successful, failed,
      and skipped tests. 
2.  `npm run cypress:open`
    - This will open the [Cypress Test Runner GUI](https://docs.cypress.io/guides/core-concepts/test-runner#Overview) and allow you to run individual tests manually
      and is the view you'll spend the bulk of your Cypress time with. The GUI is very powerful tool and it would serve you well to get acquainted with it!
      The [Selector Playground](https://docs.cypress.io/guides/core-concepts/test-runner#Selector-Playground) is especially useful when writing new tests 
      (or finding new candidates for [data-cy selectors](https://docs.cypress.io/guides/references/best-practices#Selecting-Elements))!

---

## General Testing Workflow
//edit this will probably change project to project but the spirit will remain largely the same, update as needed.


1.  Feature/bug fix is finished by dev and pushed to QA

2.  Reference feature/bug fix card AC and test bullet by bullet

    1.  If something doesn’t work as desired, document it
        (provide screenshots/curls/etc and AC bullet numbers, helps expedite the process).
    2.  If all AC is met, great - but this is just the baseline. Try to break it. Think of every whacky interaction a user might put it through.
        What happens if you submit, reject, resubmit, reject, resubmit, reject, and resubmit a regulatory PM?
        Does it still work as intended? Or does it get locked up at some point?

3)  Once you’ve shone your QA light in every nook and cranny, let the dev know it’s good to go and move their card to the Ready for Staging lane.

4)  After it goes into Staging ${client_name} will have a chance to look at it.

    1.  Despite your best efforts there will be situations where they find things you didn’t.
        They may be business logic things you had no way of knowing, common user interactions you have no way of knowing,
        or something obvious you just overlooked. It may not happen today, or tomorrow, or the next day. But it will happen.
        And when it does, cradle your ego and let it know everything is okay and you’re still doing a good job,
        the Staging environment exists for them to test in. That's the whole point.

        1.  If there are changes to be made based on their findings, repeat the above steps once the update goes into QA.

5.  At this point you have multiple options based on your workload. In priority order:

    1.  Repeat this process for all cards that are in the QA lane.
    2.  Add/update tests related to cards in the current sprint's Staging/Ready for Release lanes.
    3.  Find areas of improvement for the Cypress Suite.
        1.  New tests for existing features.
        2.  Changes/additions to make existing tests more robust/less error prone.
        3.  Reformatting existing tests to be more uniform/concise (Abstracting copy/pasted blocks, updating comment format, etc).

6)  Best practice is to run the Suite as a whole in the Staging environment
    once all cards have made their way in (or if large individual features go up).

7)  //insert information about deployment schedule here

---

## Creating a Branch

1.  Check out a new branch from staging:
    1.  `git checkout staging`
    2.  `git pull origin staging`
    3.  `git checkout -b test_suite/sprint_XXX`

Cypress related branches are merged into both QA and Staging at the same time to keep things consistent across environments because
the Cypress suite is self-contained and won’t affect the rest of the app. I generally wait until all the sprint's cards
have been progressed to Ready for Release before merging my Cypress changes to ensure I include any 'gotcha's in the test.

2.  If there are merge conflicts going into Dev, merge your changes into a branch off of Dev
    1.  `git checkout Dev`
    2.  `git pull origin Dev`
    3.  `git checkout -b test_suite/sprint_XXX_dev`
    4.  `git merge test_suite/sprint_XXX`

- test_suite/sprint_XXX -> staging
- test_suite/sprint_XXX_dev -> Dev

---

## Other Thoughts That Don't Neatly Fit Into Another Category

Due to the ever evolving nature of Cypress as a project you will periodically get popups when running `npm run cypress:open`
that alert you to a new version update. Go ahead and install the new version and check out the changelog to see if any relevant
changes were made for our purposes. Staying on top of command changes/deprecations and newly added functionality will make your
life easier as time goes on.

This will update the `package-lock.json` file so don't be alarmed when you see those files pop up in your commit/PR.
99.99% of the time it will be fine to push those changes to QA and Staging simultaneously but don't hestitate to
reach out if you're unsure.

---

## Tips and Tricks


---

## Known Cypress issues as of ${date}:


---

## Resources

- [Cypress Overview](https://docs.cypress.io/guides/overview/why-cypress)
- [Cypress Best Practices](https://docs.cypress.io/guides/references/best-practices)
- [Cypress Assertions](https://docs.cypress.io/guides/references/assertions)


a Brandon QQ™ Wheatley Production