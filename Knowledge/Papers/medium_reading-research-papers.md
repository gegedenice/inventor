---
title: "Reading Research Papers in the Age of LLMs"
description: "Memory Caching gives RNNs a growing memory, offering a middle ground between efficient recurrence and expensive full attention."
url: https://pandeyparul.medium.com/reading-research-papers-in-the-age-of-llms-63b127d2ad38
---

## Introduction

Recently there was an interesting conversation on X about how it is becoming difficult to keep up with the new research papers because of their ever increasing quantity. 
Honestly, it’s a general consensus that it’s impossible to keep up with all the research that is currently happening in the AI space and if we are not able to keep up, 
we are then missing out on a lot of important information. The main crux of the conversation was: who are we writing for if humans can’t read it, 
and if LLMs are the ones actually reading the papers, what is the ideal format for them?

This had me thinking and it reminded me of an article I wrote back in 2021 on the tools I used to read research papers effectively and how I read papers back then. 
That was the pre-ChatGPT era, and I realised how much paper reading has changed for me, since then.
So I’m sharing how I read research papers today, both manually and with AI assistance. My hope is that if you are also getting overwhelmed by the pace, some of these ideas or tools might help you build a flow that works for you. 
I don’t really have the answer to what an ideal paper format should look like in the LLM era, but I can at least share what has worked for me so far.

## The Manual way — three-pass method style

There was a time when all the reading was manual and we used to either print papers and read them or do so via an e-reader. 
During that time I was introduced to a paper by S. Keshav on the three-pass method. I’m sure you must have also come across it. 
It’s a simple yet elegant way of reading a paper by breaking the process into three steps.

As shown in the figure above, the three-pass method lets you control how deep you want to go based on your purpose and the time you have. Here is what each pass involves:

1. The first pass gives a quick bird’s-eye view. You scan the paper to understand its main idea and check if it’s relevant. The goal is to answer the 5 Cs at the end of your reading : the category of the paper, its contribution, whether the assumptions are correct, the clarity of the writing and the context of the work. This shouldn’t take more than 5–10 minutes.
2. The second pass can take up to an hour and goes a bit deeper. You can make notes and comments, but skip the proofs for now. You primarily need to focus on the figures and graphs and try to see how the ideas connect.
3. The third and final pass takes time. By now you know the paper is relevant, so this is the stage where you read it carefully. You should be able to trace the full argument, understand the steps and mentally recreate the work. This is also where you question the assumptions and check if the ideas hold up.

Even today, as much as possible, I try to begin with the three-pass method. I have found it useful not just for research papers but also for long technical blogs and articles.

## The Chatbot summary way — vanilla style

Today, it’s easy to drop a paper into an LLM-powered chatbot and ask for a quick summary. Nothing wrong in that, but I feel most AI summaries are quick and at times flatten the ideas.

But I have found few prompts that work better than the vanilla “summarise this paper” input. For instance, you can ask the LLM to output the summary in a three-pass style, the same method we discussed in the previous section which gives a much better output.

```
Give me a three-pass style look at this paper.
Pass 1: a quick skim of what the paper is about.
Pass 2: the main ideas and why they matter.
Pass 3: the deeper details I should pay attention to.”
```

Another prompt that works well is a simple problem–idea–evidence style:

```
Tell me:
• what problem the paper tries to solve
• the main idea they use
• how they support it
• what the results mean.
````
Or if I want to check how a paper compares with past work, I can ask:
```
Give me the main idea of the paper and also point out its limits or things to be careful about
```
You can always continue the chat and ask for more details if the first answer feels light. But the main issue for me is still the same: you need to switch between tabs to look at the paper and then compare the explanation and both sit in different places. For me, that constant back-and-forth becomes a point of friction. There has to be a better way which keeps both the source and AI assistance on the same canvas and this takes us to the next part.

## The specialised tools way — UI matters

So I set out to explore tools that provide LLM-assistance yet offer a better UI and a smoother reading experience. Here are three that I’ve used personally. This is not an exhaustive list, just the ones that, in my experience, work well without replacing the core reading experience. I’ll also point out out the features that I like the most for every tool.

1. alphaXiv
AlphaXiv is the tool I’ve been using for a long time because it has many useful things built right into the platform. 
It’s easy to reach a paper here, either through their feed or by taking any arXiv link and replacing arxiv with alphaxiv. 
You get a clean interface and a bunch of AI-assisted tools that sit right on top of the paper. 
There is a familiar chat window but other than that you can highlight any part of the paper and ask a question right there. 
You can also pull in context from other papers using the @ feature. 
If you want to go deeper, it shows related papers, the GitHub code, how others cite the work and small literature notes around the topic, as well. 
There is an AI audio lecture feature too, but I don’t use it often.
My favourite part is the blog-style mode. It gives me a simple, readable version of the paper that helps me decide if I should do a full deep read or not. 
It keeps the figures and structure in place, almost like how I would turn a paper into a blog.
How to Try: Replace arxiv with alphaxiv in any arXiv link, or open it directly from their site at alphaxiv.org.

2. Papiers
How do you discover new papers? For me it’s through a few newsletters, but most of the time it’s from some prominent X accounts. However, the problem is that there are many such accounts and so there is a lot of noise and signal has become harder to follow. Papiers aggregates conversations about a paper and other papers related to it into one place, making the discovery part of the reading flow itself.
Papiers is a fairly new tool but already has some great features. 
For instance, in addition to getting conversations about the paper, you can get a Wiki-style view in two formats — technical and accessible so you can choose the format based on your comfort level with the topic. 
There is also a Lineage view that shows the paper’s parents and children, so you can see what shaped the work and what came after it. And there is also a mind map feature (think NotebookLM) that’s pretty neat.
I wanted to point out here that the tool did give me paper not found error for some papers, or the X feed was missing for a few. It did work for the prominent papers though. I looked around and found in a X thread that papers currently get indexed on demand, so I guess that explains it. But it’s a new tool and I really like the offerings, so I’m sure this part will improve over time.
How to Try : Replace arxiv with papiers in any arXiv link, or open it directly from their site at papiers.

3. Lumi
Lumi is an open-source tool from the People + AI Research group ay Google and as with a lot of their work, it comes with a stunning and thoughtful UI. Lumi highlights the key parts of the paper and places short summaries in the side margin, so you always get to read the original paper along with AI generated sumamry. You can also click on any reference and it takes you straight to the exact sentence in the paper. The standout feature of Lumi is that it not only explains the text but you can also select an image and ask Lumi to explain it as well.
The only downside is that it currently works for arXiv papers under a Creative Commons license, but I’d love to see it expand to cover all of arXiv and maybe even allow uploading PDFs of other papers.
How to Try : go to https://lumi.withgoogle.com/ and then import an arxiv paper under a Creative Commons license, into the interface

4. Papers With Code by HuggingFace
This is a revival of the much-loved paparswithcode website, which was previously maintained by a team at Meta. After updates slowed down and the site gradually became outdated, Hugging Face gave it a fresh lease of life this year.
It brings together a large amount of information about each paper, including code repositories, Hugging Face models and datasets, project pages, related methods, benchmarks and evaluation results. You can also submit your own paper or research release, including work published outside arXiv.
The homepage makes it easy to explore research through trending, newest and most-cited papers. 
You can filter results by day, week, month or all time, and browse papers by domain. 
Each paper shows useful signals such as GitHub activity, linked Hugging Face models, task tags, method tags and state-of-the-art results, all one page.
Some of my favorite features on this site are:
Research context: Some papers show the predecessor work they build on, while method pages help you find other papers that introduced, used, or extended the same approach.
Benchmarks and evaluations: SOTA badges highlight papers that rank among the top three on a benchmark. You can also see multiple metrics and third-party evaluations added after publication.
Conference collections: You can browse papers from major conferences in one place and explore them by topic, task, or method.
Method pages: You can open a method, read a short explanation, and explore the papers that introduced, used, or extended it. Some papers also link to multiple implementations, including official repositories, community reproductions, and library integrations.
How to Try : Open the site at paperswithcode.co and search for a paper, task, method or benchmark.

## Other tools worth the mention
While I mostly use the above mentioned tools, there are a few others that I’ve definitely crossed paths with, and I’d encourage you to try them out if they fit your flow like: They didn’t become my main choices, but they do have some good ideas and might work well for you depending on your reading style.

1. OpenRead is a great option for reading papers as well as doing literature survey. 
It has some great add-ons like comparing papers, paper graphs to show connected papers and a paper espresso feature that gives a concise one pager summary of the paper.
Something to note here is that OpenRead is a paid tool but does come with a freemium version.

2. SciSpace is a very versatile tool and in addition to being able to chat with a paper, you can do semantic literature reviews, go deep into research, write papers and even create visualisations for your work. There are many other things it offers, which you can explore in their suite. Like OpenRead, it is also a paid tool with limited features available in the free tier.
3. Daily Papers by HuggingFace is great option if you wish to see trending papers to see trending papers. 
Another nice touch about his is you can immediately see the models, datasets and spaces on HuggingFace citing a particular paper (if they exist) and also chat with the authors.

## Conclusion

Most of the reading that I do is part of the literature review for my blog, and it’s a mix of the three strategies that I mentioned above. I still like going through papers manually, but when I want to go further, see connected papers or understand something in more detail, the three tools I mentioned help me a lot. I’m aware that there are many more AI-assisted tools for reading papers, but just like the phrase too many cooks spoil the broth, I like to stick to a few and not jump between favourites unless there is a truly standout feature.
