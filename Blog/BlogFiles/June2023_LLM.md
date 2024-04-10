# Introductory (Hypeless) Readings in Large Language Models

June 2023, Tags:  LLM, Introductory Reading

<img src="./Blog/BlogImg/June2023_LLM2.webp" style="width:70%; object-fit: contain;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em">Image from Midjourney with prompt "tangled guardrails protecting us from Large Language Models in an imaginary city, oil painting in the style of MC Escher --tile --test --creative --upbeta"</p>


Large Language Models (LLMs) have swiftly made their mark in diverse fields, from education to industry and politics. Yet, despite their popularity, LLMs retain an intriguing air of mysticism even within academic circles. In this exploration, we delve into a selection of research publications that have propelled LLMs to the forefront, shedding light on some technical intricacies and uncovering common threads.

Discussions of LLMs seem to have a high risk of becoming a disjointed collection of pedantic rants (For instance, the merits of rotary versus relative positional encoding alone can consume [an engaging blog post](https://blog.eleuther.ai/rotary-embeddings/)). Therefore, it is prudent to occasionally sidestep these technical aspects to instead focus on *meta* questions: What do the chosen evaluation metrics reveal about the research community's priorities? Can we glean insights into future research directions?

On a related note, despite being an introductory readings post, this is not a technical beginner how to on LLMs. For an excellent primer on transformers and self / multi-head attention see Sebastian Raschka
excellent [youtube series](https://www.youtube.com/watch?v=0PjHri8tc1c)  

## Rapid Fire: one eventful year 

The academic paper that was the precursor to ChatGPT, ([instructGPT](https://arxiv.org/pdf/2203.02155)) was published March of 22. Within six months [self-instruct](https://arxiv.org/abs/2212.10560) adapted the approach to become virtually self-supervised. A few months later researchers from meta trained [LLaMa](https://arxiv.org/abs/2302.13971) an LLM usig 5TB of exclusively open-source datasets (training lasted only 21 days). Two weeks after LLaMa made their model weights available, Stanford researchers combined the approaches from the previously mentioned papers to create [alpaca AI](https://crfm.stanford.edu/2023/03/13/alpaca.html), which achieved ChatGPT-3 like performance, using publically available data and commercial hardware. One week later, Berkley researchers released [Vicuna](https://lmsys.org/blog/2023-03-30-vicuna/) which was fine-tuned for a total cost of 300$ on user-shared conversations with ChatGPT. Anecdotally,  I found Vicuna's performance to be indistinguishable from that of ChatGPT. In summary, within 3 weeks of the first open source model weights becoming available (and a year since the first ChatGPT paper was published) open source models have been able to close the gap to their commercial counter-parts. Considering a recent [leaked document from Google](https://www.semianalysis.com/p/google-we-have-no-moat-and-neither), it is entirely plausible that we are heading towards a future in which state-of-the-art LLMs optimized to promote specific products or ideas are widely available and in use by even small-scale institutions (and individuals with some level of know-how). Considering this burgeoning ubiquitousness of LLMs, it is of upmost importance we begin to try to understand this technology, how it is trained, and what are their weaknesses.  

### Trained to follow: *InstructGPT* and *self-instruct*

Published by the openAI team, [instructGPT](https://arxiv.org/pdf/2203.02155) fine-tuned the GPT-3 model to follow instructions. Most importantly, the 1.3B parameter instruction tuned model output was preferred over the 175B parameter version of GPT-3 85% of the time. Strangely, despite significant performance improvements, and being a precursor to the popular ChatGPT  model, the actual [instructGPT](https://arxiv.org/pdf/2203.02155) somewhat lacking on technical advancements.

The pipeline used for tuning the model can generally be divided into three stages. First the researchers use an instructions prompt dataset collected from user interactions with the GPT-3 model. Ground truth answers to these prompts are collected from labelers, and a the data is used to fine-tune the GPT-3 model to adequately follow instructions. In the second step the fine-tuned model is used to generate multiple outputs given a prompt, and - using labeler ranking - a reinforcement model is trained to reward better prompt answers. Finally, using proximal policy optimization the reinforcement model is used to further fine tune the conversational model (PPO model).

<img src="./Blog/BlogFiles/June2023_LLM/instructGPT.png" style="width:70%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em">Taken from <a href="https://arxiv.org/pdf/2203.02155">instructGPT</a>. Comparison between the baseline GPT-3 model, the baseline when prompted to follow instructions, the instruction fine-tuned model (SFT), and model further tuned with PPO and with pretrained gradiant mixing (ptx).</p>

Considering this training pipeline, a few  observations become apparent. First and foremost, the reinforcement model has no access to "ground truth" information. Therefore, the observed performance improvements between chatGPT and GPT-3 are likely due to stylistic alignment with user expectations rather than changes in the content of the answers.  This is further supported by an increase in hallucinations between the fine-tuned (SFT) and PPO-tuned model.  The overall drop in hallucination between the baseline GPT-3 and all other models can perhaps be attributed to the addition of the instruction "if you do not know, say *I don't know*" to the prompts. This is further inforced when breaking down answers to either truthful or truthful and informative.

<img src="./Blog/BlogFiles/June2023_LLM/instructGPT_truthfulness.png" style="width:30%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em;">Taken from <a href="https://arxiv.org/pdf/2203.02155">instructGPT</a>. Grey bars indicate truthfulness, colored bard indicate truthfulness and informativeness (percentage). Tuned models were more truthful, but not neceserarly more informative.</p>

In conclusion, while ChatGPT achieved widespread notoriety and propelled openAI into a house-hold name status. The main improvements upon GPT-3 were mostly achieved by **fine-tuning for stylistic alignment rather than the core technology** behind these models. This is especially interesting considering [self-instruct](https://arxiv.org/abs/2212.10560) achieved similar performance despite doing away with all stages in the instructGPT pipeline except for the very first.

Instead of using human labeling to train a reinforcement model, [self-instruct](https://arxiv.org/abs/2212.10560) simply extended the supervised training stage with a lot more data. Surprisingly however, this data was collected by prompting the LLM itself rather than using human input. The researchers used seed data of 175 instructions and answers, the LLM was then prompted to generate similar instructions, discarding any that were too similar to ones that were previously collected, until they had a total of ~52K instructions and answers. When exploring a small sample set, the authors reported that only 54% of the generated instruction answer pairs described a valid task with valid and acceptable input.   

### Open Sourcing LLMs

Employing similar pipelines to those discussed above, Meta AI trained  another LLM called [LLaMa](https://arxiv.org/abs/2302.13971). Both the model weights and training data were made available to the community. Allowing for close and systematic evaluation. Indeed, the paper includes evaluations on over 25 data-sets.

One clear result is that despite LLMs generalizing well to new tasks and context, having similar training and testing data is crucial; [Minreva](https://openreview.net/pdf?id=IFXTZERXdM7), an extension of PaLM that was tuned on Arxiv and Math Web Pages, outperformed [LLaMa](https://arxiv.org/abs/2302.13971) on middle and highschool math problems. However, LLaMa which was trained on Github (4.5% of total training data) and Stack Exchange (2%) outperformed all other models on coding benchmarks.

The [LLaMa](https://arxiv.org/abs/2302.13971)  also included toxicity and carbon footprint evaluations. While the authors did not discuss in depth how to elevate concerns relating to these benchmarks, normalizing the reporting of these results is a step in the right direction.

In total the authors estimate that training the LLaMa model released 1015 tons of CO<sub>2</sub> almost 150 times the 6.8 tons average annual emission of an EU citizen. This puts the current LLM arms race into an interesting perspective. As always, progress comes at a price. However, in the case of LLaMa, the availability of the weights enabled further extensions of the model. Allowing the research community to make further use of the the trained model.

Two week after the the weights of LLaMA were released, researchers from stanford released Alpaca, a model that used  [self-instruct](https://arxiv.org/abs/2212.10560) to fine tune the weights. A week after that Vicuna which fine tuned the weights further was released. A [leaked document from Google](https://www.semianalysis.com/p/google-we-have-no-moat-and-neither) seems to suggest that these open source model are viewed as serious competitors of both Google and OpenAI models. Indeed, with the recent release of the Guanaco model (fined tuned Alpaca LLM, the fine tuning QLoRA method requires only 24 hours training on a single 48GB GPU) it seems as if open source LLMs are now indistinguishable from chatGPT performance wise. 

<img src="./Blog/BlogFiles/June2023_LLM/openLLMs.png" style="width:70%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em;">From a leaked Google document titled "we have no moat, and neither does OpenAI".</p>

## What are the limits of LLMs?

Examining the functional capacities of LLMs, it proves valuable to set aside abstract contemplations surrounding consciousness and focus on a practical assessment. Notably, LLMs, as a whole, [struggle with even basic math and logic questions](https://arxiv.org/abs/2302.13971), things like apparent reasoning  capabilities are called into question. Recently researchers have began conducting [these kinds of evaluations](https://arxiv.org/pdf/2306.05836.pdf). Results seem to suggest that LLMs are only capable of correlation, and "reasoning" does not generalize even when simply perturbing variable names to perform out-of-distribution tasks.

As tempting as it might be to go down the rabbit-hole of Humean definition of causality, and argue that human reasoning is also mere correlation. The fact remains that these results show a clear shortcoming of LLMs. Some researchers have argued that [emergent abilities](https://github.com/Mooler0410/LLMsPracticalGuid) such as specific types of reasoning can suddenly appear once the model complexity reaches a certain level. However, formalizing a research programme based on these theories is difficult, and accepting these limitations remains the most pragmatic moves. 

Overall, LLMs seem to be highly capable of performing many common tasks (today, an LLM assists most of my writing). Personally, I find a responsive free copy-editor more useful than a hypothetical 

## Where do we go from here?

Lilian Weng, the chief safety officer at openAI wrote an [incredible blog post](https://lilianweng.github.io/posts/2023-06-23-agent/) that echos many arguments being made by researchers trying to put [practical guidelines for making the most out of LLM](https://github.com/Mooler0410/LLMsPracticalGuide). 

First and foremost, it has been observed that certain approaches that require Language Model Models (LLMs) to explicitly state their internal reasoning and processes tend to enhance performance. This can be achieved through methods such as [ReAct](https://arxiv.org/abs/2210.03629), [Reflexion](https://arxiv.org/abs/2303.11366), and [Chain of Hindsight](https://arxiv.org/abs/2302.02676), where the LLM articulates its reasoning after completing each sub-task within a complex goal. Alternatively, approaches like [Chain of Thought](https://arxiv.org/abs/2201.11903) and [Tree of Thought](https://arxiv.org/abs/2305.10601) involve the LLM expressing its initial plan at the outset.

Secondly, while LLMs are undeniably powerful tools, their true potential shines brightest in scenarios characterized by limited data, noise, and tasks that lack well-defined formalization. However, in other circumstances, specialized deep learning models trained for specific and narrow tasks continue to outperform LLMs.

<img src="./Blog/BlogFiles/June2023_LLM/decisionTree.png" style="width:70%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em;">When should one use LLMs? From  <a href="https://github.com/Mooler0410/LLMsPracticalGuide">(Yang et al 2023).</a></p>

One interesting option is combining the best of both worlds by defining the specialized models as tools that the LLM can invoke when necessary. [HuggingGPT](https://huggingface.co/spaces/microsoft/HuggingGPT) and [Gorrilla](https://gorilla.cs.berkeley.edu/) demonstrate how useful these tools can be for completing complex multi-modal tasks. Access to search engines can help alleviate  
  
<img src="./Blog/BlogFiles/June2023_LLM/HuggingGPT.png" style="width:70%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em;"> Integrating the 53 most popular huggingFace APIs into ChatGPT. From  <a href="https://huggingface.co/spaces/microsoft/HuggingGPT">HuggingGPT</a>.</p>

## Final Musings
### Panik?  <img src="./assets/panik.png" style="height:2em;display:inline"></img>

I find SciFi novels often candidly reflect many aspects the author (and his society's) psyche. Paradoxically, this makes SciFi have a very narrow imagination when it comes to potential misuses of technology. Afterall, the world of publishing remains exlusive and political, and while media attention focuses on SkyNet and singularity scenarios, the real impact of LLMs will probably be first felt by smaller communities online and in the global south. For instance, fake news have plagued gubernatorial elections in Indonesia since at least 2014, and have arguably profoundly influenced politics in the 4th most populated country in the world. Nowadays anyone with a commercial laptop can spin up an army of fake profiles and use LLMs push specific political views, or conduct astroturfing on a massive scale.

While regulatory measures are imperative, effectively enforcing them remains a considerable challenge. Fortunately, the field of research dedicated to employing [LLMs for fact-checking](https://arxiv.org/abs/2210.08726)  purposes is already active and holds promise.

### One last thing

LLMs are already being used to automate lead generation and sales. Studying these applications of LLMs, I have recently stumbled upon "prompt injection". One wonders if a new arms race to secure and hack LLMs is already underway.

<img src="./Blog/BlogFiles/June2023_LLM/promptInject.png" src="width:30%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em;"> Injecting commands in white font and hidden text to confuse LLM is the new sport on Techie Twitter.</p>


#### Footnotes
<p style="font-size:0.8em">
<ol>
<li> This is especially worrying. For instance, if a facial recognition system places you in the crosshairs of a police investigation your lawyer can not ask for a suppression hearing, and there is no opportunity for cross examination.</li>
<li> The document does specify however that traditional "terms of service" are inadequate and that users are entitled to transparent and easy mechanisms to control how their data is used.</li>
</ol>
</p>
