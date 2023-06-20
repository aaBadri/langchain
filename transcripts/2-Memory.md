# Memory
When you interact with these models, naturally they don't 
remember what you say before or any of 
the previous conversation, which is an issue when you're building 
some applications like Chatbot and you 
want to have a conversation with them. 
And so, in this section we'll cover memory, which is basically how 
do you remember previous parts of the conversation and feed that into 
the language model so that they can have this 
conversational flow as you're interacting with them. 
So, LangChain offers multiple sophisticated options of 
managing these memories. Let's jump in and take a look. 
So, let me start off by importing my 
OpenAI API key, 
and then let me import a few tools that I'll need. 
Let's use as the motivating example for memory, using 
LangChain to manage a chat or a chatbot conversation. 
So, to do that, I'm going to set the LLM 
as a chat interface of 
OpenAI with temperature equals 0, 
and I'm going to use 
the memory as a ConversationBufferMemory, 
and you'll see later what this means. 
And I'm going to build a conversation chain. Again, 
later in this short course, Harrison will dive much more 
deeply into what exactly is a chain in LangChain. So, don't 
worry too much about the details of the syntax for now, 
but this builds an LLM. 
And if I start to have a conversation, conversation.predict, give 
the input, 
Hi, my name is Andrew. 
Let's see what it says. 
Hello, Andrew, it's nice to meet you. Right? And 
so on. And then, 
let's say I ask it, 
what is 1 plus 1? 
1 plus 1 is 2, and then 
ask it again, you know, what's my name? 
Your name is Andrew, as you mentioned earlier. Hmm, 
there's a little bit of trace of sarcasm there, 
not sure. 
And so if you want, you can change this 
verbose variable to true, 
to see what LangChain is actually doing. 
When you run predict, Hi my name is Andrew, 
this is the prompt that LangChain is generating. 
It says, 
the following is a friendly conversation between a 
human and an AI. AI is talkative, and so on. So this is a 
prompt that LangChain has generated 
to have the system have a hopeful and friendly conversation, 
and it has to save the conversation, and here's the response. 
And when you 
execute this on the 
second and third parts of the conversations, 
it keeps the prompt as follows. And notice that by the time I'm 
uttering, what is my name? This is the third turn, that's my 
third input. 
It has stored the current conversation 
as follows. Hi, my name is Andrew, what is 1 plus 1, 
and so on. And so this memory or this history of 
the conversation gets longer and longer. In fact, up on top, 
I have used the memory variable to store the memory. 
So if I were to print memory.buffer, 
it has stored 
the conversation so far. 
You can also print this out, memory.loadMemoryVariables. The 
curly braces here is actually an empty dictionary. There's 
some more advanced features that you can use with 
a more sophisticated input, but we won't talk about 
them in this short course. So don't worry about why there's an empty curly braces 
here. 
But this is what LangChain has remembered in the memory of the 
conversation so far. It's just everything that the AI or 
that the human has said. 
I encourage you to pause the video and run the code. 
So, the way that LangChain is storing 
the conversation is with this 
ConversationBufferMemory. 
If I were to use the ConversationBufferMemory 
to specify a couple of 
inputs and outputs, this is how you add 
new things to the memory if you wish to do so explicitly. 
Memory.saveContext says, hi, what's up? 
I know this is not the most exciting conversation, 
but I wanted to have a short example. 
And with that, this what the status of the memory is. 
And once again, let me actually show the 
memory variables. Now, if you want to add additional 
data to the memory, you can keep on saving additional context. So, 
conversation goes on, not much, just hanging, cool. 
And if you print out the memory, you know, there's 
now more stuff in it. 
So when you use a large language model 
for a chat conversation, the large language model 
itself is actually stateless. The language model itself does not 
remember the conversation you've had so far. And each transaction, each 
call to the API endpoint is independent. 
And chatbots appear to have memory 
only because there's 
usually 
rapid code that provides the full conversation that's 
been had so far as context to the LLM. 
And so, the memory can store explicitly 
the terms or the utterances so far. Hi, my name is Andrew. Hello, it's 
just nice to meet you and so on. 
And this memory storage is used as input 
or additional context to the LLM 
so that they can generate an output as if it's 
just having the next 
conversational turn, 
knowing what's been said before. 
And 
as the conversation becomes long, the amounts of 
memory needed becomes really, really long and does the 
cost of sending a lot of tokens to the LLM, 
which usually charges based on the number of tokens it needs to 
process, will also become more expensive. 
So LangChain provides several 
convenient kinds of memory to store and accumulate the conversation. 
So far, we've been looking at the ConversationBufferMemory. 
Let's look at a different type of memory. 
I'm going to import the conversation buffer window 
memory that only keeps a window of memory. If I set memory 
to conversational buffer window memory with k equals one, 
the variable k equals one specifies that I wanted to remember just 
one conversational exchange. That is one utterance from 
me and one utterance from 
chatbot. So, 
now if I were to have it save context, hi, what's up, not much, 
just hanging. 
If I were to 
look at memory.load variables, it only remembers the most recent utterance. 
Notice it's dropped. Hi, what's up? It's just saying 
human says not much, just hanging, and the AI says cool. So that's 
because k was equal to one. 
So this is a nice feature because it 
lets you keep track of just the most 
recent few conversational terms. In practice, you probably won't 
use this with k equals one. You use this with k set 
to a larger number. 
But still, this prevents the memory from growing 
without limit as the conversation goes longer. 
And so if I were to 
rerun the conversation that we had just now, 
we'll say, hi, my name is Andrew. 
What is 1 plus 1? 
And now I ask it, 
what is my name? 
Because k equals 1, it only remembers the 
last exchange versus what is 1 plus 1? 
The answer is 1 plus 1 equals 2, and it's forgotten this early exchange 
which is now, now says, sorry, don't have 
access to that information. 
One thing I hope you will do is pause the video, 
change this to true in the code on the left, 
and rerun this conversation with verbose equals true, 
and then you will see the prompts actually 
used to generate this, and hopefully you'll 
see that the memory, 
when you're calling the LLM on what is my name, 
that the memory has dropped this exchange where 
it learned what is my name, which is why it now says it doesn't 
know what is my name. 
With the conversational token 
buffer memory, 
the memory will limit the 
number of tokens saved. And because a lot of 
LLM pricing is based on 
tokens, this maps more directly 
to the cost of the LLM calls. So, 
if I were to say the max token limit is equal to 50, 
and actually let me inject a few comments. So let's 
say the conversation is, AI is what? Amazing. 
Backpropagation is what? Beautiful. Chatbot is what? Charming. 
I use ABC as the first letter of all of these conversational terms. 
We can keep track of what was said when. 
If I run this with a high token limit, 
it has almost the whole conversation. 
If I increase the token limit to 
100, 
it now has the whole conversation, sign of AI is what? 
If I decrease it, 
Then, you know, it chops off the earlier parts 
of this conversation to retain the number of tokens 
corresponding to the most recent exchanges 
but subject to not exceeding the token limit. 
And in case you're wondering why we needed to specify an LLM, it's because 
different LLMs use different ways of counting tokens, so 
this tells it to use the 
way of counting tokens that the chatOpenAI 
LLM uses. 
I encourage you to pause the video and run the code, 
and also try modifying the prompt to see if you 
can get a different output. 
Finally, there's one last type of memory I want to illustrate here, which 
is the conversation 
summary 
buffer 
memory. 
And the idea is, instead of limiting the memory to a fixed number 
of tokens based on the most recent utterances or a fixed number 
of conversational exchanges, 
let's use an LLM 
to write a summary of the conversation so far, 
and let that 
be the memory. 
So here's an example where I'm going to create a long 
string with someone's schedule, you know, there's meeting at 8am 
with your product team, you need your PowerPoint presentation, and so on and 
so on. So there's a 
long string saying what's your schedule, you know, maybe 
ending with a noon lunch at the Italian 
restaurant with a customer, bring your laptop, show 
the latest LLM demo. 
And so, let me use a 
conversation summary buffer memory 
with a max token limit of 400 in this case, 
pretty high token limit, 
and I'm going to 
insert in 
a few conversational terms in which we start with hello, what's 
up, no one's just hanging, 
cool. 
And then 
what is on the schedule today, and the 
response is, you know, that long schedule. 
So this 
memory 
now has quite a lot of text in it. In fact, let's take a look 
at the memory variables. 
It contains that entire 
piece of text, 
because 400 tokens was sufficient 
to store 
all this text. 
But now, 
if I were to reduce the max token limit, say to 100 tokens, 
remember, this stores the entire conversational history. 
If I reduce the number of tokens 
to 100, 
then, 
the conversation summary buffer memory has actually used an 
LLM, the OpenAI endpoint in this case, 
because that's where we set the LLM to, to 
actually generate a summary 
of the conversation so far. So the summary is, human-AI 
engage in small talk before the start of the schedule, and 
informs human in the morning meeting, blah, blah, blah, 
lunch meeting with customer interested in AI and latest 
AI developments. 
And so, 
if we were to have a conversation, 
using this LLM, 
let me create a conversation chain, 
same as before. 
And let's say that we were to ask, 
you know, input what would be a good demo to show. 
I said verbose equals true, so here's the prompt. 
The LLM thinks the current conversation 
has had 
this discussion so far, 
because that's the summary of the conversation. 
And just one note, if you're familiar with the OpenAI 
chat API endpoint, there is a specific system message. 
In this example, 
this is not using the official OpenAI system message, 
it's just including it as part of the prompt here, 
but it nonetheless works pretty well. And given this prompt, you know, 
the LLM outputs basic constituent AI developments, 
so it's just showcasing our latest NLP capabilities. Okay, that's 
cool. 
Well, it's, you know, making some suggestions to the cool demos, 
and makes you think if I was meeting a customer, I 
would say, 
boy, if only there were open source framework available to 
help me build cool NLP applications using LLMs. 
Good things are launching. 
And the interesting thing is, if you now look at 
what has happened to the memory, 
so, notice that here, it has incorporated the 
most recent AI system output, whereas my utterance asking it, 
what would be a good demo to show, has been incorporated 
into the system message, you know, the overall summary of 
the conversation so far. With the conversation 
summary buffer memory, what it tries to do is keep the 
explicit storage of the messages up to the 
number of tokens we have specified as a limit. So, 
you know, this part, the explicit storage, we're 
trying to cap at 100 tokens, because that's what we're asked for. 
 
And then anything beyond that, 
it will use CLM to generate a summary, which is what is seen up here. 
And even though I've illustrated these different memories 
using a chat as a running example, 
these memories are useful for other applications too, 
where you might keep on getting new snippets of text, 
or keep on getting new information, 
such as if your system repeatedly goes online 
to search for facts, but you want to keep the total memory 
used to store this growing list of facts as, you know, 
capped and not growing arbitrarily long. 
I encourage you to pause the video and run the code. 
In this video, you saw a few types of memory 
including buffer memories that limits based on number of 
conversation exchanges or tokens 
or a memory that can summarize tokens above a certain 
limit. 
LangChain actually supports additional memory types as well. 
One of the most powerful is vector data memory, 
if you're familiar with word embeddings and text embeddings, the 
vector database actually stores such embeddings. 
If you don't know what that means, don't worry about it. Harrison will 
explain it later. 
And it can then retrieve the most relevant 
blocks of text using this type of a vector database for its memory. 
And LangChain also supports entity memories, 
which is applicable when you wanted to remember 
details about specific people, specific other entities, 
such as if you talk about 
a specific friend, you can have LangChain 
remember facts about that friend, which would be 
an entity in an explicit way. When you're implementing applications 
using LangChain, you can also use multiple types 
of memories such as 
using one of the types of conversation memory that you saw in 
this video plus additionally entity memory 
to recall individuals. So this way you can remember maybe a summary 
of the conversation plus an explicit way of storing 
important facts about important people in 
the conversation. 
And of course, in addition to using these memory types, 
it's also not uncommon for developers to 
store the entire conversation in the conventional database. Some 
sort of key value store or SQL database. So you could refer back 
to the whole conversation for auditing or for 
improving the system further. 
And so, that's memory types. I hope you find this useful 
building your own applications. 
And now let's go on to the next video to learn about the 
key building block of LangChain, namely the chain.
