# Agents
Sometimes people think of a large language model 
as a knowledge store, as if it's learned to memorize a 
lot of information, maybe off the internet, so 
when you ask it a question, it can answer the question. 
But I think a even more useful way to think of a 
large language model is sometimes as a reasoning engine, 
in which you can give it chunks of text or other sources 
of information. 
And then the large language model, LLM, will maybe use 
this background knowledge that's learned off the internet, but 
to use the new information you give it 
to help you answer questions or reason through 
content or decide even what to do next. And that's what 
LangChain's Agents framework helps you to do. 
 
Agents are probably my favorite part of LangChain. 
I think they're also one of the most powerful parts, but 
they're also one of the newer parts. So we're 
seeing a lot of stuff emerge here that's really new to 
everyone in the field. And so this should be a very exciting lesson 
as we dive into what agents are, how to create and 
how to use agents, how to equip them with different types of 
tools, like search engines that come built into LangChain, 
and then also how to create your own 
tools, so that you can let agents interact with any 
data stores, any APIs, any functions that you 
might want them to. 
So this is exciting, cutting-edge stuff, but 
already with emerging, important use cases. So with that, let's 
dive in! 
So to get started with Agents, we're going to start as we always do, by 
importing the correct environment variables. 
We're also going to need to install a few packages here. So 
we're going to use the "duckduckgo-search" engine and "wikipedia". So we're 
going to want to pip install those. I've already installed those 
in my environment, so I'm not going to run this 
line. But if you guys have not, you should uncomment that line, run 
it, and then you're good to go. 
 
We're then going to import some methods and classes 
that we need from LangChain. So we're going to import some methods 
to load tools. 
And these are things that we're going to connect the 
language model to. We're going to load a method to 
initialize the agent. We're going to load the Chat OpenAI 
wrapper, and we're going to load "AgentType". 
So "AgentType" will be used to specify what 
type of agent we want to use. There are a bunch of different 
types of agents in LangChain. We're not going to go over 
all of them right now. We'll just choose one and run with that. We're then 
going to initialize the language model that we're going to use. 
Again, we're using this as the reasoning engine that we're 
using to drive the agent. 
We'll then load the tools that we're going to use. 
So we're going to load DuckDuckGo search and Wikipedia. And 
these are built-in LangChain tools. Finally, we're going to 
initialize the agent. 
We pass it the tools, the language model, and agent type. 
 
And so here we're using "CHAT_ZERO_SHOT_REACT_DESCRIPTION". 
Not going to go over in too much detail what this means. 
The important things to note are chat. This is optimized to 
work with chat models. 
And then react. React is a prompting strategy that 
elicits better thoughts from a language model. 
We're also going to set "handle_parsing_errors=True". 
If you remember from the first lesson, we chatted a bunch 
about output parsers and how those can be used to take the 
LLM output, which is a string, and parse it into 
a specific format that we can use downstream. 
That's extremely important here. When we take the output of the LLM, which 
is text, and parse it into the specific action and the specific 
action input that the language model should take. 
Let's now use this agent. Let's ask it a question about a recent 
event that the model, when it was trained, didn't know. So let's ask 
it about the 2022 World Cup. 
The models here were trained on data up to around 2021. 
So it shouldn't know the answer to this question. And 
so it should realize that it needs to use a 
tool to look up this recent piece of information. 
So we can see here that the agent realizes that it needs 
to use DuckDuckGo search, and then looks up 
the 2022 World Cup winner. 
And so it gets back a bunch of information. We can then see 
that the agent thinks that the 2022 World 
Cup has not happened yet. 
So this is a good example of why agents are still pretty exploratory. 
You can see that there's a bunch of information here 
about the 2022 World Cup, but it doesn't quite 
realize all that has happened. And so it needs to look up more things and 
get more information. And so then based on that information, it can 
respond with the correct answer that "Argentina won 
the 2022 World Cup". 
Let's then ask it a question where it should 
recognize that it needs to use Wikipedia. 
So Wikipedia, it has a lot of information about specific 
people and specific entities from a long time ago. It doesn't 
need to be current information. 
So let's ask it about Tom M. Mitchell, an 
American computer scientist. And what book did he write? We 
can see here that it recognizes that it 
should use Wikipedia to look up the answer. So 
it searches for Tom M. Mitchell Wikipedia. 
And then does another follow-up search just to make sure that it's got 
the right answer. So it searches for "Tom M. Mitchell Machine 
Learning" and gets back more information. 
And then based on that, it's able to finally answer with "Tom M. Mitchell 
wrote the textbook, 'Machine Learning'". 
You should pause the video here and try 
putting in different inputs. 
So far, we've used tools that come defined in LangChain already, but 
a big power of agents is that you can connect it to 
your own sources of information, your own APIs, your own data. 
So here we're going to go over how you can create 
a custom tool so that you can connect 
it to whatever you want. 
Let's make a tool that's going to tell us what the 
current data is. 
First, we're going to import this tool decorator. 
 
This can be applied to any function and 
it turns it into a tool that LangChain can use. Next, 
we're going to write a function called "time", 
which takes in any text string. We're not really going to use that. And it's 
going to return today's date by calling date time. 
In addition to the name of the function, we're also going to write a 
really detailed doc string. 
That's because this is what the agent will use 
to know when it should call this tool and how it should 
call this tool. 
For example, here we say that, "The input should always 
be an empty string.". That's because we don't use it. 
If we have more stringent requirements on what 
the input should be, for example, if we have a 
function that should always take in a search query or a SQL 
statement, you'll want to make sure to mention that here. We're now 
going to create another agent. This time we're adding the 
time tool to the list of existing tools. 
And finally, let's call the agent and ask it 
what the date today is. 
It recognizes that it needs to use the time tool, 
which it specifies here. 
It has the action input as an empty string. This is great. 
This is what we told it to do. 
And then it returns with an observation. And then finally, 
the language model takes that observation 
and responds to the user, "Today's date is 2023-05-21". 
You should pause the video here and try 
putting in different inputs. This wraps up the lesson on agents. 
This is one of the newer and more exciting 
and more experimental pieces of LangChain. 
So I hope you enjoy using it. Hopefully it showed you how you 
can use a language model as a reasoning engine 
to take different actions and connect 
to other functions and data sources. 
