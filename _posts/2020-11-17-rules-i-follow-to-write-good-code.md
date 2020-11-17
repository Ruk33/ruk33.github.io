---
layout:     post
title:      "Rules I follow to write good code"
date:       2020-11-17 12:58:51 -0300
comments:   true
---

What is good code? To me, good code is code that's simple; easy to understand, follow 
and change if required. And so, how can we write "good code"? Let's see some of 
the basics.

## Names

Naming is one of the most important aspects of good code and it's hard. Let's 
see an example:

```c
int valid(int n)
{
    int x = 18;
    return n > x;
}
```

Ok, so we got a function that by just looking at the name it doesn't tell us 
much, and this to me is a red flag. A function should be able to tell me 
exactly what it does. I do want to want to empathize on "what", not "how". As 
an example, if I got a function `walk`, I don't wanna know the details, I don't 
wanna know the inner details you do, I'm just interested in the action itself.

Going back to the previous example, let's see how can we improve it.

```c
int can_drive(int age)
{
    int min_legal_age = 18;
    return age > min_legal_age;
}
```

Perfect, short and sweet. I can tell exactly what this function is for without 
even having to look at the function's body. And if I do, if I do check into the 
inner details it's quite easy to understand the criteria.

## Comments

I used to believe on "good code doesn't need comments; the code itself is 
the documentation". And, well as you probably guest, it's not how the world works.
Comments are a must, but you have to know what type of comments you need.

```c
// defines variable life and assigns the value 42
int life_meaning = 42;
```

This comment, doesn't serve any purpose at all. But what about the next one?

```c
// The value 42 was calculated to be
// the meaning of life. Read more on 
// www.google.com/meaning/of/life
int life_meaning = 42;
```

## Short functions and single responsibility?

Short functions are nice when they improve readability. But, single 
responsibility is a must for good code. It's what allows you to build 
complex systems using understandable blocks of code, almost like if you 
were writing a recipe or a list of actions.

```c
void do_fry_egg(int how_many)
{
    int gas_off = 0;
    int gas_on = 1;
    int five_minutes = 300;

    turn_gas(gas_on);
    place_pan_on_gas();
    add_oil();
    break_eggs_in_pan(how_many);
    wait_for(five_minutes);
    remove_pan_from_gas();
    serve_eggs();
    turn_gas(gas_off);
}
```

Notice how each function seems to do one thing and only one thing. And that 
allows us to build the entire function using blocks; predictable and understandable 
blocks without surprises in the middle. What I mean by this is, imagine if, 
`turn_gas(on)` not only turns the gas but also places a pan on it. That's the kind 
of surprise I'm referring to. Don't think I'm crazy, these types of scenarios are quite 
common in the real world.

## Using parameters correctly

Usually, functions need external values and we tend to send those using parameters. 
But, have you thought on what's the minimum you need for a function to 
do it's job? because that's exactly what you should be sending and nothing more.

```c
int transfer_money(struct User* from, struct User* to, int amount)
{
    if (!has_enough_money(from->bank_account, amount)) {
        return 0;
    }

    transfer(from->bank_account, to->bank_account, amount);
}
```

Notice how in this function we are only using the property `bank_account` from 
the users, so, why are we passing or requesting the entire `User` struct? wouldn't 
it be better to just send the bank account numbers?

```c
int transfer_money(int bank_account_from, int bank_account_to, int amount)
{
    if (!has_enough_money(bank_account_from, amount)) {
        return 0;
    }

    transfer(bank_account_from, bank_account_to, amount);
}
```

There. Just think about the minimum you need for the function to do its job. 
Don't over-analyze it and don't fall for traps such as, "well, what if tomorrow 
I need to log what's user sends the transaction?" because that's a rabbit hole. 
There will always be a "what if". If you need to pass too much data, it may mean 
you are doing too much in a single function.

Now, just for fun, let's go to the previous example, what if I do really need to log 
transfers. The client sent a new requirement and now, we need to log it. Well, 
block by block you can build complex systems :)

```c
int transfer_money(int bank_account_from, int bank_account_to, int amount)
{
    ...
}

int log_transfer_money(char *from, char *to, int amount)
{
    log("Customer %s has transferred to %s the amount of %d", from, to, amount);
}

int transfer_money_logging_operation(struct User *from, struct User *to, int amount)
{
    int transfer_succeed = transfer_money(from->bank_account, to->bank_account, amount);

    if (!transfer_succeed) {
        return 0;
    }

    log_transfer_money(from->name, to->name, amount);

    return 1;
}
```

This seems like a good chunk of code to practice our documentation skills.

```c
/**
 * Transfer money from customer to another customer.
 * It will return 1 if the operation succeed.
 * If the operation fails (maybe the customer doesn't have sufficient funds), 0 will be returned
 * This function shouldn't be used directly since it won't leave any 
 * logs of the operation in the system, instead, use @transfer_money_logging_operation
 */
int transfer_money(int bank_account_from, int bank_account_to, int amount);

/**
 * Log money transfer operation.
 * In this application, it is required that every single money transaction 
 * must be logged so make good use of this function
 */
int log_transfer_money(char *from, char *to, int amount);

/**
 * Helper function to transfer money and log the operation if it succeed.
 * It will return 1 if the operation succeed, 0 otherwise.
 * Check @transfer_money for possible failure cases
 */
int transfer_money_logging_operation(struct User *from, struct User *to, int amount);
```

## Using data structures

You need to know your data structures to be able to choose the right one for 
the proper job. If you do so, you will find out your job gets easier and, it 
may even perform better than expected. Choose the wrong data structure and 
it will quite likely be hell.

Thinking in data structures can also help you to solve problems. When you are 
stuck in a problem think, what data structure could be good here? it may 
give you an insight into the problem.

## That's it for today

In the next article I would love to touch on some design patterns but time will 
tell. I hope some of these basics tips were useful to you!