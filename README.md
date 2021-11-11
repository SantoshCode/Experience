# Learning from failure

## Incident #1

### **Always think logically about error before searching for a solution.**

***CSP (Content-Security-Policy) errors in my react app***

I was working in my react application in Stripe module. My task was to implement Stripe payment method in that application. I did every thing and it was working fine in my local development environment but after I deployed it to server. `Card Number Input` field was not showing up. And then I thought "Oh, this might have occured because I missed something while pusing code to server" which is a fair thing to say. And I check my logs everything was right and correctly committed. So I decided to look into browser console. It said `Failed to load Stripe.js`. So then I check a function named `loadStripe` which was a fuction provided by stripe to load `stripe.js` script. But, infact stripe built this function in a way that `Failed to load Stripe.js` might not happen because it was a Promise based function and corresponding Element where we were supposed to pass returing value of `loadStripe` accpets both Promise as well as Sync values. So I thought there is nothing wrong with `loadStripe`. So **I stopped thinking** and started copy pasting `Failed to load Stripe.js`. I found hundereds of related and un-related solutions, tried every one of them no thing worked. And I un-knowlingly found out that `CSP` had a thing to do with my issue. `CSP` was blocking `Stripe.js` to load in my react website. So **I didn't logically think again** and started searching for my solution to `CSP` problem and again I came across many solutions no thing worked. I tried adding individual scripts required by my application to my `index.html` inside ` <meta http-equiv="Content-Security-Policy"  /> ` tag but it didn't worked. ***Here I didn't think about the fact that I just added every scripts required by my application and even after that it shows `CSP` has blocked `stripe.js`***. So I keep messing with my `CSP`s. Nothing worked. So Next day I sat down and started think about error because I was tried of searching.

So Below were my thoughts.

1. I can be 100% sure that error was because of `CSP` blocking my `stripe.js` file from hydrating into my react application.

2. So to solve above issue I tried to change `CSP` accordingly in my `index.html` file. But surprisingly `CSP` error was still there.

3. Now I can be sure that there is no thing worng with my react application. I should probably check deployment process.

4. And then there I found `nginx` was blocking every scripts. `nginx.conf` had a default `add_header Content-Security-Policy`.

5. So everytime even if I correctly managed `CSP` in my `index.html` `nginx` was doing opposite of it.

So at last I patched everything in my `nginx.conf` file and everything worked smoothly 🙂.
