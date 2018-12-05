---
layout: post
title: "The Perils of Class Instance Variables in Ruby"
tags:
  - Ruby
---

Recently someone at work asked about class instance variables. We had recently
been told about a catastrophic incident wherein data was being shared with the
wrong objects because a class method set an instance variable, then later used
that instance variable to do some important work.

If you're familiar with variable ownership in Ruby, you can probably see where
this is going. Since each class is an instance of `Class`, an instance
variable in a class method belongs to the class. Each time that method is
called, the exact same intsance variable is referenced.  In a multi-threaded
context, this can have some very undesirable side effects.

I'll create a contrived scenario to illustrate. Say we're a bank and we've got
customers who want their balance emailed to them each night. We assume customers
that carry no balance shouldn't get emails. We've created `BalanceNotitfication`
to handle this.

```ruby
class BalanceNotitfication
  def self.send(email, balance)
    @balance = balance.to_f

    if should_email?
      BalanceMailer.send(to: email, subject: "Your balance is #{@balance}")
    end
  end

  def should_email?
    !@balance.zero?
  end
end
```

Things are looking great: tests pass, the product team loves it, it looks great
in the staging environment. Time to ship! Then the first night it's out in
its new multi-threaded production environment calls start coming in from frantic
customers. "Someone must have stolen my identity." "Freeze my account, now!"
Something is wrong. They're receiving emails with balances from other accounts.

Here's how that could happen:

```ruby
 BalanceNotice.send("joe@joe.com", 50) |
     @balance = 50.0                   |
     should_email? => true             |
                                       | BalanceNotice.send("ann@ann.com", 9000)
                                       |     @balance = 9000.0
     BalanceMailer.send(               |
       "joe@joe.com",                  |
       "Your balance is $9000.0"       |
     )                                 |
                                       |     should_email? => true
                                       |     BalanceMailer.send(
                                       |       "ann@ann.com",
                                       |       "Your balance is $9000.0"
                                       |     )
```

While Ann is happy with this new feature, Joe is ecstatic (for now).
`BalanceNotice.send` was called with his balance as an argument, but before his
email was sent `@balance` was re-bound to Ann's balance.

Unless you anticipate this behavior, this bug might be hard to find. However, it
is pretty simple to fix. By replacing the class instance variable (`@balance`)
with a local variable (`balance`), and passing that local variable to
`should_email?` as an argument, sanity is returned.

```ruby
class BalanceNotitfication
  def self.send(email, balance)
    balance = balance.to_f

    if should_email?(balance)
      BalanceMailer.send(to: email, subject: "Your balance is #{balance}")
    end
  end

  def should_email?(balance)
    !balance.zero?
  end
end
```

Now each time `.send` is called `balance` will be local to that call. Joe will
get Joe's balance (with great disappointment) and Ann will continue to get Ann's
balance.
