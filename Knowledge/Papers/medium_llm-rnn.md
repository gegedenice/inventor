---
title: "Google Published a Paper That Might End the Transformer-Only LLM Era"
description: "Memory Caching gives RNNs a growing memory, offering a middle ground between efficient recurrence and expensive full attention."
url: https://medium.com/gitconnected/google-published-a-paper-that-might-end-the-transformer-only-llm-era-09f0acfca402
---

For almost a decade, the Transformer has been the default architecture behind most major progress in large language models. Its advantage comes from more than scale.

At the core of the Transformer is attention, which gives every token access to a growing memory of previous tokens. This is powerful for long-context understanding and retrieval, but it also creates a serious cost problem: memory and compute grow quickly as the context becomes longer.

Google’s paper, “Memory Caching: RNNs with Growing Memory
View on
alphaXiv
,” revisits an old architectural question from a new angle. Instead of asking whether recurrent models can replace Transformers directly, the paper asks whether recurrent models can be given a better memory system.

Traditional RNN-style models are efficient because they compress the past into a fixed-size state, but that same fixed memory becomes a bottleneck when the model needs to recall precise information from long contexts.

Memory Caching proposes a middle ground. The model still processes sequences recurrently, but it saves compressed memory checkpoints at segment boundaries.

Later tokens can retrieve from the current online memory and from older cached memories. This allows the effective memory capacity of recurrent models to grow with sequence length without paying the full cost of Transformer-style token-level attention.

In this article, we walk through the history behind this problem, why memory became the central advantage of Transformers, how Memory Caching works, and why its variants — Residual Memory, Gated Residual Memory, Memory Soup, and Sparse Selective Caching — matter.

We also cover the paper’s experiments across language modeling, Needle-in-a-Haystack retrieval, in-context retrieval, LongBench, and MQAR. The main takeaway is not that Transformers are dead. The more careful conclusion is that full attention is no longer the only credible path to growing memory.

Press enter or click to view image in full size

Table of Contents:
The Memory Problem Behind the Transformer Era
A Short History of Sequence Model Memory
The Core Bottleneck: Fixed Memory vs Growing Memory
What Memory Caching Actually Does
Four Ways to Use Cached Memories
Segmentation: The Hidden Design Choice
Linear Memory vs Deep Memory: What Is Actually Being Cached?
Applying Memory Caching to Real Architectures
Experiments: What the Paper Tests and What It Finds
Ablations and Efficiency: What Actually Matters
What This Paper Is Really Saying
I’m hosting a live workshop on Hands-On Hermes Agent: Persistent Agents, Memory, Skills, and Real Workflows.

In 120 minutes, you will learn how Hermes Agent works, set up your first persistent agent, and build practical workflows with memory, skills, messaging, and automation.

Book Your Seat

Press enter or click to view image in full size

1. The Memory Problem Behind the Transformer Era
Google researchers have published a new paper that might challenge one of the strongest assumptions in modern AI: that the future of large language models must continue to be built around Transformers.

The paper is titled “Memory Caching: RNNs with Growing Memory
View on
alphaXiv
.” The title sounds modest, but the idea behind it is important. For the last several years, the Transformer has been the default architecture behind most major progress in language modeling.

This is not only because Transformers scale well, but also because their attention mechanism gives them a powerful form of memory. Each token can look back at previous tokens and retrieve information from the context directly.

That ability is one of the main reasons Transformers became so effective. When the model needs to answer a question, resolve a reference, copy a value, or use information from earlier in the prompt, attention gives it a direct path to the relevant part of the sequence. The model does not need to compress everything into a single hidden state before using it later. Instead, the context itself becomes a growing memory.

But this is also where the cost starts.

In a standard Transformer, attention compares tokens against other tokens. As the sequence becomes longer, the number of token-to-token interactions grows quickly. During inference, the model also keeps a KV cache so it can reuse previous key and value representations. This is useful, but it means that long-context Transformers do not only require more computation; they also require more memory at serving time.

This is the basic trade-off that defines much of modern sequence modeling:

Transformers remember well because their memory grows with the sequence.
Recurrent models are efficient because their memory does not.
A recurrent neural network, or any modern architecture that behaves recurrently, processes the sequence step by step and keeps a hidden state. That hidden state acts as a compressed representation of the past. This makes recurrent models attractive for long sequences because the model does not need to store every previous token. The memory remains fixed-size, and the computation can scale more efficiently.

The weakness is that fixed-size memory has a natural bottleneck. As the sequence grows, more and more information must be compressed into the same state. For short contexts or tasks that do not require precise recall, this can work well. But for long-context tasks, especially tasks where the model must retrieve a specific fact from much earlier in the sequence, this compression becomes a serious limitation.

This is why many recurrent alternatives to Transformers look promising in terms of efficiency but struggle on recall-heavy tasks. They can process long sequences cheaply, but they often lose the exact information that attention would have preserved.

Memory Caching starts from this observation.

The paper asks a direct question: what if recurrent models did not have to rely on only one fixed-size memory state?

Instead of forcing the model to compress the entire past into a single hidden state, Memory Caching stores checkpoints of the recurrent memory as the sequence is processed. The sequence is divided into segments. Each segment is compressed into a memory state. These memory states are then cached. Later tokens can retrieve not only from the current online memory, but also from cached memories from previous segments.

This gives recurrent models a form of growing memory. It is not the same as Transformer attention, because the model is not storing every token independently. It is storing compressed memory checkpoints. But it is also not a standard RNN, because the model is no longer limited to one fixed hidden state.

Memory Caching, therefore, creates a middle ground:

A standard RNN keeps one fixed-size memory.
A Transformer keeps token-level memory across the context.
Memory Caching keeps a growing set of compressed memories.
This is why the paper is interesting. It does not claim that Transformers are obsolete. In fact, the results show that Transformers remain very strong on some recall-intensive tasks. But it does show that the usual framing, efficient RNNs versus expensive attention, is incomplete. There is another point in the design space: recurrent models with memory that grows as the sequence grows.

Press enter or click to view image in full size

Figure 1. Transformers grow memory by keeping token-level access to the past. RNNs compress the past into one fixed state. Memory Caching sits between them by storing compressed recurrent memory checkpoints.
The important idea is that memory capacity is not only a detail of implementation. It is one of the central architectural constraints of sequence models. If a model has too little memory, it forgets. If it stores everything, it becomes expensive. The challenge is to decide what should be stored, how compressed it should be, and how the model should retrieve from it later.

Memory Caching is one attempt to answer that question. It keeps the recurrent update rule, but changes how memory is accessed. Instead of reading only from the latest hidden state, the model can read from a set of memory states. This simple change turns recurrence from a fixed-memory system into a growing-memory system.

That is why this paper matters. It is not just another efficient attention variant. It is part of a broader shift toward treating memory as an explicit design dimension in language models.

The Transformer era may not end because one architecture suddenly replaces attention. A more realistic possibility is that the “pure Transformer” era gradually gives way to hybrid memory systems: models that use attention where precise token-level access is needed, recurrent compression where efficiency matters, and cached memory states when long-range retrieval requires something between the two.

Press enter or click to view image in full size

Figure 2. In Memory Caching, each token can be retrieved from the current recurrent memory and from compressed memory checkpoints saved from earlier segments.
2. A Short History of Sequence Model Memory
To understand why Memory Caching is interesting, it helps to look at the history of sequence models through one specific lens: how the model stores and retrieves information from the past.

Most architecture debates are usually framed around speed, scaling, parallelism, or benchmark performance. Those are important, but they are downstream of a more basic design question: when a model reads a long sequence, where does the past go?

Different generations of sequence models answered this question differently.

Early recurrent neural networks (RNNs) answered it with a hidden state. The model reads one token, updates its state, reads the next token, updates the state again, and continues until the end of the sequence. The hidden state is the model’s working memory. Everything the model needs from the past must be compressed into that state.

This design is elegant because it is naturally efficient. The model does not need to keep every previous token in memory. It only needs to keep the current hidden state. In principle, this makes recurrence attractive for long sequences and streaming applications.

The problem is that the hidden state has a fixed size.

As the sequence grows longer, the model must keep compressing more information into the same amount of memory. Some information can be preserved, but some will be overwritten, diluted, or made difficult to retrieve. This is the classic long-term dependency problem. The model may process a token early in the sequence, but by the time it needs that information later in the sequence, the signal may no longer be available in a usable form.

Long Short-Term Memory networks, or LSTMs, were one of the most important attempts to address this weakness. Instead of using a simple recurrent state update, LSTMs introduced gates that control what should be remembered, what should be forgotten, and what should be exposed to the next step. This gave recurrent models a much better mechanism for managing information over longer time spans.

However, LSTMs did not fundamentally change the memory capacity problem. They improved how information is written and preserved, but they still relied on a fixed-size memory. The model still had to decide what to keep and what to forget as the sequence grew.

Press enter or click to view image in full size

Figure 3. The history of sequence modeling can be read as a history of memory design: from one fixed hidden state, to token-level attention, and now toward hybrid systems with compressed, growing memory.
The Transformer changed this trajectory by moving away from recurrence. Instead of passing all information through a single hidden state, the Transformer uses self-attention. Each token can compare itself with other tokens in the context and retrieve information directly from them.

This was a major shift. In recurrent models, the past is compressed before it is reused. In Transformers, the past remains available as token-level representations. This is why attention is so effective for tasks that require recall, copying, reference resolution, and in-context learning. A token near the end of a prompt can still access a relevant token near the beginning, as long as both are inside the context window.

This is also why Transformers became the default architecture for modern language models. Their memory mechanism fits the needs of language modeling very well. Language is full of dependencies that are not strictly local. A name, definition, instruction, or constraint may appear early in a document and become relevant much later. Attention gives the model a direct retrieval mechanism for this kind of dependency.

But the same design also creates the long-context cost problem. If each token can attend to many previous tokens, the computation and memory requirements grow quickly as the context becomes longer. During inference, the model also stores key and value representations in the KV cache, which makes long-context serving expensive.

So the field started looking again at alternatives.

Linear attention was one important step in this direction. It showed that under certain kernel formulations, attention can be rewritten in a recurrent form. Instead of explicitly comparing every query against all previous keys, the model maintains a compact state that can be updated as tokens arrive. This reduced the cost from quadratic to linear in sequence length, at least for that class of attention mechanisms.

This was an important conceptual moment because it blurred the line between attention and recurrence. It suggested that the difference between Transformers and RNNs is not always absolute. Some attention mechanisms can be expressed as recurrent updates, and some recurrent models can be understood as compressed memory systems.

After that, several modern architectures tried to recover some of the efficiency of recurrence while keeping as much Transformer-like performance as possible.

RWKV is one example. It tries to combine Transformer-style parallel training with RNN-style efficient inference. RetNet follows a similar motivation from another direction: it introduces a retention mechanism that can be computed in parallel, recurrent, or chunkwise recurrent forms.

Mamba uses selective state-space dynamics, where the model can decide what information to propagate or forget based on the current input. Titans introduces neural long-term memory, where a memory module learns to memorize historical context during inference.

These models are different in their details, but they are part of the same broader movement. The field is trying to move beyond the simple choice between:

full attention with strong recall but high cost
recurrence with low cost but fixed memory
Memory Caching fits directly into this line of work.

It does not start by replacing the recurrent update rule. Instead, it asks whether the fixed-memory bottleneck can be relaxed. If recurrent models struggle because they compress the entire past into one state, then one natural solution is to stop using only one state.

The idea is to save intermediate memory states as the sequence is processed. These saved states act as compressed checkpoints of the past. Later tokens can be retrieved from the current memory and from older cached memories. This gives the model a growing memory, but not in the same expensive way as full attention.

Press enter or click to view image in full size

Figure 4. Memory Caching keeps the efficiency intuition of recurrence, but avoids forcing the entire past through one active hidden state.
This historical context matters because Memory Caching is not just another variation of attention. It is part of a deeper architectural question: Can we design models where memory grows with the sequence, but in a compressed and controllable way?

Transformers solved the memory problem by keeping token-level access to the context. Recurrent models solved the efficiency problem by compressing the context into a fixed-size state. Memory Caching tries to combine parts of both ideas. It keeps recurring, but gives it a larger and more structured memory surface.

This is why the paper is best understood as a continuation of a long sequence modeling debate, not as an isolated architecture trick. The central issue has remained the same from RNNs to LSTMs to Transformers to modern recurrent models:

How much of the past should the model store, how compressed should that memory be, and how should the model retrieve from it when it matters?

3. The Core Bottleneck: Fixed Memory vs Growing Memory
The simplest way to understand the Memory Caching paper is to focus on one question:

Does the model’s memory grow with the sequence, or does it stay fixed?

This question separates the two major families of sequence models.

A standard recurrent model has fixed memory. It reads tokens one at a time and updates a hidden state. No matter whether the sequence has 100 tokens or 100,000 tokens, the model still carries the past through the same memory object. That memory may be a vector, a matrix, or a more complex learned state, but the key idea is the same: the model compresses the past into a fixed-size representation.

A Transformer has growing memory. As the sequence becomes longer, the number of stored token representations also grows. During attention, a token can compare itself with previous tokens and retrieve information directly from them. This gives the model much stronger access to the past, especially when the task requires exact recall.

The difference is not only about implementation. It changes what kind of information the model can preserve.

In a recurrent model, every new token modifies the memory state. The memory must keep useful information and discard less useful information. This is efficient, but it creates pressure. If the model reads a long document, early information has to survive many update steps before it can be used again. Some information may remain, but it is increasingly compressed into the same fixed memory state.

This is why recurrent models often struggle with recall-intensive long-context tasks. The model may have processed the relevant information earlier, but processing something is not the same as keeping it in a retrievable form. Once information has been compressed too heavily, the model may no longer be able to recover the exact detail needed later.

A Transformer avoids this specific bottleneck by keeping token-level access to the context. If a fact appears near the beginning of the prompt, a later token can still attend to it directly. The fact does not need to survive as a signal inside one hidden state. It remains available as part of the context representation.

But this is also why Transformers become expensive.

Attention gives the model a rich memory surface, but the cost grows with the number of tokens. In standard attention, tokens interact with other tokens, so the number of interactions grows quickly as the context length increases. During generation, the model also stores key and value representations in the KV cache. This makes long-context inference memory-intensive, especially when the model serves many users or processes very long documents.

So we get the central trade-off:

Recurrent models are efficient because they compress history into fixed memory.
Transformers are powerful because they keep growing token-level memory.
Memory Caching tries to create a middle ground by letting recurrent memory grow, but in compressed checkpoints rather than raw token-level states.
This is the main idea behind the paper.

Instead of asking a recurrent model to carry the entire past through one hidden state, Memory Caching saves intermediate memory states while the sequence is being processed. The sequence is divided into segments. Each segment is compressed by the recurrent memory module. At the end of a segment, the final memory state is cached.

Later tokens can be retrieved from two places:

the current online memory, which is still being updated as new tokens arrive;
older cached memories, which store compressed information from previous segments.
This changes the role of recurrence.

In a standard recurrent model, the latest memory state must represent everything useful from the past. In Memory Caching, the latest memory state is still important, but it is not alone. It is supported by a growing collection of older memory checkpoints. These checkpoints act like compressed snapshots of previous parts of the sequence.

The model no longer needs to compress the entire history into one active state. It can distribute the past across several cached states.

Press enter or click to view image in full size

Figure 5. Memory Caching turns memory capacity into a dial. It can behave closer to a standard RNN when few memory states are cached, or closer to attention when many cached states are available.
This “dial” is important. Memory Caching is not a single point between RNNs and Transformers. It defines a design space. If we cache very few memory states, the model remains close to a normal recurrent model. It is efficient, but the past is still heavily compressed. If we cache many memory states, the model has more direct access to older parts of the sequence. This improves recall, but increases retrieval cost.

At the extreme, if every token became its own segment, the model would move closer to the logic of attention. Each token would have its own stored memory contribution. That would improve memory resolution, but it would also move the model toward the cost profile of attention.

The practical value of Memory Caching is that it does not force the model into either extreme. It lets the architecture choose how much memory to store based on the segment size, retrieval strategy, and available compute budget.

This is why segmentation becomes one of the most important design choices in the method.

Suppose we split a long sequence into chunks of 256 tokens. Each chunk is processed by the recurrent memory module, and one memory checkpoint is cached for that chunk. If the sequence has 16,000 tokens, the model does not need to store 16,000 separate token-level memories. It can store a much smaller number of compressed memory checkpoints. A later token can then be retrieved from the current memory and from those cached checkpoints.

This is not as precise as full attention. A cached memory checkpoint represents a segment, not an individual token. The model may know that useful information is compressed into a certain segment of memory, but it does not have the same fine-grained access that a Transformer has to every token.

However, it is much less restrictive than a single recurrent state. The model does not need one hidden state to preserve everything. Each segment gets its own compressed memory footprint.

That is the core trade-off:

Memory Caching gives up some token-level precision in exchange for a more efficient growing memory.

This also explains why the method is especially relevant for long-context modeling. In short contexts, a fixed memory may be enough. If the task only requires local coherence or general language modeling behavior, recurrence can work well. But as the context grows, the weakness of fixed memory becomes more visible. The model must decide what to preserve and what to overwrite across a much longer sequence.

Long-context tasks often require something more specific. The model may need to retrieve a name, number, instruction, citation, code variable, or fact that appeared thousands of tokens earlier. This kind of retrieval is difficult if all historical information has been repeatedly compressed into one active state.

Memory Caching gives the model more retrieval paths. A later token can still read from the current memory, but it can also access memories from older segments. In other words, old information does not have to survive only by being repeatedly carried forward. It can remain available through a cached checkpoint.

Press enter or click to view image in full size

Figure 6. In a fixed-memory recurrent model, the query reads from one current state. In Memory Caching, the query can read from the online memory and from compressed checkpoints of older segments.
The word “compressed” is important here.

Memory Caching does not simply recreate Transformer attention. It does not store all previous tokens and attends to them directly. Each cached state is the result of a recurrent compression process. This means each memory checkpoint summarizes a segment, rather than preserving every token in its original form.

This creates a different kind of memory system. It is not raw memory. It is a structured, compressed memory. That distinction matters because it explains both the strength and the limitation of the method.

The strength is efficiency. The model can grow its memory without storing every token. The number of cached memories depends on the number of segments, not directly on the number of tokens. This can reduce the cost compared to full attention, especially when segments are larger than one token.

The limitation is recall resolution. Since each cached memory compresses a segment, some token-level detail may be lost. If a task requires the exact retrieval of a rare string buried inside a long segment, full attention may still have an advantage because it can directly access the token representation. Memory Caching improves recurrent recall, but it does not automatically make compressed memory equivalent to full attention.

This is why the paper’s claim is more subtle than “RNNs replace Transformers.”

A better reading is:

Memory Caching expands the design space between recurrent compression and full attention.

It shows that recurrent models do not have to remain fixed-memory systems. Their effective memory can grow with sequence length, but in a controlled way. Instead of storing everything, the model stores compressed checkpoints. Instead of reading only from the latest hidden state, it retrieves from multiple memory states.

This is the point where the paper becomes technically interesting. Once we allow recurrent models to keep several cached memories, we need to answer a new question:

How should the model combine these memories when producing the next output?

The simplest answer is to add them together. A more careful answer is to gate them based on relevance. A more efficient answer is to retrieve only a sparse subset. The rest of the paper explores these choices through several Memory Caching variants.

That is where we go next.

4. What Memory Caching Actually Does
Now that we have the high-level trade-off, we can describe the method itself. Memory Caching is built around a simple change: instead of using only the latest recurrent memory state, the model keeps older memory states and allows future tokens to retrieve from them.

This may sound small, but it changes the role of recurrence.

In a standard recurrent model, the hidden state is continuously updated. At every step, the model reads a new token, writes it into the current memory, and moves forward. The latest memory state is the only active summary of the past. If something useful was stored earlier, it has to remain in that state through all future updates.

Memory Caching keeps the recurrent update, but changes what happens at segment boundaries.

The input sequence is split into segments:

Segment 1 → Segment 2 → Segment 3 → … → Current segment

Each segment is processed by a recurrent memory module. At the end of a segment, the final memory state is saved as a cached checkpoint. So instead of having only one evolving memory, the model builds a collection of compressed memories:

M1, M2, M3, …, Monline

Here:

M1, M2, M3, and so on, are cached memories from previous segments.
Monline is the current online memory being updated inside the current segment.
A new query token can be retrieved from both the online memory and the cached memories.
This is the core of the method.

Press enter or click to view image in full size

Figure 7. Memory Caching divides the sequence into segments, stores compressed memory checkpoints, and lets later tokens retrieve from both the current online memory and older cached memories.
The easiest way to understand this is to separate the process into two phases: writing and reading.

During writing, the recurrent model behaves almost normally. Tokens arrive one by one, and the memory state is updated according to the recurrent update rule. If the model is based on linear attention, that update may look like a matrix update. If the model uses a deeper memory module, the update may look more like an inner learning step. The details can change depending on the underlying architecture.

But the abstract form is simple:

new memory = update(previous memory, key, value)

Each token contributes information to the memory through its key and value representations. The memory keeps changing as the segment is processed.

At the end of the segment, Memory Caching saves the final state.

That saved state is not a raw copy of all tokens. It is a compressed memory produced by the recurrent model after reading the segment. In other words, each cached memory is a learned summary of a block of tokens.

During reading, the method differs from a standard recurrent model.

A normal recurrent model computes the output from the current memory only:

output = read(current memory, query)
Memory Caching instead computes the output using the current memory and previous cached memories:

output = aggregate(
    read(current online memory, query),
    read(cached memory 1, query),
    read(cached memory 2, query),
    ...
)
The query token is no longer limited to the latest hidden state. It can ask several memory checkpoints for information.

This is why the paper describes the cached states as checkpoints of the memory process. The model is not just storing the final state after the entire sequence. It is storing intermediate states before they are overwritten or heavily modified by later tokens.

That is the key difference.

A standard recurrent model says:

Only the latest memory matters.

Memory Caching says:

Earlier memory states may still be useful, so keep them available.

This gives older information a more direct retrieval path. It does not need to survive only through repeated updates into the latest memory. If a segment contained useful information, its cached memory can still be queried later.

Press enter or click to view image in full size

Figure 8. Memory Caching leaves the recurrent write process mostly intact, but expands the read process so the model can retrieve from multiple memory states.
This distinction between writing and reading is important because Memory Caching is not simply “add more hidden states.” It changes the retrieval interface of the recurrent model.

The model still compresses each segment. It still benefits from the efficiency of recurrence. But at retrieval time, the model has a larger memory surface to query. Instead of asking one compressed state to answer everything, it can ask several compressed states, each representing a different part of the history.

This is also why the method can be applied to different recurrent architectures.

The paper applies Memory Caching to several types of memory modules, including Linear Attention, Sliding Window Linear Attention, Deep Linear Attention, and Titans-style deep memory modules. The underlying memory update rule can be different in each case. Memory Caching does not require one specific recurrent mechanism. It only requires a memory state that can be saved and later queried.

In this sense, Memory Caching behaves more like a framework than a single model architecture.

The framework has three main components:

4.1 Segment the sequence
The first component is segmentation.

The sequence is divided into blocks:

S1, S2, S3, …, SN

Each block contains a subset of tokens. The segment size determines how much information each cached memory must compress.

If segments are large, the model stores fewer memory checkpoints. This is more efficient, but each checkpoint has to compress more information.

If segments are small, the model stores more memory checkpoints. This gives the model more detailed access to the past, but retrieval becomes more expensive.

So segment size controls the memory-compute trade-off.

4.2 Cache the memory state
The second component is caching.

After the model processes a segment, it stores the final memory state for that segment. This state becomes a compressed representation of the segment.

For example:

Segment 1 → M1
Segment 2 → M2
Segment 3 → M3
Each M is not a token. It is not a KV cache in the Transformer sense. It is a recurrent memory state that has already compressed the information in the segment.

This is why Memory Caching is different from simply extending the context window. It does not preserve the full token-level representation of every previous token. It stores compressed memories.

The memory grows, but it grows in compressed form.

4.3 Retrieve from online and cached memories
The third component is retrieval.

When the model processes a token in the current segment, it forms a query. That query can be applied to the current online memory and to the cached memories from earlier segments.

Conceptually:

current output =
    contribution from online memory
  + contribution from cached memory 1
  + contribution from cached memory 2
  + contribution from cached memory 3
  + ...
This is the simplest version. The paper then introduces more careful aggregation strategies. Some variants add all cached memory outputs. Others learn gates so that relevant memories contribute more. Another variant selects only a sparse subset of cached memories.

But all variants share the same principle:

Do not rely on one memory state when several compressed memory checkpoints are available.

This is what allows the model’s effective memory capacity to grow with the sequence.

Press enter or click to view image in full size

Figure 9. Memory Caching can be understood as a three-step process: segment the sequence, cache compressed memory states, and retrieve from those states when later tokens need information.
A useful analogy is to think of a long document.

A standard recurrent model reads the document and keeps updating one notebook page. Every new paragraph modifies the same page. By the end, the page may contain a useful summary, but many details from early sections may be gone.

A Transformer keeps direct access to all paragraphs. This is powerful, but expensive if the document is very long.

Memory Caching is closer to keeping one compressed note per section. The notes are not the full document, but they preserve more structure than one final summary. Later, when answering a question, the model can look at the current note and also revisit notes from earlier sections.

This analogy is not perfect, but it captures the central idea: Memory Caching does not eliminate compression. It distributes compression across the sequence.

That is why it is useful to describe Memory Caching as growing compressed memory.

It grows because the model stores more memory checkpoints as the sequence grows.

It is compressed because each checkpoint summarizes a segment rather than preserving every token.

The next question is how these cached memories should be combined.

If the model simply adds them all together, older segments remain available, but every segment is treated equally. That may be too crude. A query about a fact from Segment 2 should probably use Segment 2 more than Segment 7. A query about the current local context should probably use the online memory more than the old cached states.

This leads to the main design space of the paper: different ways to aggregate cached memories.

The paper proposes several variants:

Residual Memory
Gated Residual Memory
Memory Soup
Sparse Selective Caching
Each one answers the same question differently:

When the model has many cached memories, which ones should contribute to the current output, and how?

That is the next part of the method.

5. Four Ways to Use Cached Memories
Once a recurrent model has access to multiple cached memories, the next question is how to use them. This is the main design space opened by Memory Caching. The model is no longer forced to retrieve from only one hidden state.

It can retrieve from the current online memory and from several cached memory checkpoints from earlier segments. But having more memory sources does not automatically solve the problem. The model still needs a rule for combining them.

The paper introduces four variants:

Residual Memory
Gated Residual Memory
Memory Soup
Sparse Selective Caching
All four variants share the same basic setup. The sequence is split into segments. Each segment produces a cached memory. The current segment has an online memory. For the current query, the model can read from these memory states.

What changes from one variant to another is the aggregation strategy. In other words, the variants answer the same question differently:

Given many memory states, how should the model combine them for the current token?

5.1 Residual Memory: Add the Cached Memories
The simplest version is Residual Memory. In this variant, the model reads from the current online memory and from all previous cached memories. Then it adds their outputs together.

Conceptually:

output =
    read(online memory, query)
  + read(cached memory 1, query)
  + read(cached memory 2, query)
  + read(cached memory 3, query)
  + ...
The intuition is straightforward. The online memory captures the current segment. The cached memories capture earlier segments. Adding their outputs gives the current token access to information from the entire history, but in compressed form.

This is the most direct way to turn recurrence into growing memory. A standard recurrent model has one read path:

query → current memory → output
Residual Memory creates several read paths:

query → current memory → output
query → cached memory 1 → output
query → cached memory 2 → output
query → cached memory 3 → output
The outputs are combined through a residual-style summation.

Press enter or click to view image in full size

Figure 10. Residual Memory retrieves from the online memory and all cached memories, then adds their outputs together. It is the simplest way to use cached recurrent memory.
The advantage of Residual Memory is simplicity. It does not require a router or a learned memory selection mechanism. If the cached memories exist, the model can read from them.

But this simplicity is also the main weakness.

Residual Memory treats all cached memories in the same way. A segment from thousands of tokens ago and a segment from the recent past both contribute to the output. This may be useful when broad historical information matters, but it is not ideal when the query only needs one specific part of the context.

For example, suppose Segment 2 contains the definition of a variable, while Segment 8 contains unrelated discussion. If the current token asks about that variable, Segment 2 should contribute more than Segment 8. Residual Memory does not explicitly model this difference.

This leads to the next variant.

5.2 Gated Residual Memory: Weight the Relevant Memories
Gated Residual Memory, or GRM, adds a relevance weight to each memory. Instead of treating every cached memory equally, the model learns how much each segment should contribute to the current output. The output is still built from the online memory and cached memories, but each memory output is multiplied by a gate.

Conceptually:

output =
    gate_online × read(online memory, query)
  + gate_1      × read(cached memory 1, query)
  + gate_2      × read(cached memory 2, query)
  + gate_3      × read(cached memory 3, query)
  + ...
The gate controls the contribution of each memory.

If a cached memory is relevant to the current query, its gate should be high. If it is irrelevant, its gate should be low.

This makes retrieval context-dependent.

The paper proposes making the gate depend on both the current token and the segment representation. A simple segment representation can be created by mean-pooling the token representations inside the segment. The model then compares the current token representation with the segment representation to estimate relevance.

This is an important design choice. If the gate only depends on the current token position, it becomes mostly positional filtering. But if the gate depends on both the current token and the content of the segment, the model can retrieve based on contextual similarity.

In simple terms:

Does this query look related to this past segment?
If yes, that segment contributes more.

Press enter or click to view image in full size

Figure 11. Gated Residual Memory makes retrieval query-dependent. Each cached memory receives a learned relevance weight before its output is added to the final prediction.
GRM is more expressive than simple Residual Memory because it can focus on relevant segments. It also avoids a subtle issue in the linear memory case.

For strictly linear memory, simple residual summation can sometimes collapse into a single pre-summed memory. In that case, the model is not really using separate cached memories in a dynamic way. The memories are separate in implementation, but the final computation can behave like one larger fixed memory.

GRM avoids this because the gates depend on the current token. Since the weights change from token to token, the model cannot pre-sum all cached memories once and reuse the same result for every query. Each token can build a different mixture of memories.

This is why gating is more than a minor addition. It turns cached memory retrieval into an input-dependent operation.

5.3 Memory Soup: Combine the Memories Before Reading
Residual Memory and GRM both follow the same pattern:

read from each memory → combine the outputs
Memory Soup changes the order:

combine the memories → read from the combined memory
This distinction is important.

In Residual Memory, each cached memory produces its own output for the query. The model then adds or weights these outputs. This is output aggregation.

In Memory Soup, the model combines the memory states themselves into a new temporary memory. Then the query is applied to that combined memory.

Conceptually:

combined memory =
    gate_1 × cached memory 1
  + gate_2 × cached memory 2
  + gate_3 × cached memory 3
  + gate_online × online memory

output = read(combined memory, query)
This is why the paper calls it Memory Soup. It is inspired by the idea of model soups, where multiple model weights are averaged into one model. Here, the “models” being mixed are the cached memory modules.

The key idea is that each token can build its own memory for retrieval.

For the current token, the model creates a temporary memory from the cached memory states. That temporary memory depends on the relevance gates, so it can change from token to token.

Press enter or click to view image in full size

Figure 12. Gated Residual Memory combines memory outputs. Memory Soup combines the memory states first, then reads from the resulting temporary memory.
For linear memory, this distinction may not matter much. If the memory is just a linear matrix, then blending the memories first and applying the query can be mathematically equivalent to applying the query to each memory and blending the outputs afterward.

But with deep or non-linear memory, the distinction becomes meaningful.

If each memory is a neural module rather than a simple matrix, then blending the parameters creates a different retrieval function. The query is not only choosing how much to use each memory output. It effectively constructs a temporary memory module specialized for the current token.

This makes Memory Soup especially interesting for architectures with deep memory, such as Deep Linear Attention or Titans-style memory modules.

The practical intuition is:

GRM asks several questions and combines their answers.
Memory Soup builds a temporary memory from several memories and asks that memory once.
This is a more complex retrieval mechanism, but it gives the model a richer way to use cached states.

5.4 Sparse Selective Caching: Retrieve Only What Matters
The previous variants retrieve from all cached memories. This is useful for recall, but it can become expensive when the sequence is very long.

If the model has hundreds or thousands of cached memory checkpoints, reading from all of them for every token may be too costly. This creates a new problem: Memory Caching improves capacity, but naive retrieval can still become expensive.

Sparse Selective Caching, or SSC, addresses this by retrieving only a subset of cached memories.

Instead of reading from every cached checkpoint, the model uses a router. The router compares the current token with summaries of past segments and selects the top-k most relevant cached memories.

Conceptually:

query token → router → choose top-k cached memories → retrieve only from selected memories
The online memory is still used, but only a small subset of cached memories is loaded and queried.

This is similar in spirit to sparse Mixture-of-Experts models. In MoE models, a router chooses which experts should process a given token. In SSC, a router chooses which cached memories should be used for a given token.

The difference is that the “experts” here are not independent feed-forward networks. They are cached memory states from previous sequence segments.

Press enter or click to view image in full size

Figure 13. Sparse Selective Caching uses a router to select only the most relevant cached memories, reducing retrieval cost while preserving long-range access.
SSC is important because it makes Memory Caching more practical for very long sequences. Without sparsity, the retrieval cost grows with the number of cached segments. If the sequence is divided into many segments, the model may have a strong memory capacity but expensive retrieval.

With SSC, the model can store many cached memories but only activate a few at a time. This gives a better trade-off:

many memories stored, few memories retrieved

This is useful because most queries do not need the entire past. A token usually needs either the local context, a specific earlier segment, or a small subset of relevant historical information. SSC lets the model search over memory summaries first, then retrieve from the memories that are likely to matter.

5.5 Comparing the Four Variants
The four variants can be understood as increasingly selective ways to use cached memory.

Press enter or click to view image in full size

A useful way to summarize the design space is:

Residual Memory: use everything equally
Gated Residual Memory: use everything, but weight it
Memory Soup: build a temporary memory from weighted memories
Sparse Selective Caching: select only the most relevant memories
Each variant makes a different trade-off between simplicity, expressiveness, and efficiency.

Residual Memory is the easiest to understand. It gives the model access to older memory states without adding a selection mechanism.
GRM is more adaptive. It keeps all memories available but lets the query decide how much each one should matter.
Memory Soup is more expressive when the memory module is deep or non-linear. Instead of combining answers, it combines the memory modules themselves.
SSC is the efficiency-oriented version. It recognizes that long-context memory is only useful if retrieval remains manageable.
Press enter or click to view image in full size

Figure 14. The Memory Caching variants differ in how they combine cached memory states: equal summation, gated weighting, parameter blending, or sparse routing.
The important point is that Memory Caching is not only about storing more memory. Storage alone is not enough. The model also needs a retrieval policy.

This is similar to a database or search system. Having a large archive is useful only if the system can retrieve the right part of it at the right time. Residual Memory gives the model a simple archive scan. GRM gives it soft relevance weighting. Memory Soup builds a temporary memory from the archive. SSC adds a router that narrows the search before retrieval.

This makes Memory Caching more than a fixed architectural trick. It defines a family of memory retrieval mechanisms for recurrent models.

The next design question is segmentation.

If cached memories are created per segment, then the size and structure of those segments will strongly affect the behavior of the model. Large segments create fewer memories but require more compression. Small segments create more memories but increase retrieval cost.

That trade-off is the focus of the next section.

6. Segmentation: The Hidden Design Choice
Memory Caching depends on one design choice that may look secondary at first, but actually controls the whole method:

How should the sequence be split into segments?

Every cached memory comes from a segment. If the segmentation is poor, the cached memories will be poor. If the segments are too large, each memory has to compress too much information. If the segments are too small, the model stores too many memories, and retrieval becomes expensive.

So segmentation is not just a preprocessing detail. It is one of the main architectural knobs in Memory Caching.

The reason is simple. Memory Caching does not store raw tokens. It stores compressed memory checkpoints. Each checkpoint represents a segment of the sequence. Therefore, the segment size determines how much information is compressed into each cached memory.

A segment can be thought of as a compression unit.

Segment → recurrent memory update → cached memory checkpoint

If a segment contains 1,024 tokens, then one cached memory must summarize 1,024 tokens. If a segment contains 128 tokens, the cached memory only needs to summarize 128 tokens. Smaller segments give the model more detailed memory, but they also create more cached states.

This creates a direct trade-off:

smaller segments → more cached memories → better memory resolution → higher retrieval cost

larger segments → fewer cached memories → stronger compression → lower retrieval cost

This is one of the most important parts of the paper because it shows that Memory Caching is not a single fixed architecture. It is a memory design framework. By changing the segmentation strategy, we can move the model closer to a standard RNN or closer to Transformer-like memory.

6.1. The Two Extremes: RNNs and Transformers
The paper makes a useful observation: both standard RNNs and Transformers can be seen as extreme cases of the same segmentation idea.

At one extreme, a standard recurrent model behaves as if the entire sequence is one segment. The model processes all tokens and only keeps one final memory state. This gives maximum compression and low memory cost, but it also creates the strongest forgetting bottleneck.

Whole sequence → one memory state

At the other extreme, a Transformer behaves like a system where each token effectively has its own memory representation. This gives the model fine-grained access to the past, but it is expensive because memory grows at token-level resolution.

Each token → separate accessible representation

Memory Caching sits between these extremes.

Instead of one memory for the entire sequence or one memory per token, it creates one memory per segment:

Segment 1 → M1
Segment 2 → M2
Segment 3 → M3
...
Segment N → MN
This is why segmentation is the control dial. It determines how many compressed memories exist and how much each memory has to summarize.

Press enter or click to view image in full size

Figure 15. Segment size controls where Memory Caching sits between fixed recurrent memory and token-level Transformer memory.
6.2 Constant-Size Segmentation
The simplest strategy is constant-size segmentation.

Here, the sequence is divided into equal-sized blocks. For example, the model might cache one memory every 256 tokens:

Tokens 1–256       → M1
Tokens 257–512     → M2
Tokens 513–768     → M3
Tokens 769–1024    → M4
...
This is easy to implement and easy to reason about. Every memory checkpoint represents the same number of tokens. The model gets a regular memory grid over the sequence.

The main advantage is predictability.

If the segment size is fixed, then the number of cached memories grows linearly with the sequence length. A 16,000-token context with a segment size of 256 produces about 62 cached memories. A 64,000-token context produces about 250 cached memories.

This makes the memory structure regular. The model can learn that each cached state represents roughly the same amount of history.

Constant-size segmentation also preserves relatively uniform resolution across the context. The beginning of the sequence and the middle of the sequence are compressed at the same level. No part of the history is forced into a much larger segment simply because it is old.

That is useful for recall tasks. If a key fact appears early in the sequence, it is not necessarily buried inside an enormous old segment. It is stored in a segment of the same size as every other segment.

But the downside is cost.

If every segment is the same size, then the number of cached memories keeps growing as the sequence grows. If the model reads from all cached memories, retrieval can become expensive. This is why constant-size segmentation often needs gating or sparse retrieval to remain practical at very long context lengths.

Press enter or click to view image in full size

Figure 16. Constant-size segmentation gives every part of the context the same memory resolution, but the number of cached memories grows with sequence length.
6.3 Logarithmic Segmentation
The paper also discusses logarithmic segmentation. The motivation is efficiency. Instead of keeping memory resolution constant across the entire history, logarithmic segmentation compresses older history more aggressively while keeping more recent information at finer resolution.

This resembles how many memory systems are designed. Recent information is often stored in more detail, while older information is summarized into larger chunks.

A simple intuition is:

Recent past → small segments
Far past    → larger segments
For example, the model might keep the most recent tokens in small segments, then merge older tokens into larger and larger segments as they move further away from the current position.

This reduces the number of cached memories. If the number of segments grows logarithmically rather than linearly, retrieval can be much cheaper for very long sequences.

That is the appeal of logarithmic segmentation.

However, the paper points out a problem: logarithmic segmentation can create segments that are too large or too small, depending on the position. Very large old segments may force the recurrent memory to compress too much information into one checkpoint. Very short segments may not give the memory module enough tokens to form a useful representation.

So logarithmic segmentation is computationally attractive, but it can be less stable as a memory representation.

The deeper issue is that compression is not free. When a segment becomes large, the memory checkpoint has to summarize more information. If the task later asks for a precise fact within that large segment, the model may not be able to recover it.

This is the same problem as standard recurrence, but now it appears locally inside each large segment.

Press enter or click to view image in full size

Figure 17. Constant segmentation keeps memory resolution uniform. Logarithmic segmentation reduces the number of cached memories, but the older history becomes more heavily compressed.
6.4 Segment Size as a Compression Budget
A useful way to think about segment length is as a compression budget.

Each segment asks the model to solve the same problem:

What information from this block of tokens should be preserved in the memory checkpoint?

The larger the segment, the harder this problem becomes.

For a small segment, the memory only needs to summarize a short span. It may be easier to preserve names, numbers, definitions, local dependencies, or instructions. For a large segment, the memory has to decide what matters across a much wider range of tokens.

This matters because many long-context tasks are not only about general topic understanding. They often require exact or semi-exact retrieval.

Examples:

A name mentioned once near the beginning of a document.
A variable definition from an earlier code block.
A constraint in a long instruction.
A number from a table.
A short phrase hidden inside a long context.
If that information is inside a large segment, it may be compressed away. If it is inside a smaller segment, the model has a better chance of preserving it in a retrievable form.

So segment size affects recall quality.

But smaller is not always better. If segments become too small, the model stores many cached memories. Then the retrieval system has to search or aggregate over many states. This increases compute and may introduce noise if too many irrelevant memories are available.

This is why segmentation and retrieval should be designed together.

Constant-size segmentation may work well with Sparse Selective Caching because the router can choose from many uniformly sized memory checkpoints. Logarithmic segmentation may reduce retrieval cost, but it places more pressure on the memory module to compress older segments effectively.

The best choice depends on the target workload.

For a task that mostly depends on recent context, larger old segments may be acceptable. For a task that requires precise recall anywhere in the document, uniform or smaller segments may be better.

Press enter or click to view image in full size

Figure 18. Segment size trades off retrieval cost against memory resolution. Larger segments are cheaper but more lossy; smaller segments preserve more detail but create more cached memories.
6.5 Why Segmentation Is Different from Chunking in RAG
It may be tempting to compare Memory Caching segmentation to chunking in retrieval-augmented generation. There is a connection, but they are not the same.

In RAG, chunking usually splits documents into text chunks that are embedded and stored in a vector database. At retrieval time, the system searches for chunks that are semantically similar to the query.

Memory Caching also splits a sequence into segments, but each segment is not stored as plain text or as a static embedding. It is processed by a recurrent memory module, and the resulting memory state is cached.

So the cached object is not a document chunk. It is a learned memory state.

This difference matters. A RAG chunk preserves text. A cached recurrent memory compresses the text through the model’s internal memory update rule. It may preserve semantic and task-relevant information, but it does not guarantee exact text recovery.

That is why Memory Caching should be understood as architectural memory, not external retrieval.

It changes the model’s internal computation. The model is not calling an external retriever over documents. It is querying its own cached memory states.

Still, the analogy is useful at a high level: both RAG and Memory Caching depend heavily on how information is segmented. In both cases, the system must decide what unit of history should become retrievable later.

6.6 The Practical Interpretation
The main lesson is that Memory Caching does not remove the memory trade-off. It makes the trade-off explicit.

A standard RNN hides the trade-off inside one hidden state. The entire past is compressed, whether we like it or not.

A Transformer avoids much of that compression by keeping token-level memory, but pays for it with high context cost.

Memory Caching exposes the intermediate design space. It lets us choose:

How many memory states to cache;
How much text each memory should compress;
Whether old and recent information should have the same resolution;
Whether retrieval should read from all memories or only selected ones.
This makes memory capacity a tunable architectural budget.

The important question is no longer simply:

Should we use attention or recurrence?
It becomes:

How much memory resolution do we need, and where should we spend it?
For long-context models, this is a useful shift. Many applications do not need full token-level attention over the entire context. They need enough memory to preserve the parts of the sequence that may matter later. Memory Caching gives recurrent models a mechanism for doing that, but segmentation determines how effective that mechanism can be.

The next section moves from segmentation to the type of memory being cached. The behavior of Memory Caching is different when the memory module is linear versus when it is deep or non-linear.

7. Linear Memory vs Deep Memory: What Is Actually Being Cached?
So far, we have described Memory Caching as if every cached memory is the same kind of object:

Segment 1 → M1
Segment 2 → M2
Segment 3 → M3
But this hides an important detail.

What exactly is M?

In a normal RNN, we usually think of memory as a hidden state vector. In linear attention, the memory may be a matrix-like state that accumulates key-value information. In a deeper memory model, the memory may be closer to a small neural network that changes as it reads the sequence.

This matters because Memory Caching does not behave the same way for every kind of memory.

The paper discusses Memory Caching in both linear memory modules and deep memory modules. This distinction is important because it affects how useful the cached states are, how different the Memory Caching variants really are, and why methods like Memory Soup become more interesting when the memory is deep.

7.1 Linear Memory: A Compressed Matrix of the Past
The simplest case is linear memory.

Linear memory appears naturally in linear attention. In standard attention, a query compares itself against many previous keys, then uses the resulting attention weights to retrieve values. This gives the model token-level access to the past, but it is expensive because the query has to interact with many previous tokens.

Linear attention changes the computation. Instead of storing every past key and value separately, the model maintains a compact recurrent state. Each new token updates this state, and later queries read from it.

At a high level, the memory behaves like this:

memory state = accumulated key-value information from previous tokens

Then a query reads from that memory:

output = query applied to memory state

This is why linear attention can be written in a recurrent form. The model does not need to attend explicitly to every previous token. It can update a memory state as tokens arrive.

In this setting, a cached memory is often a matrix-like state produced after processing a segment.

For example:

Segment 1 → linear memory matrix M1
Segment 2 → linear memory matrix M2
Segment 3 → linear memory matrix M3
Each memory checkpoint summarizes the key-value information from its segment.

This fits Memory Caching nicely. Instead of keeping only the latest accumulated memory, the model can store several intermediate linear memory states and query them later.

However, linear memory also creates an interesting limitation.

Because the memory operation is linear, some ways of combining cached memories can collapse into simpler operations. For example, if we simply add the outputs from several linear memories, this can be close to reading from one summed memory.

Conceptually:

read(M1, query) + read(M2, query) + read(M3, query)

may behave similarly to:

read(M1 + M2 + M3, query)

This means that simple Residual Memory may not fully exploit the fact that the memories are separate. If every cached memory contributes equally, then the model may behave as if it had one larger accumulated memory rather than several distinct segment memories.

This is one reason why gating matters.

With Gated Residual Memory, the contribution of each cached memory depends on the current query:

γ1(query) × read(M1, query)
γ2(query) × read(M2, query)
γ3(query) × read(M3, query)

Now the model cannot simply pre-sum all memories once and use the same result for every token. Different queries can create different weighted combinations of cached memories.

So, for linear memory, the key advantage of Memory Caching is not only that it stores more memory states. The real advantage comes when the model can select, gate, or route among those states in a query-dependent way.

Press enter or click to view image in full size

Figure 19. With linear memory, cached states are compact matrix-like summaries. Gating is important because it prevents all cached memories from collapsing into one fixed summed memory.
7.2 Deep Memory: A Memory Module That Learns During Inference
Deep memory is more expressive.

Instead of treating memory as a simple matrix, a deep memory module can be a small neural network or a parameterized function. As the model reads tokens, this memory module is updated. In other words, the model is not only accumulating key-value statistics. It is adapting an internal memory function.

This is the idea behind architectures such as Deep Linear Attention and Titans-style neural memory. The model has a memory module that can be updated during the forward pass. It can learn from the current sequence and then use that learned memory later.

This changes what a cached memory checkpoint represents. With linear memory, a cached memory is usually in a compressed state. With deep memory, a cached memory can be closer to a learned retrieval function.

That is a stronger object. Instead of saying:

M1 stores a summary of Segment 1

We can think of it as:

M1 is a memory function adapted to Segment 1

This distinction matters because different segments can produce different memory functions. A segment full of code may adapt the memory differently from a segment full of natural language. A segment containing definitions may produce a different memory state from a segment containing examples or instructions.

So when the model caches deep memories, it is not only saving compressed summaries. It may be saving different versions of a memory module after it has adapted to different parts of the sequence.

This makes Memory Caching more expressive.

A later query can retrieve from several learned memory functions, each shaped by a different segment of the past.

Press enter or click to view image in full size

Figure 20. Memory Caching is more expressive with deep memory because each cached state can represent a different adapted memory function, not only a linear summary.
7.3 Why Memory Soup Matters More for Deep Memory
The distinction between linear and deep memory also explains why Memory Soup is important.

Recall the difference between Gated Residual Memory and Memory Soup.

Gated Residual Memory reads from each memory separately, then combines the outputs:

read M1 → o1
read M2 → o2
read M3 → o3

weighted sum of o1, o2, o3 → final output
Memory Soup combines the memories first, then reads from the combined memory:

weighted blend of M1, M2, M3 → M*

read from M* → final output
For linear memory, these two operations can sometimes be very similar. If reading is a linear operation, then blending memories before reading may produce the same result as reading separately and blending outputs afterward.

But for deep memory, the order matters.

If each memory is a neural module, then blending the memory parameters creates a new temporary memory function. The query is not only combining answers. It is reading from a new memory object constructed from several cached memories.

This makes Memory Soup more than a different implementation detail.

It changes the retrieval function itself.

A useful way to understand this is:

Gated Residual Memory = ask several memories and combine their answers

Memory Soup = build a temporary memory from several memories, then ask it once
When the memory is linear, the difference may be small. When the memory is deep, the difference can be significant because the model is blending learned memory functions, not just numeric summaries.

Press enter or click to view image in full size

Figure 21. Memory Soup becomes more meaningful when cached memories are deep modules, because blending memories can create a new query-specific retrieval function.
7.4 Cached Memory as a Checkpoint of Learning
There is another useful interpretation. A recurrent memory model can be seen as learning from the sequence as it processes it. Each token updates the memory. After a segment, the memory has adapted to that segment. When Memory Caching saves the memory at that point, it is saving a checkpoint of this adaptation process.

This is why the word “checkpoint” is useful.

The model is not only storing a representation of a segment. It is storing the state of the memory after processing that segment.

For a long sequence, the memory evolves over time:

M0 → M1 → M2 → M3 → … → Monline

A standard recurrent model only uses the latest state. Earlier states disappear as the memory continues to update.

Memory Caching keeps those earlier states available:

save M1
save M2
save M3
…
continue updating Monline

This is especially important when later updates might overwrite or distort useful information from earlier segments. By caching intermediate states, the model keeps access to earlier versions of its memory before they were modified by later context.

Press enter or click to view image in full size

Figure 22. Cached memories can be interpreted as checkpoints of the memory module as it adapts to different parts of the sequence.
7.5 Checkpoints vs Independent Segment Compressors
There is also a second way to think about cached memories.

In the checkpoint view, the same memory evolves through the sequence, and we save intermediate states:

M0 → M1 → M2 → M3

But we can also interpret cached memories as independent compressors for different segments:

Segment 1 → compressor → M1
Segment 2 → compressor → M2
Segment 3 → compressor → M3

These views are related, but they emphasize different things.

The checkpoint view highlights continuity. The memory state evolves as the model reads the sequence, and cached memories preserve earlier stages of this evolution.

The independent-compressor view highlights segmentation. Each segment produces its own compressed memory, and retrieval later chooses among those compressed segment memories.

Both views are useful.

If we want to understand why Memory Caching helps recurrent models, the checkpoint view is natural. It says: earlier useful states do not need to be overwritten completely.

If we want to understand routing and sparse selection, the independent-compressor view is natural. It says: each cached memory is a retrievable unit, similar to a learned compressed chunk.

Press enter or click to view image in full size

Figure 23. Memory Caching can be interpreted either as checkpointing an evolving recurrent memory or as building compressed memories for individual segments.
7.6 Why This Distinction Matters
The difference between linear and deep memory affects the whole method.

With linear memory, Memory Caching is mainly about increasing the number of compressed states and learning how to weight or select them. The memory objects are simple, efficient, and easy to aggregate, but some variants may become mathematically similar unless the aggregation is query-dependent.

With deep memory, Memory Caching becomes more expressive. Each cached state may represent a different adapted memory module. The model can retrieve not only from different summaries, but from different memory functions shaped by different parts of the sequence.

This helps explain why the paper evaluates Memory Caching across different recurrent architectures rather than only one. The method is meant to be general. It can be applied to linear attention-style memory, sliding-window linear attention, deep linear attention, and Titans-style memory.

The same high-level idea stays fixed:

cache memory states across segments and retrieve from them later

But the behavior depends strongly on what kind of memory state is being cached.

This gives us a more complete picture of the method.

Memory Caching has three major design dimensions:

How the sequence is segmented
How cached memories are aggregated or selected
What kind of memory module is being cached
The previous section covered segmentation. The section before that covered aggregation. This section covered the memory module itself.

Next, we can look at how the paper applies Memory Caching to actual architectures, including Linear Attention, Sliding Window Linear Attention, Deep Linear Attention, and Titans.

8. Applying Memory Caching to Real Architectures
At this point, Memory Caching may sound like a standalone architecture. But the paper’s framing is slightly different.

Memory Caching is better understood as a general technique that can be added to recurrent memory models.

The method does not require every model to use the same hidden state, the same update rule, or the same retrieval function. It only requires one basic property: the model must have a recurrent memory state that can be saved and queried later.

This is why the paper applies Memory Caching to several architectures instead of presenting it as one isolated model.

The common pattern is always the same:

recurrent update rule → segment-final memory state → cached memory checkpoint → later retrieval

The memory module may be simple or deep. The segment memory may be matrix-like or neural-network-like. The retrieval function may read from all cached memories, gate them, blend them, or sparsely select a subset. But the high-level wrapper stays the same.

This is one of the more important contributions of the paper. It does not only propose a new recurrent architecture. It proposes a way to give existing recurrent architectures a growing memory surface.

Press enter or click to view image in full size

Figure 24. Memory Caching acts like a wrapper around recurrent memory models. The underlying update rule can change, but the cached-memory interface stays the same.
8.1 Linear Attention: The Simplest Case
The first architecture family is Linear Attention.

Linear Attention is one of the most natural starting points because it already has a recurrent interpretation. Instead of computing full pairwise attention between tokens, the model maintains a compact memory state that accumulates information from previous keys and values. A query then reads from that memory state.

This gives Linear Attention an efficiency advantage over full attention. The model does not need to store and compare every previous token, unlike a standard Transformer. It can process the sequence through a recurrent update.

However, this also means that Linear Attention inherits the recurrent memory bottleneck.

The memory state is compact. It is efficient, but it is still a compressed representation of the past. If the sequence becomes long and the task requires precise recall, the model may lose information that a Transformer would have kept accessible through token-level attention.

Memory Caching changes this by saving intermediate linear memory states.

Instead of relying only on the latest accumulated linear memory, the model can cache one memory per segment:

Segment 1 → linear memory M1
Segment 2 → linear memory M2
Segment 3 → linear memory M3

A later query can then read from the current online memory and from these older cached memories.

This makes the effective memory of Linear Attention grow with sequence length. It is still not the same as full attention because each cached memory compresses a segment, not an individual token. But it is no longer limited to one fixed-size state.

The important detail is that Linear Attention has a linear memory module. This means that some aggregation variants can become mathematically similar. If all cached memories are simply added together, the result can behave like one larger accumulated memory. This is why query-dependent gating is important. Gating lets different tokens use different mixtures of cached segment memories.

In practice, Linear Attention is the cleanest example of Memory Caching because it shows the core idea with the least architectural complexity:

linear recurrent memory + cached checkpoints = growing compressed memory

Press enter or click to view image in full size

Figure 25. For Linear Attention, Memory Caching stores matrix-like memory states at segment boundaries and lets later queries retrieve from them.
8.2 Sliding Window Linear Attention: Local Updates with Cached Memory
The paper also applies Memory Caching to Sliding Window Linear Attention, or SWLA.

The motivation behind SWLA is slightly different from standard Linear Attention. In a simple online linear recurrence, the memory is updated token by token using the latest input. SWLA updates memory based on a window of recent tokens. This gives the update rule access to a small local context rather than only the immediately current token.

This is useful because local context often matters. A token’s meaning is not always independent of the nearby tokens around it. By updating memory with a short window, the model can produce a richer segment memory than a purely token-by-token update.

But SWLA still has the same broad limitation: it remains recurrent and compressed. Its memory is still a fixed-size object unless we add a mechanism that lets memory grow.

Memory Caching gives SWLA that mechanism.

The model can process each segment using the SWLA update rule, then save the resulting memory state as a cached checkpoint. Later queries can retrieve from the current SWLA memory and from older cached memories.

This creates a useful combination:

SWLA gives the memory update a better local context.
Memory Caching gives the model access to older memory checkpoints.
Gating or sparse selection decides which past segments should matter for the current query.
So SWLA with Memory Caching is not simply “Linear Attention with more memory.” It is a version where each cached memory may already reflect a local window of contextual updates.

That makes each checkpoint potentially more informative than a memory updated from isolated individual tokens.

Press enter or click to view image in full size

Figure 26. SWLA changes how each memory state is written: instead of updating only from the latest token, the memory update can use a local window before being cached.
8.3 Deep Linear Attention: From Matrix Memory to Learned Memory Functions
The next architecture is Deep Linear Attention, or DLA. This is where the distinction from the previous section becomes more important. With standard Linear Attention, memory is matrix-like. With deeper memory modules, the memory can behave more like a learned function that changes as it processes the sequence.

Deep Linear Attention extends the simple linear memory idea by making the memory module deeper and more expressive. Instead of a shallow matrix-style update, the memory has additional structure. This can make it better at representing complex patterns, but it also changes the meaning of a cached memory checkpoint.

In simple linear memory, a checkpoint is mostly a compressed key-value summary of a segment.

In deep memory, a checkpoint can be closer to an adapted memory module. The model has processed a segment, updated its memory, and the resulting state reflects how the memory has adapted to that segment.

This is where Memory Caching becomes more interesting.

If we cache deep memory states from different segments, we are not only storing several summaries. We may be storing several different memory functions, each shaped by a different part of the context.

This makes variants such as Memory Soup more meaningful. For linear memory, blending memory states before reading can sometimes resemble blending outputs after reading. But for deep memory, blending the memories first can create a new temporary memory function. The retrieval function itself changes.

So DLA gives Memory Caching more expressive room.

The cached memories are not just smaller versions of attention. They are checkpoints of a learned memory module as it adapts over the sequence.

Press enter or click to view image in full size

Figure 27. For deep memory modules, cached states can represent adapted memory functions. This makes Memory Soup more meaningful because blending memories can change the retrieval function itself.
8.4 Titans: Neural Long-Term Memory Meets Memory Caching
The most interesting application is Titans. Titans is built around the idea of neural long-term memory. Instead of relying only on attention or a fixed recurrent state, Titans introduces a memory module that can learn to store historical context during inference. This makes it already aligned with the motivation behind Memory Caching: better long-context behavior through explicit memory design.

However, even a neural memory module can face a form of memory pressure.

If the model keeps updating the same memory as it reads a long sequence, then earlier information may still be overwritten, diluted, or transformed by later updates. The memory may be more expressive than a simple linear state, but it is still an evolving state.

Memory Caching offers a way to preserve intermediate stages of that evolution.

For Titans, cached memories can be interpreted as checkpoints of the neural memory after it has adapted to different parts of the sequence. A later query can retrieve from the current memory and from older memory checkpoints.

This is important because long-context tasks often require both adaptation and recall. A model may need to adapt to the topic, style, or structure of a document. But it may also need to retrieve a specific fact, name, instruction, or detail from an earlier part of the context. If the memory keeps changing, that earlier detail may become harder to access. Caching gives the model an additional route back to previous memory states.

This makes Titans a good test case for Memory Caching because it asks whether an already memory-focused recurrent architecture can benefit from explicit memory checkpointing.

The paper’s answer is yes: Memory Caching is not only useful for simple linear memory. It can also improve architectures with deeper, learned memory modules.

8.5 Why Architecture-Agnostic Matters
The main takeaway from these applications is that Memory Caching is architecture-agnostic.

It does not depend on one specific recurrent implementation. It can be applied to any model that has a recurrent memory update and a retrieval operation. Linear Attention, SWLA, DLA, and Titans differ in how they write to memory and how expressive that memory is, but they all expose something Memory Caching can use: a memory state that can be saved.

This matters because the field is not likely to converge on a single replacement for Transformers. More likely, future long-context architectures will combine several ideas:

local attention for short-range precision;
recurrent memory for efficient sequence processing;
cached memory checkpoints for long-range access;
routing or gating to decide which memory should be used;
deep memory modules when simple compressed states are not expressive enough.
Memory Caching fits into this broader direction. It is not a claim that every model should become a classic RNN again. It is a way to make recurrent memory less brittle by giving it a growing set of accessible checkpoints.

This is why the paper’s architecture experiments are important. They show that the method is not tied to one carefully engineered model. It can be layered onto different recurrent memory designs.

Press enter or click to view image in full size

Figure 28. Memory Caching applies broadly because it only requires a recurrent memory state that can be saved and queried later.
8.6 The Practical Interpretation
For engineers and researchers, the practical interpretation is straightforward.

If a model already has a recurrent memory state, the question becomes:

Should we rely only on the latest state, or should we preserve older states as retrievable checkpoints?

Memory Caching argues for the second option.

The latest memory is not always the best memory for every query. It is the most recent memory, but not necessarily the most relevant one. A query about Segment 2 may be better served by a cached memory from Segment 2 than by the final memory after the model has processed many later segments.

This changes how we think about recurrent architectures.

A recurrent model no longer has to behave like a single rolling summary. It can behave more like a memory system with multiple historical checkpoints. Some checkpoints may be used often. Others may be skipped. Some may be blended. Some may be selected by a router.

That turns memory from a fixed hidden state into a structured retrieval surface.

This also explains why the next section is about experiments. The architecture story is promising, but the real question is whether these cached memories improve actual model behavior. The paper evaluates that across language modeling, reasoning, synthetic recall, and long-context benchmarks.

The key question becomes:

Does growing compressed memory actually close the gap between recurrent models and Transformers on long-context recall?x

9. Experiments: What the Paper Tests and What It Finds
The previous sections explained the method. Now we can ask the practical question:

Does Memory Caching actually improve recurrent models, or is it only an elegant memory design idea?

The paper answers this through a broad set of experiments. The goal is not only to show that Memory Caching improves perplexity, although that matters. The more important question is whether it improves the specific weakness that motivated the method in the first place: long-context recall.

A fixed-memory recurrent model can be efficient and competitive in many language modeling settings, but it often struggles when the task requires retrieving a precise piece of information from far back in the context. This is exactly where Transformers are strong because attention keeps token-level access to the context.

So the experimental section is designed around one central comparison:

Can a recurrent model with cached memories close the gap with attention-based models on recall-heavy long-context tasks?

The paper evaluates this question across several task families:

language modeling and commonsense reasoning;
Needle-in-a-Haystack retrieval;
in-context retrieval;
LongBench long-context understanding;
Multi-Query Associative Recall.
The important pattern is consistent across these tasks: Memory Caching improves recurrent baselines, especially when the task requires access to information from earlier parts of the sequence.

It does not mean Transformers are suddenly irrelevant. On pure recall tasks, Transformers still remain very strong. But the gap becomes smaller, and in several settings, Memory Caching gives recurrent models a much better long-context profile than their original fixed-memory versions.

Press enter or click to view image in full size

Figure 29. The paper evaluates Memory Caching not only on language modeling, but also on tasks designed to expose whether compressed recurrent memory can retrieve specific information from long contexts.
9.1 Experimental Setup
The paper evaluates Memory Caching at two main scales. The smaller scale uses models with around 760M parameters trained on 30B tokens. The larger scale uses models with around 1.3B parameters trained on 100B tokens.

For language modeling and commonsense reasoning, the default context length is 4K with a segment length of 256 tokens. For the more explicitly long-context tasks, the models are trained with a 16K context window so the differences between short-range and long-range behavior become more visible.

The evaluated model families include:

Transformer-style baselines;
recurrent or subquadratic baselines such as RetNet, DeltaNet, RWKV-style models, and related memory architectures;
SWLA, DLA, and Titans without Memory Caching;
SWLA, DLA, and Titans enhanced with Memory Caching variants.
The Memory Caching variants tested include:

Log-Linear++;
Gated Residual Memory
Memory Soup;
Sparse Selective Caching.
This is an important experimental design because it not only compares Memory Caching against a weak baseline. It compares several recurrent memory models with and without cached memory, and it also compares against stronger attention or hybrid baselines.

The main question is whether the improvement comes from the memory caching idea itself, not simply from using a better base model.

Press enter or click to view image in full size

Figure 30. The experiments test whether adding cached memory checkpoints improves several recurrent memory architectures across both standard language modeling and recall-intensive long-context tasks.
9.2 Language Modeling and Commonsense Reasoning
The first set of experiments is standard language modeling and commonsense reasoning.

This part matters because a long-context memory method should not only improve synthetic recall. It should also preserve or improve general language modeling ability. If a memory mechanism helps with retrieval tasks but damages normal language modeling, it is less attractive as a general architecture component.

The paper reports results across perplexity metrics and downstream tasks such as PIQA, HellaSwag, WinoGrande, ARC, SIQA, and BoolQ.

The main result is that Memory Caching consistently improves the recurrent baselines. SWLA, DLA, and Titans all benefit from the cached-memory variants. The improvement is not always dramatic on every individual task, but the average trend is clear: giving recurrent models access to cached memory states improves their overall performance.

A useful example is the larger 1.3B setting. Titans already perform strongly as a memory-based recurrent architecture, but adding Gated Residual Memory improves the average score further. DLA also improves when enhanced with GRM, Memory Soup, or SSC. The same trend appears at the smaller scale, where Memory Caching improves SWLA, DLA, and Titans relative to their base versions.

The interpretation is straightforward. Even in tasks that are not purely long-context retrieval tests, fixed-memory recurrence creates pressure. The model must keep useful information inside one evolving memory state. Caching intermediate states relaxes that pressure. The model has more historical memory surfaces to consult, which improves both perplexity and downstream task accuracy.

Press enter or click to view image in full size

Table 1. Across language modeling and commonsense reasoning, Memory Caching improves recurrent memory models on average, suggesting that cached memory not only helps synthetic recall tasks.
One important detail is that GRM tends to be one of the strongest variants. This supports the earlier technical intuition: it is not enough to store many cached memories. The model also needs a query-dependent way to decide which memories should matter.

Simple residual aggregation helps, but gated aggregation is usually more adaptive.

9.3 Needle-in-a-Haystack: Testing Direct Long-Context Recall
The Needle-in-a-Haystack benchmark is closer to the core motivation of the paper. In this setup, a specific piece of information is hidden inside a long context, and the model must retrieve it. The paper evaluates three levels of difficulty:

S-NIAH-1: passkey retrieval;
S-NIAH-2: numerical needle retrieval;
S-NIAH-3: UUID-based needle retrieval.
The tests are run at different context lengths: 4K, 8K, and 16K.

This benchmark is useful because it directly measures whether the model can recover a specific detail from a long sequence. It is not enough to understand the general topic. The model must preserve and retrieve a precise item.

The results show the main weakness of fixed-memory recurrent models. For example, the base DLA model performs reasonably well on easier and shorter settings, but its accuracy falls sharply as the context gets longer and the needle becomes more difficult. This is exactly what we would expect from a compressed memory system: as more information is pushed into the same state, precise recall becomes harder.

Memory Caching improves this significantly.

DLA with GRM, Memory Soup, or SSC performs much better than base DLA, especially at longer context lengths. Titans also improve with Memory Caching. Titans is already strong on some of the easier passkey settings, but Memory Caching improves the harder numerical and UUID retrieval settings as well.

This is one of the clearest experimental validations of the method. If the problem is fixed-memory compression, then saving compressed checkpoints should help. The Needle-in-a-Haystack results support that argument.

Press enter or click to view image in full size

Figure 31. Needle-in-a-Haystack tasks expose the weakness of fixed-memory recurrence. Memory Caching helps because the relevant segment remains available as a cached checkpoint.
Press enter or click to view image in full size

Table 2. Memory Caching gives the largest visible gains on recall-heavy long-context tasks, where base recurrent models tend to degrade as context length increases.
The most important takeaway is not that every MC variant beats Transformers everywhere. It does not. The takeaway is that Memory Caching changes the failure mode of recurrent models. Instead of relying only on the final memory state, the model can retrieve from earlier checkpoints. This gives it a much better chance of recovering information that would otherwise be compressed away.

9.4 In-Context Retrieval
The paper then evaluates in-context retrieval tasks. These tasks are different from Needle-in-a-Haystack. They are not only about recovering a single artificial key. They evaluate whether a model can use information provided in the input context to answer questions or retrieve structured information. The paper uses datasets such as SWDE, SQuAD, FDA, TriviaQA, DROP, and Natural Questions.

The important experimental design here is that the input is truncated to different lengths. This allows the paper to test how performance changes as the model receives more context.

For fixed-memory recurrent models, more context can become a problem. Adding more input does not automatically help if the model cannot preserve the relevant information. In some cases, a longer context may even make retrieval harder because more information competes for the same memory capacity.

Memory Caching changes that behavior. By distributing the context over multiple cached memories, the model has more retrieval routes. The relevant piece of context does not need to survive only inside the latest memory state.

The results again show that MC-enhanced DLA and Titans variants usually improve over their base versions. GRM is especially strong because it can weigh memories based on the current query. This is important for in-context retrieval because not all segments are equally relevant. A question about one fact should not be retrieved from every part of the document equally.

Press enter or click to view image in full size

Figure 32. In in-context retrieval, the useful information may be located in one segment while other segments are distractors. Query-dependent gating helps the model retrieve from the relevant cached memory.
Press enter or click to view image in full size

Table 3. In-context retrieval results show that Memory Caching helps recurrent models preserve useful information across longer inputs, especially when retrieval must remain stable as context length increases.
9.5 LongBench: Long-Context Understanding Beyond Synthetic Recall
Synthetic recall tasks are useful because they isolate the memory problem. But a long-context architecture also needs to work on broader long-context understanding.

This is why the paper is evaluated on LongBench.

LongBench contains tasks such as long-document question answering, multi-document reasoning, summarization, few-shot learning, and code-related long-context tasks. These are not all pure retrieval tasks. Some require synthesis, reasoning, or generating a coherent answer from a large input.

This matters because Memory Caching should not only behave like a passkey retriever. It should improve the model’s ability to use longer context in more realistic settings.

The paper reports that MC-enhanced variants improve over their base recurrent models on LongBench. This is consistent with the earlier results: when a task benefits from access to earlier context, cached memories improve the effective memory capacity of recurrent models.

However, LongBench also reminds us that memory capacity is only one part of long-context performance. A model still needs good language modeling, instruction following, reasoning, and generation ability. A stronger memory system helps, but it does not automatically solve every long-context task.

Press enter or click to view image in full size

Figure 33. LongBench evaluates whether Memory Caching helps beyond synthetic recall, including tasks that require reasoning, summarization, and long-context understanding.
9.6 MQAR: Controlled Associative Recall
The final major benchmark is Multi-Query Associative Recall, or MQAR.

MQAR is useful because it directly tests associative memory. The model receives many key-value associations and then has to answer multiple queries. This is close to the core question behind efficient sequence models:

Can the model store many associations and retrieve the right value when queried?

This benchmark is especially difficult for fixed-size memory. If many associations are compressed into one state, they can interfere with each other. The model may remember common patterns, but retrieving the exact value for a specific key becomes hard.

Memory Caching gives the model more capacity by spreading associations across cached memory states. Instead of forcing all associations into one final memory, the model can retrieve from multiple checkpoints.

The paper reports that MC-enhanced models perform well compared with base recurrent models and strong recurrent baselines. This supports the broader claim: Memory Caching improves not only general language modeling, but also associative recall capacity.

Press enter or click to view image in full size

Figure 34. MQAR tests whether a model can store and retrieve many key-value associations. Memory Caching helps by distributing associations across several cached memory states.
Press enter or click to view image in full size

Figure 35. On MQAR, Memory Caching improves associative recall by increasing the number of retrievable memory states rather than forcing all associations into one compressed memory.
9.7 The Overall Experimental Pattern
Across the experiments, the pattern is consistent. Memory Caching improves recurrent models most clearly when the task requires retrieval from a long context.

The improvement appears in synthetic recall, in-context retrieval, and broader long-context understanding. It also improves average language modeling and commonsense reasoning performance, although those gains are usually less visually dramatic than the recall gains.

The results support four conclusions.

First, fixed-size memory is a real bottleneck. Base recurrent models can be efficient, but they often degrade on recall-heavy tasks because the entire past must be compressed into one evolving state.
Second, cached memory checkpoints help. They reduce the pressure on the final memory state by preserving intermediate states that can be retrieved later.
Third, retrieval policy matters. GRM is usually strong because it makes memory use query-dependent. SSC is important because it reduces retrieval cost by selecting only a subset of memories. Memory Soup becomes especially meaningful for deep memory modules, where blending memory states can change the retrieval function itself.
Fourth, Transformers are not fully displaced by these results. On pure token-level recall, attention still has a strong advantage because it keeps detailed token-level access. Memory Caching does not eliminate compression. It distributes compression across segments.
The paper does not prove that the Transformer era is over tomorrow. It shows something more precise: the strict trade-off between fixed recurrent memory and expensive Transformer memory is no longer the only option.

Memory Caching creates a middle ground. It gives recurrent models growing memory, but in a compressed and controllable form. The experiments show that this middle ground is not just theoretical. It improves real models across multiple task families.

The next question is why. Which design choices matter most? Is it the gating? The segmentation? The depth of the memory module? The sparse routing? And how expensive is the method compared with attention?

That is where the ablation and efficiency results become important.

10. Ablations and Efficiency: What Actually Matters
The experimental results show that Memory Caching improves recurrent models, especially on long-context and recall-heavy tasks. But that still leaves a more important question:

Which part of the method is actually responsible for the improvement?

Is it enough to simply store cached memories?
Does the model need gates?
Does the gate need to be context-dependent?
Does Memory Caching still work if the memory module is simpler?
And how expensive is all of this compared with Transformers?
This is where the ablation and efficiency results matter.

Ablations are important because Memory Caching has several moving parts. The method is not just one trick. It combines segmentation, checkpointing, retrieval, gating, and, in some variants, sparse selection. If we only look at final benchmark numbers, we cannot tell which design choices are essential and which ones are optional.

The paper’s ablation study tests this directly.

10.1 The Main Design Choices
By this point, we can summarize Memory Caching as four design decisions:

1. Where do we split the sequence?
2. What memory state do we cache?
3. How do we retrieve from cached memories?
4. How many cached memories do we retrieve from?

The ablation study mostly focuses on the third question: retrieval.

This makes sense because storing memory checkpoints alone does not solve the problem. The model also needs to know how to use those checkpoints.

A bad retrieval policy can turn cached memory into noise. If the model always retrieves equally from all previous segments, irrelevant memories can interfere with the current prediction. If the model uses only positional information, it may select old memories because of where they are, not because of what they contain. If the memory module is too weak, each cached checkpoint may not preserve enough useful information.

So the ablation question is not simply:

Does more memory help?

It is more precise:

What kind of memory retrieval makes cached memory useful?

Press enter or click to view image in full size

Figure 36. The ablation study asks which parts of Memory Caching are responsible for the gains: context-dependent retrieval, gating, memory expressivity, and separate retrieval projections.
10.2 Context-Dependent Retrieval Matters
One of the most important ablations removes context-dependence from the gate.

In the full Gated Residual Memory setup, the gate is not only a function of the current token. It also depends on a representation of the cached segment. This means the model can ask:

Is this cached segment relevant to the current query?

That is different from asking only:

Should I use memory from this relative position?

The difference is subtle but important. A position-only gate can learn that recent segments are usually useful, or that earlier segments should sometimes be suppressed. But it cannot directly compare the query with the content of a segment. This makes it weaker for retrieval tasks where the relevant information may appear anywhere.

For example, suppose the answer is in Segment 2. A position-only gate may not know that Segment 2 matters unless it has learned a strong positional pattern. But if the gate compares the current query with the segment representation, it can assign a higher weight to Segment 2 because its content is related to the query.

That is why context-dependent gates are central to the method.

They turn Memory Caching from a passive archive into a query-conditioned memory system.

The ablation results support this. When the context-dependent part is removed, retrieval accuracy drops significantly. Language modeling and commonsense reasoning are affected as well, but the retrieval drop is the clearest signal. This matches the intuition: context-dependent gating matters most when the model must recover specific information from a long context.

Press enter or click to view image in full size

Figure 37. Context-dependent gating lets the model compare the current query with cached segment representations, making retrieval content-aware rather than only position-aware.
10.3 Gating Matters, But Even Residual Memory Helps
The second major ablation removes gating. Without gates, the model falls back toward the simpler Residual Memory variant. It can still retrieve from cached memories, but it treats them more uniformly.

This is a weaker mechanism, but it is still better than having no cached memory at all.

That is an important point.

The results suggest that even simple residual access to older memory states can help recurrent models. This means that the core idea of preserving earlier memory checkpoints has value by itself. Earlier memory states contain information that may no longer be cleanly available in the final memory state.

However, the best results come when the model can weigh those memories.

This gives us a useful hierarchy:

No cached memory
→ only the final recurrent state is available

Residual Memory
→ cached states are available, but retrieval is mostly uniform

Gated Residual Memory
→ cached states are available and query-dependent

Sparse Selective Caching
→ cached states are available, query-dependent, and retrieval is sparse
The jump from no cache to residual memory shows that checkpointing helps. The jump from residual memory to gated memory shows that retrieval policy matters.

Press enter or click to view image in full size

Figure 38. The ablations suggest that cached checkpoints help on their own, but learned gates make the cached memories more useful by controlling their contribution to each query.
10.4 Memory Expressivity Still Matters
Another ablation replaces the richer memory module with a linear memory module.

This connects back to the earlier section on linear versus deep memory. A linear memory state can be efficient and useful, but it is a simpler object. A deep memory module can represent more complex transformations and can behave more like an adapted retrieval function.

The ablation results show that Memory Caching remains useful even with simpler memory. This is important because it means the method is not completely dependent on a highly expressive neural memory module.

However, the stronger versions still benefit from expressive memory.

This makes sense. Memory Caching gives the model more memory slots, but each slot still has to store useful information. If each cached memory is weak, then adding more of them helps only up to a point. If each cached memory is expressive, then each checkpoint can preserve a richer representation of its segment.

So there are two levels of memory capacity:

Number of cached memories
×
Expressivity of each memory
Memory Caching increases the first term. Deep memory improves the second. The best systems likely need both.

Press enter or click to view image in full size

Figure 39. Memory capacity depends both on how many cached memories exist and how expressive each cached memory is. Memory Caching increases the number of retrievable states; deep memory increases the richness of each state.
10.5 Separate Retrieval Projections Matter
The paper also tests a design choice involving shared versus separate projections for retrieval.

The practical interpretation is that the model needs enough separation between the representation used to ask the query and the representation used to connect the query to cached segments. If those signals are shared too aggressively, the retrieval mechanism can become unstable or collapse.

This is a common theme in memory systems. Storage and retrieval are related, but they are not always the same operation.

A model may need one representation for the token’s local computation and another representation for deciding which memory state is relevant. If the same projection is forced to do both jobs, the system may lose flexibility.

In the ablation table, the shared-projection variant fails badly. This suggests that the retrieval interface is not a minor implementation detail. It is part of the memory system.

The cached memories are only useful if the model can address them properly.

Press enter or click to view image in full size

Figure 40. Memory retrieval benefits from separating the representation used to read from memory from the representation used to decide which cached memory is relevant.
10.6 Efficiency: The Other Half of the Paper
Accuracy is only half of the story. The reason this paper matters is not simply that Memory Caching improves recurrent models. It matters because it tries to improve memory capacity without fully returning to Transformer-style quadratic attention.

A Transformer keeps token-level memory. This is powerful, but expensive. As context length grows, attention must manage interactions over many previous tokens, and inference incurs the cost of a growing KV cache.

A standard recurrent model has the opposite profile. It uses a fixed-size memory state so that it can be much cheaper at long sequence lengths. But the cost is compression. All past information has to fit into one state.

Memory Caching creates an intermediate point.

It does not store every token. It stores one compressed memory per segment. If the sequence has length L and each segment has a length S, then the number of cached memories is roughly:

N = L / S
The model retrieves from cached memories rather than from every previous token.

This creates a controllable memory-compute trade-off.

With larger segments, the model stores fewer memories and is more efficient, but each memory is more compressed. With smaller segments, the model stores more memories and has better memory resolution, but retrieval becomes more expensive.

Sparse Selective Caching makes this even more practical because it stores many memories but retrieves from only a subset.

Store many checkpoints.
Retrieve only the most relevant ones.

This is why SSC is important. It separates storage capacity from active retrieval cost.

Press enter or click to view image in full size

Figure 41. Memory Caching aims for the middle of the cost–recall trade-off: more recall capacity than fixed-memory recurrence, but lower cost than full token-level attention. Sparse Selective Caching improves this trade-off by retrieving only a small subset of cached memories.
10.7 Training Throughput
The paper’s efficiency result compares training throughput across baselines and Memory Caching variants.

The high-level result is that MC variants provide a middle ground between Transformers and standard recurrent models. They are not as cheap as the simplest recurrent baseline because they add cached memory retrieval. But they become much more efficient than Transformers as context length grows.

This is especially important for long-context training and inference.

At short context lengths, the difference may not look decisive because modern attention implementations are highly optimized. But as the sequence length grows, the quadratic behavior of full attention becomes harder to avoid. Recurrent models remain attractive because their state update can scale more gently.

Memory Caching adds some cost, but the cost is controlled by the number of cached memories and the retrieval strategy.

SSC is especially important in this comparison. Since it selects only the top-k relevant cached memories, it can keep the retrieval overhead small while preserving much of the accuracy benefit.

This is the practical appeal:

GRM: strong accuracy, reads broadly from cached memories
SSC: strong accuracy, retrieves sparsely, better efficiency profile

So if GRM is the most straightforward high-performing variant, SSC is the variant that looks most production-relevant for very long contexts.

Press enter or click to view image in full size

Figure 42. Training throughput shows the practical motivation for Memory Caching: it improves long-context recall while remaining more efficient than full attention at longer sequence lengths.
10.8 Why SSC Is Especially Important
Sparse Selective Caching is not only another variant. It may be the most important variant for scaling.

GRM reads from all cached memories and weights them. This is simple and effective, but the retrieval cost grows as the number of cached segments grows. For a moderate context length, that may be acceptable. For extremely long contexts, it becomes less attractive.

SSC changes the scaling behavior.

Instead of asking every cached memory to contribute, the model first routes the query to a small set of relevant memories. Only those selected memories are retrieved.

This gives the model two memory layers:

large passive memory pool
+
small active retrieval set

That is a common pattern in scalable systems. Search engines do not score every document with the most expensive ranking model. Databases do not scan every row when an index can narrow the candidate set. Retrieval systems often use a cheap first-stage retriever and a more expensive second-stage reranker.

SSC applies a similar idea inside the model architecture.

The model can keep many cached memory checkpoints, but only activate the few that appear relevant to the current query.

This makes SSC a strong candidate for very long-context recurrent models because it avoids the main weakness of dense cached-memory retrieval: reading too much memory for every token.

Press enter or click to view image in full size

Figure 43. Sparse Selective Caching keeps many cached memories available but retrieves from only a small selected subset, making Memory Caching more scalable.
10.9 The Practical Lesson from the Ablations
The ablation and efficiency results point to a clear practical lesson:

Memory Caching works because it combines more memory capacity with better memory addressing.

Caching alone gives the model more historical states. But the strongest results come when the model can decide which states matter for the current query.

That means the design space is not only about storing more information. It is about building a better interface between the current token and the compressed past.

The paper’s ablations suggest that several ingredients matter:

cached checkpoints preserve earlier memory states;
gates make retrieval query-dependent;
context-dependent gates make retrieval content-aware;
expressive memory modules make each checkpoint more useful;
sparse routing keeps retrieval scalable.
This also explains why Memory Caching is more than a simple “add more hidden states” trick. It is closer to a memory architecture pattern.

The model writes compressed memories over time. Later, it reads from them selectively. The quality of the system depends on both sides:

write path: what gets stored?
read path: how is it retrieved?

A standard recurrent model has a write path but only one active memory state. A Transformer has token-level read access but pays a high cost. Memory Caching tries to define an intermediate system: compressed writes, growing memory, and selective reads.

This is the main reason the paper is worth paying attention to.

It does not just say “RNNs need more memory.” That has been obvious for a long time. It proposes a concrete way to give recurrent models more memory while keeping the memory budget tunable.

The next section can now step back from the experiments and explain what the paper is really saying about the future of sequence models.

11. What This Paper Is Really Saying
After going through the method, variants, architectures, experiments, ablations, and efficiency results, we can now step back and ask the larger question:

What is this paper really saying about the future of sequence models?

The tempting interpretation is to say:

RNNs are back.

But that is too simple.

A better interpretation is:

The old memory trade-off is changing.

For years, sequence modeling has been framed around a fairly clean contrast.

RNNs compress the past into a fixed-size hidden state. This makes them efficient, but it limits their ability to preserve exact information over long contexts.

Transformers keep access to previous tokens through attention. This gives them strong recall and flexible in-context learning, but it becomes expensive as the context grows.

Memory Caching challenges this binary framing. It says that recurrent models do not need to be restricted to one final hidden state. They can keep their efficient recurrent update rule while also allowing memory to grow over time through cached checkpoints.

This does not make them identical to Transformers. The cached memories are still compressed. The model is not storing every token at full resolution. But it does create a middle ground that is much more flexible than the usual RNN-versus-Transformer comparison.

The paper is not only introducing a method. It is pointing toward a different way to think about architecture design:

Memory is not a fixed property of an architecture.
Memory is a design space.

11.1 The Transformer Advantage Was Always a Memory Advantage
The Transformer did not become dominant only because it removed recurrence. That was part of the story, especially for training. Compared with classic RNNs, Transformers are easier to parallelize across the sequence. This made them much better suited for large-scale training on modern accelerators.

But for long-context reasoning and retrieval, the deeper advantage is memory.

Self-attention gives each token a way to interact with earlier tokens. The model does not have to compress the entire past into one state before using it. It can retrieve information from token-level representations.

This is extremely powerful.

If a name appears 4,000 tokens earlier, a Transformer can, in principle, attend back to that token. If an instruction appears near the beginning of a prompt, the model can keep it accessible. If a document contains many key-value associations, attention gives the model a mechanism to look back at the relevant keys.

This is why attention behaves like a growing memory system.

As the context length grows, the memory surface grows with it. More tokens means more stored key-value representations. More stored representations means more opportunities for retrieval.

But the same property creates the cost problem.

The Transformer does not get growing memory for free. Full attention requires interactions across tokens, and long-context inference requires maintaining a growing KV cache.

That cost is manageable for many practical context lengths, but it becomes a major bottleneck as context windows move from thousands of tokens to hundreds of thousands or millions.

So the key question becomes:

Can we keep some of the benefits of growing memory without storing and retrieving at full token-level resolution?

Memory Caching answers yes, at least partially.

Press enter or click to view image in full size

Figure 44. The Transformer’s long-context strength comes from growing token-level memory: every previous token can remain directly accessible, but this creates high memory and compute cost at long context lengths.
11.2 Recurrent Models Were Not Wrong; Their Memory Was Too Compressed
The paper also reframes recurrent models. A simple way to criticize RNNs is to say that they forgot too much. That criticism is often correct, but it hides the reason why they remain attractive.

Recurrent models have a very useful property: they process sequences through a compact state. This gives them a natural efficiency advantage. They do not need to keep all previous token representations active in the same way full attention does.

The problem is not recurrence itself. The problem is forcing the entire past through one memory bottleneck.

A standard recurrent model behaves like this:

long sequence → repeated updates → one final memory state

This is efficient, but it creates pressure. Every new token can modify the memory. Useful information from earlier parts of the context may be overwritten, diluted, or entangled with later information.

This is especially damaging for recall-heavy tasks.

If the model only needs the general topic of a document, compression may be enough. But if it needs a specific number, key, UUID, name, instruction, or fact from far back in the context, a single compressed state becomes fragile.

Memory Caching changes the recurrent model’s memory interface:

long sequence → segment memories → cached checkpoints → selective retrieval

The model still compresses. But it no longer compresses the whole past into only one state.

This is the important shift.

Memory Caching does not reject recurrence. It tries to fix the part of recurrence that made it weak on long-context recall.

Press enter or click to view image in full size

Figure 45. Memory Caching preserves the efficiency intuition of recurrence while reducing the pressure on one final hidden state.
11.3 The Paper Is Not Saying Attention Is Dead
This paper does not prove that Transformers are obsolete. Transformers still have a strong advantage when token-level recall is essential. They store context at high resolution. Memory Caching stores compressed checkpoints. That means some information is still lost inside each segment.

A cached memory checkpoint is not the same as a full list of tokens.

If a segment contains many unrelated facts, the memory module still has to compress them. If the segment is too large, details may be lost. If the retrieval gate selects the wrong memory, the model may still fail. If the memory module is not expressive enough, the checkpoint may not preserve what the query needs.

So the right conclusion is not:

Transformers are over.

The better conclusion is:

Full attention is no longer the only credible path to growing memory.

That is a much stronger and more useful claim.

The paper shows that recurrent architectures can be upgraded with a memory system that grows with sequence length. It does not fully match the Transformer’s token-level memory in every setting, but it narrows the gap in important long-context tasks while keeping a more controllable cost profile.

This makes the future less likely to be “Transformer versus RNN” and more likely to be a mixture:

attention where exact token-level recall is needed
+
recurrence where efficient compression is useful
+
cached memory where long-range access is needed
+
routing where not all memory should be read every time

Press enter or click to view image in full size

Figure 46. The paper does not replace attention with recurrence. It expands the design space between fixed-memory recurrence and full token-level attention.
11.4 Memory Capacity Becomes a Dial
One of the most useful ideas in the paper is that memory capacity can be controlled. In a standard RNN, the memory capacity is mostly fixed by the hidden state size and architecture. Increasing the sequence length does not increase the number of memory states available at retrieval time.

In a Transformer, the memory capacity grows with every token. This gives strong recall, but the cost grows as well. Memory Caching gives a more tunable option.

The memory capacity depends on how the sequence is segmented and how many cached memories are retrieved. If the model uses large segments, it saves fewer checkpoints:

lower cost
more compression per checkpoint
weaker recall resolution
If the model uses smaller segments, it saves more checkpoints:

higher cost
less compression per checkpoint
better recall resolution
If it uses Sparse Selective Caching, it can store many checkpoints but retrieve only a small subset:

large passive memory
small active retrieval set
This is why Memory Caching is better understood as a memory capacity dial.

The dial can be turned depending on the task.

For short-context language modeling, a small number of cached memories may be enough. For retrieval-heavy long-context tasks, more memory resolution may be needed. For ultra-long sequences, sparse routing becomes more important because dense retrieval over all cached memories becomes too expensive.

This turns memory into a configurable resource rather than a fixed architectural assumption.

Press enter or click to view image in full size

Figure 47. Memory Caching turns memory capacity into a tunable system parameter: segment size, number of checkpoints, and retrieval policy control the trade-off between cost and recall.
11.5 The Real Innovation Is the Read Interface
It is easy to focus on the caching part of Memory Caching. But caching alone is not enough.

A large archive is only useful if the model can retrieve from it correctly.

This is why the retrieval interface matters so much. The paper’s variants can be understood as different answers to one question:

When a query arrives, how should it read from past memory states?
Residual Memory gives the simplest answer:

read from all cached memories and add the outputs
Gated Residual Memory gives a better answer:

read from all cached memories, but weight them differently
Memory Soup gives a different answer:

blend memory modules first, then read from the blended memory
Sparse Selective Caching gives a scalable answer:

select the most relevant cached memories, then retrieve only from them
This means Memory Caching is not just about increasing storage. It is about building a better read path over compressed history.

This distinction matters because many long-context systems fail not because they have no memory, but because their memory is poorly addressed.

A model can store many things and still retrieve the wrong one. It can retrieve too broadly and introduce noise. It can retrieve too narrowly and miss the relevant segment. It can rely too much on recency and ignore older information.

The strongest Memory Caching variants treat retrieval as query-dependent. The current token does not just read from “the past.” It reads from memory states that are weighted, selected, or blended according to the current context.

That is the key architectural lesson.

The future of sequence models may depend as much on memory addressing as on memory storage.

Press enter or click to view image in full size

Figure 48. Memory Caching separates the write path from the read interface. The model writes compressed checkpoints over time, then uses a retrieval policy to decide how the current query should access them.
11.6 This Is Not Classic RNN Revival
There is another mistake to avoid. Memory Caching should not be presented as a return to 1990s-style RNNs.

The method uses recurrence, but the architectural context is different. Modern recurrent and linear-attention models are not simply Elman RNNs with larger hidden states. They include better update rules, better training infrastructure, deeper memory modules, gating, routing, and sometimes hybrid attention components.

The paper applies Memory Caching to architectures such as Linear Attention, Sliding Window Linear Attention, Deep Linear Attention, and Titans-style neural memory. These are part of a broader wave of efficient sequence models trying to keep some benefits of recurrence while avoiding the weaknesses that made classic RNNs lose ground to Transformers.

So the real story is not:

Old RNNs are back.

It is:

Modern recurrent memory is becoming more structured.

Classic RNN memory was mostly a single evolving hidden state. Modern memory architectures are becoming closer to systems with:

write rules;
stored checkpoints;
segment summaries;
learned gates;
sparse routers;
deep memory modules;
retrieval policies.
That looks less like a simple recurrent loop and more like a small memory system inside the model.

This is why Memory Caching fits the current direction of long-context research. The field is moving away from the idea that context handling must be either full attention or fixed recurrence. Instead, researchers are exploring memory compression, retrieval, routing, recurrence, local attention, global tokens, state-space models, and hybrid designs.

Memory Caching is one clear point in that design space.

Press enter or click to view image in full size

Figure 49. Memory Caching is not simply a return to classic RNNs. It is part of a shift toward structured memory systems inside sequence models.
11.7 What Would It Mean to “End the Transformer Era”?
The phrase “end the Transformer era” needs careful handling. The Transformer era does not necessarily end when one architecture beats Transformers on every benchmark. That is unlikely to happen suddenly.

A more realistic ending would look different.

The Transformer era ends when the field stops assuming that full attention is the default answer for every sequence modeling problem. That shift may happen gradually.

For some workloads, full attention may remain the right choice. For others, hybrid architectures may be better. For very long contexts, models may combine local attention, recurrent memory, sparse retrieval, and cached states. For streaming data, recurrence and checkpointed state may be more natural. For memory-constrained inference, compressed memory may be necessary.

So the end of the Transformer era may not mean:

Transformers disappear.
It may mean:

Transformers become one component in a broader architecture toolbox.
That is already how many architecture transitions happen. Older ideas rarely disappear completely. They get absorbed, modified, and used where they make sense.

Convolutions did not disappear after Vision Transformers. Recurrence did not disappear after attention. Retrieval did not disappear after larger context windows. Instead, the field keeps recombining useful mechanisms.

Memory Caching is another recombination.

It takes recurrence, adds growing memory through checkpoints, and uses attention-like retrieval ideas through gating and routing. The result is neither a classic RNN nor a standard Transformer. It is a hybrid memory design.

This is why the paper is interesting.

It does not need to kill Transformers to matter. It only needs to show that some of the reasons we depended on Transformers can be achieved differently.

Press enter or click to view image in full size

Figure 49. Future long-context models may not be pure Transformers or pure RNNs. They may combine local attention, recurrent updates, cached memory checkpoints, and routing into one memory stack.
11.8 The Main Conceptual Shift
The main conceptual shift is this:

Long-context modeling is not only about increasing context length.
It is about deciding what kind of memory the model should have.

A larger context window is useful, but it does not answer all memory questions.

Should the model store every token?
Should it compress segments?
Should it keep summaries?
Should it retrieve sparsely?
Should older context be lower resolution?
Should memory be updated online?
Should memory be neural and trainable during inference?
Should retrieval depend on the query, position, or segment content?
These are architecture decisions. Memory Caching makes those decisions explicit. It shows that a sequence model can have multiple memory resolutions:

current online memory for recent processing;
cached compressed memories for earlier segments;
sparse routing to select relevant history;
deep memory modules for more expressive segment storage.
This is closer to how engineered memory systems are usually designed. We rarely use only one memory layer. Computer systems use registers, cache, RAM, disk, indexes, and databases. Search systems use candidate retrieval and ranking. Human memory is also not a flat list of every experience at equal resolution.

Modern AI architectures may move in the same direction: not one memory mechanism, but a hierarchy of memory mechanisms.

Memory Caching is an early version of that hierarchy inside recurrent sequence models.

Press enter or click to view image in full size

Figure 50. Memory Caching suggests that long-context models may evolve toward memory hierarchies rather than one flat attention mechanism or one fixed recurrent state.
11.9 The Bottom Line
The paper’s real message is not that Transformers are finished.

It is that the reason Transformers won — growing memory — can be rethought.

For a long time, the practical choice looked like this:

Use recurrence if you want efficiency.
Use attention if you want recall.

Memory Caching proposes a third option:

Use recurrence for efficient updates.
Cache memory checkpoints for growing capacity.
Retrieve selectively for long-range recall.

This third option is not perfect. It still compresses. It still depends on segment size. It still needs good retrieval policies. It still has to prove itself at larger scales and in real production workloads.

But it changes the architecture conversation.

The important question is no longer:

Will RNNs beat Transformers?

The better question is:

What memory structure should a model use for a given context length, task, and compute budget?

That is the direction this paper points toward.

If the Transformer era ends, it will probably not end with one replacement architecture. It will end with a broader realization: full attention is one memory design, not the only memory design.

Memory Caching is one of the clearest examples of that shift.

Conclusion
The main contribution of Memory Caching is not simply that it improves recurrent models on several benchmarks. The deeper contribution is that it reframes the long-context architecture problem as a memory design problem.

For years, the trade-off looked straightforward. Recurrent models were efficient, but their memory was fixed. Transformers had strong recall, but their memory grew with context length and became expensive. Memory Caching challenges this binary view. It shows that a recurrent model does not need to keep only one final hidden state. It can preserve intermediate memory states, organize them as cached checkpoints, and retrieve from them later.

This creates a more flexible design space. A model can use recurrence for efficient sequence processing, cache compressed checkpoints for long-range access, and use gates or routers to decide which memories matter for the current query. Sparse Selective Caching pushes this further by separating the passive memory pool from the active retrieval set: many memories can be stored, but only a small subset needs to be read.

The experiments support the core idea. Memory Caching improves recurrent baselines across language modeling, long-context understanding, and recall-heavy benchmarks. The improvements are especially meaningful on tasks where standard recurrent models struggle because too much information has to be compressed into one state. At the same time, the results should be interpreted carefully. Transformers still remain very strong, especially when token-level recall is required. A cached memory checkpoint is still compressed; it is not the same as storing every previous token.

That is why the strongest interpretation is not “RNNs are back” or “Transformers are finished.” The stronger and more accurate interpretation is that sequence model memory is becoming more modular. The future may not be a pure Transformer stack or a pure recurrent stack. It may be a hybrid memory architecture: local attention for short-range precision, recurrent memory for efficient updates, cached checkpoints for long-range compressed recall, and routers or gates for query-dependent retrieval.

If the Transformer era ends, it may not end through one sudden replacement architecture. It may end because full attention stops being the default answer to every long-context problem. Memory Caching is one of the clearest signs of that shift. It turns memory capacity from a fixed architectural property into a controllable system parameter: what to compress, what to cache, what to retrieve, and how much compute to spend.

The practical lesson is simple. Long-context modeling is not only about making the context window larger. It is about designing the memory system behind that context. Memory Caching gives us one possible blueprint for doing that.