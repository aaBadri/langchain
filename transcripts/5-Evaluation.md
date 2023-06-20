# Evaluation

When building a complex application using an LLM, one of the 
important but sometimes tricky steps is how do you 
evaluate how well your application is doing? 
Is it meeting some accuracy criteria? 
And also, if you decide to change your implementation, 
maybe swap in a different LLM, or change the strategy 
of how you use a vector database or something else to retrieve chunks, 
or change some other parameters of your system, 
how do you know if you're making it better or worse? In 
this video, Harrison will dive into some 
frameworks on how to think about evaluating a LLM-based application, 
as well as some tools to help 
you do that. 
These applications are really chains and sequences of 
a lot of different steps. And so honestly, part of the first 
thing that you should do is just understand 
what exactly is going in and coming out of each step. 
And so some of the tools can really just be thought of 
as visualizers or debuggers in that vein. 
But it's often really useful to get a more holistic picture on a lot 
of different data points of how the model is doing. And 
one way to do that is by looking at things by eye. But 
there's also this really cool idea of using language models themselves 
and chains themselves to evaluate other 
language models, and other chains, and other 
applications. And we'll dive a bunch into that as well. 
So, lots of cool topics, and I find that with a lot of 
development shifting towards prompting-based development, developing 
applications using LLMs, this whole workflow evaluation process 
is being rethought. So, lots of exciting concepts in this video. 
Let's dive in. 
Alright, so let's get set up with evaluation. First, we 
need to have the chain or the application that we're going 
to evaluate in the first place. 
And we're going to use the document question answering chain 
from the previous lesson. 
So we're going to import everything we need. 
We're going to load the same data that we were using. 
We're going to create that index with one line. 
And then we're going to create the retrieval QA chain by 
specifying the language model, the chain type, the retriever, and 
then the verbosity that we're going to print out. 
So we've got this application, and the first thing we need to do is 
we need to really figure out what are 
some data points that we want to evaluate it on. 
And so there's a few different methods that we're going 
to cover for doing this. 
The first is the most simple, which is basically we're 
going to come up with data points that we think 
are good examples ourselves. 
And so to do that, we can just look at some of the data and come up 
with example questions and then example ground truth 
answers that we can later use to evaluate. 
So if we look at a few of the documents here, 
we can kind of get a sense of what's going on inside them. It 
looks like the first one, there's this pullover set, there's this in 
the second one, there's this jacket, it has a bunch of details about all of 
them. 
And from these details, we can create some 
example query and answer pairs. 
So the first one, we can ask a simple, 
"Does the Cozy Comfort Pullover Set have side pockets?". 
And we can see by looking above that it does, in fact, 
have some side pockets in it. 
And then for the second one, we can see that this 
jacket is from a certain collection, the DownTek collection. 
And so we can ask the question, "What collection is this jacket from?" 
 
And have the answer be, "The DownTek collection". So here we've 
created two examples. 
But this doesn't really scale that well. It 
takes a bit of time to look through each example 
and figure out what's going on. And so is there a way that 
we can automate it? 
And one of the really cool ways that 
we think we can automate it is with language models themselves. 
So we have a chain in LangChain that can do exactly that. 
So we can import the QA generation chain. 
And this will take in documents and it 
will create a question answer pair from each document. 
It'll do this using a language model itself. So 
we need to create this chain by passing in the 
Chat OpenAI language model. 
And then from there, we can create a bunch of examples. 
And so we're going to use the apply and parse method 
because this is applying an output parser to 
the result because we want to get back 
a dictionary that has the query and answer pair, not 
just a single string. 
And so now, if we look at what exactly is returned here, 
we can see a query, and we can see an answer, and let's 
check the document that this is a question and answer for, and 
we can see that it's asking what the weight of this is, we can 
see that it's taking the weight from here, and look at 
that! We just generated a bunch of question-answer pairs. We didn't 
have to write it all ourselves. Saves us a bunch of 
time, and we can do more exciting things. And so now, let's go 
ahead and add these examples into the examples that we already created. 
 
So, we got these examples now, but how exactly do we evaluate what's 
going on? 
The first thing we want to do is just run 
an example through the chain, and take a look at the output it 
produces. 
So here we pass in a query, and we get back an answer. But, 
this is a little bit limiting in terms 
of what we can see that's actually happening inside the chain. 
 
What is the actual prompt that's going into the language model? What 
are the documents that it retrieves? If this were a more 
complex chain with multiple steps in it, what 
are the intermediate results? 
It's oftentimes not enough to just look at the final answer to understand 
what is or could be going wrong in the chain. 
And to help with that, we have a fun little util in LangChain called 
"langchain.debug". 
And so if we set "langchain.debug = True", and we now rerun 
the same example as above. 
We can see that it starts printing out 
a lot more information. 
And so if we look at what exactly it's printing out, we can see that it's diving 
down first into the retrieval QA chain. 
And then it's going down into a stuff documents chain. And 
so as mentioned, we're using the stuff method. 
And now it's entering the LLM chain, where we have a few different inputs. So 
we can see the original question is right there. 
And now we're passing in this context. And 
we can see that this context is created from a bunch of 
the different documents that we've retrieved. 
And so when doing question answering, oftentimes when a wrong result is 
returned, it's not necessarily the language model itself that's messing 
up. It's actually the retrieval step that's messing up. 
 
And so taking a really close look at 
what exactly the question is, and what exactly the context is, 
can help debug what's going wrong. 
We can then step down one more level 
and see exactly what is entering the language model, 
Chat OpenAI itself. 
And so here we can see the full prompt that's 
passed in. So we've got a system message. 
We've got the description of the prompt that's used. And so 
this is the prompt that the question answering chain is using under 
the hood, which we actually haven't even looked 
at until now. 
And so we can see the prompt printing out, 
"Use the following pieces of context to answer the user's 
question. If you don't know the answer, just say 
that you don't know, don't try to make up an answer." And then we see 
a bunch of the context as inserted before, and then we see 
a human question, which is the question that we asked it. We can also see 
a lot more information about the actual return 
type. So rather than just a string, we get back a 
bunch of information like the "token_usage", so the "prompt_tokens", the 
"completion_tokens", "total_tokens", and the "model_name". 
 
And this can be really useful to track the tokens that you're 
using in your chains or calls to language models over time and 
keep track of the total number of tokens, which 
corresponds very closely to the total cost. 
And because this is a relatively simple chain, 
we can now see that the final response, "The Cozy Comfort Pullover Set, 
Stripe does have side pockets.", is getting bubbled up 
through the chains and getting returned to the user. So we've 
just walked through how to look at and debug what's going 
on with a single input to this chain. 
But what about all the examples we created? 
How are we going to evaluate those? 
Similarly to when creating them, one way to do it would be manually. 
We could run the chain over all the examples, then look at the outputs, 
and try to figure out what's going on, whether it's correct, incorrect, partially 
correct. Similar to creating the examples, that starts 
to get a little bit tedious over time. 
And so let's go back to our favorite solution. Can 
we ask a language model to do it? First, we need to create predictions 
for all the examples. Before doing that, I'm actually 
going to turn off the debug mode in order to just not 
print everything out onto the screen. 
And then I'm going to create predictions for all 
the different examples. And so I think we 
had seven examples total, and so we're going to loop through this 
chain seven times, getting a prediction for each one. 
Now that we've got these examples, we can think about evaluating them. 
So we're going to import the QA, question answering, eval chain. We 
are going to create this chain with a language model, because 
again, we're going to be using a language model to help 
do the evaluation. 
And then we're going to call evaluate on this chain. We're 
going to pass in examples and predictions, and we're going to 
get back a bunch of graded outputs. And so, in order to 
see what exactly is going on for each example, we're going to loop 
through them. 
We're going to print out the question, and 
again, this was generated by a language model. 
We're going to print out the real answer, and 
again, this was also generated by a language model when it had the whole 
document in front of it, and so it could generate 
a ground truth answer. 
We're going to print out the predicted answer, and 
this is generated by a language model when it's doing 
the QA chain, when it's doing the retrieval with the embeddings and the 
vector databases, passing that into a language model, and then trying 
to guess the predicted answer. 
And then we're also going to print out the grade, and 
again, this is also generated by a 
language model when it's asking the eval chain to grade what's going on 
and whether it's correct or incorrect. 
And so, when we loop through all these examples and print them out, 
we can see those in detail for each example. 
And looks like here it got everything correct. This is a 
relatively simple retrieval problem, so that is reassuring. 
So let's look at the first example. The question here is, "Does 
the Cozy Comfort Pullover Set have side pockets?". The 
real answer, and we created this, is "Yes". 
The predicted answer, which the language model produced was, 
"The Cozy Comfort Pullover Set, Stripe does have side pockets". 
And so we can understand that this is a correct answer. 
And actually the language model does as well, 
and it grades it correct. 
But let's think about why we actually need to use the language 
model in the first place. 
These two strings are actually nothing alike. 
They're very different. One's really short, one's really long. I don't even 
think, yes doesn't appear anywhere in this string. So if we were to 
try to do some string matching, or exact matching, or even 
some regexes here, it wouldn't know what to 
do. They're not the same thing. And that shows off the importance of 
using the language model to do evaluation here. You've got these answers, 
which are arbitrary strings. There's no single one 
truth string that is the best possible answer. There's 
many different variants. And as long as they have 
the same semantic meaning, they should be graded as 
being similar. And that's what a language model helps with, as opposed 
to just doing exact matching. 
 
This difficulty in comparing strings is 
what makes evaluation of language models so hard in 
the first place. 
We're using them for these really open-ended tasks, 
where they're asked to generate text. This hasn't really 
been done before, as models until recently 
weren't really good enough to do this. And so a lot of 
the evaluation metrics that did exist up to 
this point just aren't good enough. And we're having 
to invent new ones, and invent new heuristics for doing so. 
And the most interesting and most popular of those heuristics at the moment 
is actually using a language model to do the evaluation. This finishes 
the evaluation lesson, but one last thing I want to 
show you is the LangChain Evaluation Platform. This is a way to 
do everything that we just did in the notebook, but 
persisted and show it in a UI. And so let's check it out. Here, 
we can see that we have a session. We called it, deeplearningai. 
 
And we can see here that we've actually persisted all the 
runs that we ran in the notebook. And 
so this is a good way to track the inputs 
and outputs at a high level. 
But it's also a really good way to see what 
exactly is going on underneath. So this is the same 
information that was printed out in the notebook 
