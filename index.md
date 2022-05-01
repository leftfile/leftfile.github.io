## Thinking differently about code: a new approach to design thinking

# Table of contents
1. [PART ONE: a gentle introduction](#one)
Read this if you use an object-oriented approach to designing your software on a daily basis and are wondering what this essay is about. 
 
2. [PART TWO: condensed wisdom. ](#two)
Read this if you want a simple list of rules for better design with examples.

3. [PART THREE: a more in-depth discussion](#three) Read this if you consider yourself a functional programmer and/or don't mind philosophical discussions on programming.


# PART ONE: a gentle introduction <a name="one"></a>

**So, in short, what is this about?**

As the title suggests, this is about introducing a new way to think about software design. You're likely familliar with the SOLID principles - well, we will introduce a new set of principles that are much simpler and are not based on object-oriented programming. Skip to part two if you just want to see them.

**Does that mean you're teaching us functional programming? I don't think that's simple.**

You're probably thinking of purely functional languages such as Haskell, which can indeed be difficult to master. Our guidelines draw inspiration from many of the principles of functional programming, but are (much like the SOLID principles) language and technology-agnostic. You may have heard of the advantages of a functional approach, but didn't see an easy way to start, and this is what we aim to provide. Actually, our main objective is to show that designing simple, readable, robust and testable code can be much easier than you've been led to believe.

**Okay, but why would I bother with that? What's wrong with the way we've done things so far? If it ain't broke don't fix it.**

Well, many have made convincing arguments that it is in fact broke - you might have heard of that. But you don't have to personally believe that to find these guidelines useful, you just need to be curious enough to ask yourself whether there could be a different way to write code than the one you've likely been taught. We believe that as software becomes more and more complex every year, the best way to counteract that is to make good design simpler and simpler. Also essential in tackling this ever- growing complexity are the benefits of functional programming. Yes, we have more arguments for that in fancy computer science-y terms and references to other smart(er) people who've already talked about it - check part three for that.


**Okay, I see your point. But I've been so accustomed to this type of thinking, it's hard to switch now. Maybe you can give me an example?**

Sure, let's check out this simple class.

```python

class Name:
    def __init__(self, first, last):
        self.first = first
        self.last = last

    def full_name(self):
        return self.first + " " + self.last

    def email_address(self):
        return self.first.lower() + "." + self.last.lower() + '@' + 'domain.com'


name = Name("Foo", "Bar")
print(name.email_address())

```
You've probably been taught to design code this way. You think about what functionality you will need and look for an entity that you can pronounce an object, in this case Name. You’re so used to it you probably think this is a sensible abstraction, after all, it gives your code shape, starts you off on some sort of a design path. But think about it: in real life, there is no Name object with a full_name method (unlike a Car object with a drive method directly translated from real life to code that you were promised in tutorials). You, the software engineer constructed this nonsencial object solely for the purpose of concatenating some strings of people's names. Really, all you're doing is this:

```python


def get_full_name(first, last):
    return first + " " + last

def get_email_address(first, last):
    return first.lower() + "." + laste.lower() + '@' + 'domain.com'

print(get_email_address("Foo", "Bar"))  


```
This is shorter, more readable and as we will see in part two it even abides by the principles of functional programming. So how did we get here from our initial class? If you look closely, the only thing we've done is take the data that is in the init function (first and last name) and make them parametrs to our functions - because this is the only purpose that this data actually had all along: we need it to perform these two calculations! This was the only reason for this object to exist and we can now safely omit it. So if you're struggling to reason about your code without classes (especially while refactoring) the rule of thumb is: take what were previously object properties and make them either variables or parameters. From there, it will be easier to start reasoning about functions, what they do and why. But if you are not refactoring a class and are looking to design a piece of code from scratch, here comes part two.



# PART TWO: condensed wisdom <a name="two"></a>

 

These simple rules show how you can introduce functional principles into your code without having to learn new syntax, just think about existing syntax differently. If you follow them your code won't look that much different than any other code at first glance, but you will still reap many benefits of a functional approach.

We believe programming language constructs - variables, data structures, functions, etc. - are kind of like people: how they treat you depends a lot on how you treat them. So simply improving the ways you structure your code on this lowest (unless you're a language designer) and therefore simplest level can improve the functioning of many other levels of a much larger system. This also means that you don't have to refactor your entire object-oriented codebase at once - purity is nice, but gradual optimisation is more realistic and therefore better.

We will make our points through an example of one of the most common (and boring) software engineering tasks: sending email.



1. <b>SAME values. If possible, avoid changing the value of variables. If a value changes assign it to a new variable.</b>

	**Example:**
	
	You have a list of addresses you need to send en email to.
	
	```python
	receivers = ['matt@gmail.com', 'lauren1@email.com', 'tina@domain.co']
	```
	
 Sometime later in your code you need to add a new receiver to your list. Instead of adding it to the existing list (``` receivers + 'jack123@hotmail.com````) you introduce a new variable
 
 ```python
   new_receivers = ['matt@gmail.com', 'lauren1@email.com', 'tina@domain.co', jack@123@hotmail.com]
 ```
	
	**Simple explanation:** This may seem like an unnecessary extra step in the moment, but it prevents a lot of bugs in the long run as it becomes more and more difficult to trace the changes of the value through the different places in your code.
		
	**Less simple explanation:** Immutability is a fundamental principle of functional programming. Even when a language allows mutability of data structures, developing an intuition of not using it unnecessarily has a similar advantage of robustness.


2.  <b>SMALL functions. A function is the basic building block of your program (not a class). A function is never too small, but is easily too large. It should only do one thing. </b>

	**Example:**
	
	 Your task is to write a piece of code that constructs and sends a promotional email to a mailing list. This seems simple enough, so you are tempted to put it all in one function. But as soon as the word 'and' appears in the descripton of your task, this is usually a sign you should split it into (at least) two functions.
 
	**Simple explanation:** This will keep your code readable.
		
	**Less simple explanation:** Without using classes, a programmers first instinct is usually to write code in a procedural way. This usually leads to long, unreadable and badly designed functions. Having such a rule in our mind helps to start design thinking early without classes.	

3. <b> SEPARATE actions and computations. There should be two types of functions: Those who perform actions and those who compute values. </b>


	**Example:**
	
	Ok, so we already know we're going to split our code into functions, but how exactly? We're going to find the parts of our program that communicate with the outside world (actions) and the parts that do not (computatios) and separate them. 
	
	
	Some more details on the requirements of our task: since this is a promotional email for our company, the sender (us) and the list of receivers (our mailing list) will always be the same. The subject, however, will vary, because we will have different types of promotions. In the body of the email, we also need to display today's day.
	
	At last, let's see the code:
	
	```python
	import smtplib
	from datetime import datetime
	
	sender = 'our_company@domain.com'
	
	receivers = ['matt@gmail.com', 'lauren1@email.com', 'tina@domain.co']
	
	def get_todays_day():
	    
	    return datetime.today().strftime('%A')
	
	
	
	def construct_message(subject, weekday):
	
	    template = """From: From Person <from@fromdomain.com>
	    To: To Person <to@todomain.com>
	    Subject: {subject}
	
	    Hey, happy {weekday}!
	
	    This is a test e-mail message.
	    """.format(subject=subject, weekday=weekday)
	
	    return template
	
	
	
	def send_email(subject):
	
	    message = construct_message(subject)
	
	    try:
	        smtpObj = smtplib.SMTP('localhost')
	        smtpObj.sendmail(sender=sender, receiver=receivers, message=message)         
	        print("Successfully sent email")
	    except Exception:
	        print("Error: unable to send email")
	

	
	```

	When we start thinking about our code, we realise that it actually performs two actions and one computation! How? Well, we also need to get today's date and convert it to the string of today's weekday. This might seem like it could be a computation at first glance, but it's not because it interacts with the outside world, or rather depends on it - the result will not always be the same, in fact, it will be different every day. Following the rule above, we decide to put this into a separate function, which gives us more control, makes it easier to locate bugs and we can also reuse it anytime we need the current weekday.
	
	**Simple explanation:** This distinction makes writing debugging and testing significantly easier.
	
	**Less simple explanation:** This isolates side effects, which is one of the most important features of functional programming. The easiest way to reason about this separation is to have a separate function for each.


4. <b> SIMPLE logic in functions that compute values. We can think of these functions in terms of having just three elements: parameters, return value, and logic. Logic is is simply what computes the return value from the parameters.</b>

	**Example:**
	
	Let's pretend for a second that we haven't written our function that constructs the email yet, but we know what we need from it - the final template that will be send. So we set that as a return value. We also know the subject and the weekday will vary, which means they should be parameters. For simplicity's sake let's say we are 100% sure that all of these values will always be of type string.
	
			
			def construct_message(subject, weekday):
				
			### ???
				
			    return template
			    
	 Now, to wrire this function, we only need to ask ourselves one question - how do we get the 'template' string from the 'subject' and 'weekday' strings in the simplest possible way? And again, this is our result:
	 
	 
	```python
	
		
	def construct_message(subject, weekday):
	
	    template = """From: From Person <from@fromdomain.com>
	    To: To Person <to@todomain.com>
	    Subject: {subject}
	
	    Hey, happy {weekday}!
	
	    This is a test e-mail message.
	    """.format(subject=subject, weekday=weekday)
	
	    return template
	
	
	```
	
	We have simply added to the body of the function what is necessary to compute the return value from the parameters - and nothing else.
	
		    
	
	**Simple explanation:** This ensures that every time this function is called, the exact same thing happens - no unexpected bugs.
		
 **Less simple explanation:** This encourages the functional programming principles of referential transparency and determinism.
	


1. <b> SENSIBLE abstractions. Most code duplication can be avoided with a properly parametrised function.</b>
	
	**Example:**
	
	Of course our tiny example is not a great tool to talk about abstractions, which mostly emerge later, in a larger codebase. But	when choosing a parameter for our email sending function, we still asked ourselves: will we need to peform this action several times in a slightly different way? The answer was yes, we will need to send several emails with different subjects. .
		
	```
	
	
	send_email(subject='Hello everyone!')
	
	send_email(subject='To hell with everything')
	
	send_email(subject='This example is funny')
	
	```
	
	
	Again, this is a really simple example, but even when thinking about much more complicated abstractions we are really thinking in s similar way, just on a larger scale. Even more advanced language constructs such as higher order functions can ostensibly be reasoned about in this simple way. 
	
 **Simple explanation:** The best way to avoid code repetition is to think about how to best parametrise your functions. Many other abstractions can be unnecessary and even complicate code more.
	
	
	
	**Less simple explanation:** Again, in the absence of an object-oriented framework, programmers might be tempted to throw all thought of abstractions out the window, but that's not what we want - rather, we want to make the concept of abstractions simpler and more practical to reason about. We're not trying to over- or underengineer, just...engineer.
	

# PART THREE: a more in-depth discussion <a name="three"></a>

### 1. Why do we need an alternative to SOLID principles? 

First introduced in 2000 by Robert C. Martin, the SOLID principles are still today, more than 20 years later, the de facto standard of software architecture. They introduce design patterns that are the basis of object-oriented programming, providing guidelines on how to structure your code, modules and applications. It is still widely considered good practice to follow these principles in your codebase.

On the other hand, the last couple of years saw a rise of critiques of object-oriented programming (OOP) as well. Functional programming (FP) has often been suggested as a superior alternative, so much so that it has in turn been dismissed as ‘trendy’ by some. In response it is usually pointed out that FP has been around just as long as OOP, it just never reached mainstream popularity. One of the contentions of this essay is that SOLID principles themselves might have contributed to that as well. Since these principles provide a relatively friendly and digestible way to navigate the complicated task of structuring your code, they likely had a hand in making OOP seem human and real-life friendly, rather than mathematical and unapproachable (as FP is often thought to be).

However, even with insightful critiques of OOP accumulating, most production codebases are still written in an object-oriented way. Interest in FP has indeed surged, and it is talked about more than ever, but in practice it is very rarely actually adopted as a default paradigm for your average production codebase of a for-profit piece of software. Following our previous line of reasoning, this could partly be because FP does not have its own equivalent of SOLID principles - simple guidelines that help to reason about structuring your code. So this essay essentially asks: what if we tried to invent that - what would that look like?

This essay will not focus on pointing out the pitfalls of an object-oriented approach (as many have very successfully done so already) and it definitely won’t criticise SOLID principles as such (after all, many of them can usefully be applied outside of an OOP context as well). It does however take issue with the fact  that these frameworks still have a monopoly on the way we think about software architecture. Think about it: pretty much everything has changed to an unrecognisable degree in software development in the past 20 years. Processing power increased significantly, countless new paradigm-shifting technologies appeared, software has had to learn to handle exponentially more demanding tasks. The industry tried its best to handle that by inventing or popularising concepts such as concurrency, distributed systems, micro-services and automated testing to name only a few.

The very meaning of the words ‘technology’ or ‘software’ is drastically different today than it was at the time of the inception of the SOLID principles. Does it make sense then for the prevailing philosophy of software architecture to stay exactly the same? Does it make sense that the way we think about code stays the same even when things that the code builds change, and will continue to change (that is, become more complicated) ? Adding to that the aforementioned criticisms of OOP we can conclude that it is imperative that we at least attempt to introduce an alternative to SOLID principles - a philosophy that is just as easily digestible (or easier!), but promotes a functional instead of an object-oriented approach.

### 2. Functional programming vs. functional thinking

Let us first define the basic terms we’re going to talk about moving forward. As you might have noticed we’re using the terms object-oriented (or functional) programming and object-oriented (or functional) design interchangeably. In the context of this essay both of them refer to the same thing which is probably closer to design than programming in the sense that it is more about mental models that it is about specific syntax. This means that the concepts we will introduce here will be language and technology agnostic. We will follow in the footsteps of Eric Normand who in his seminal book Grokking simplicity talks about ‘functional thinking’ rather than ‘functional programming’. Normand introduces ‘functional thinking’ as more of a general practice that is applicable to any language, technology, type of codebase or task - it is mainly about the way we think about our code.

Functional thinking is therefore a much broader concept than functional programming. Usually when we talk about functional programming we mean writing code in a functional language such as Erlang or Haskell, which by design leaves the programmer no choice but to follow functional principles - the language itself imposes them. But functional thinking can also refer to adopting functional principles in languages which are not purely functional but support a functional approach, such as Javascript or Python. Even though we’re thinking in a functional way in both cases the way we go about it will be quite different in each case.

Most people consider languages which are not purely functional to be much more beginner-friendly. Python, which has surpassed Javascript as the most popular language this year is a great example of that, because it is profoundly non-restrictive by design. A beginner doesn’t even need to think about stuff like types, declaring variables or using counter variables in for loops to do useful things, while an advanced user can quite literally tweak its internals. It’s often said that Python reads almost like pseudo-code, which is quite the opposite of how functional languages often read. But this flexibility of a programming language can also be a downside in the sense that it makes it much easier for the programmer to make design choices which prove to be detrimental in the future. Following functional principles in such a language must be a conscious choice in every given moment, a choice made by the human rather than automated by the language. It is then not really surprising that despite the benefits, many people are reluctant to take this path. It is difficult to reconcile the freedom that a language like Python gives us with with the structure and restrictions of the functional paradigm. The goal of this essay is to somehow attempt to bridge that gap and make functional thinking as accessible as possible.


### 3. Why isn’t functional programming the norm?

Reading the previous section one might ask - what’s the point of trying to popularise functional thinking if we could just popularise pure FP? Did we not just argue that purely functional languages guide the programmer towards functional thinking with their restrictions while in a language like Python, this is somewhat less ‘natural’ as it is not built in in the language itself? So why even bother doing that? A purist will likely argue that this isn’t just pointless but maybe even bad practice. Even though intuitively it’s not difficult to see why ‘purely functional’ would be better than ‘functional style’ (and it indeed is in many contexts), the goal of this essay is to address the broadest possible audience, which brings with it a necessary fundamental attitude of pragmatism over purity and practicality over beauty. There really is no such thing as purity in the software industry, only endless optimisations, which often take the form of moving away from different types of impurities.

Furthermore, if making functional languages appeal to a wider audience would be that easy, it would likely have been done already. Of course many reasons for OOP’s reign are circumstantial, some consider it to be little more than a legacy of the popularity of Java that seeped into the entirety of the industry and refuses to leave. Now, this perpetuates itself and many people simply don’t know how to begin using FP in their day to day because they don’t use it at their job. Companies, meanwhile, are reluctant to use it in their stack because they would have a smaller pool of qualified candidates to choose from, and the cycle continues. 

But it’s also not difficult to argue that there are deeper reasons for this, to wonder if maybe FP is inherently unable to appeal to a wider audience, that making a language like Haskell popular would be impossible without changing it fundamentally. OOP does have a large fundamental advantage due to its seeming proximity to the real world. This makes learning and teaching it easier as there is a smoother transition from everyday modes of thinking to, let’s call it, a programming mode of thinking (at least for most people - see next paragraph).

The following that FP has is, of course, much smaller but arguably more passionate, we could call it cult-like. It seems that for most functional programmers functional thinking is something so intuitive that it seems difficult to see how or why anyone would do it another way. It’s not only about being convinced of the benefits of a functional approach (even though those are undeniable), but also about the ease with which some programmers employ this way of thinking. So the two programming paradigms are really groups of two different types of programmers - and people. 

This leads us to believe that a purely functional revolution is unlikely to happen anytime soon. What follows from this is that the only viable way to make functional thinking more popular is to make it more like OO - only on the surface, of course. In a pure functional language that’s impossible, since we are not trying to change the language itself, only its ecosystem. But in languages that support multiple paradigms we have the freedom to do so. There we can shape functional thinking into digestible chunks, wrap it into a friendlier philosophy, attempt to guide users to better design decisions - similar to what the SOLID principles did for OO. We can think of this as populating an empty space between the two camps, bridging the gap between them, thus allowing them to learn from each other. 


© Leftfile 2022










