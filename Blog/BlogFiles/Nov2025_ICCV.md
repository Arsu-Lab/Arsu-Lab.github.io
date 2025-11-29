# ICCV 2025 Digest

November 2025, Tags: misc

Last month I had the pleasure of attending the 2025 International Conference on Computer Vision (ICCV) in Hawaii. After taking a month to digest the experience, and catch up on some deadlines, I decided that for posterity's sake, it might be useful to write down a few observations on the state of AI in 2025.

## i. We Now Live in a Multipolar ML Landscape

For the first time since ICCV started running back in 1987, the majority of authors were from institutions based in China (50.8% without Hong Kong which would add another 2.5%). The USA (17.2%) was a (not so close) second, followed by Korea, Germany, and Great Britain (with no other European country cracking the top 15).


<img src="./Blog/BlogOther/ICCV_countries.webp" style="width:60%;"></img>

Moreover, most of the companies that had booths were Chinese companies with branches in San Francisco. Though this might seem unsurprising considering DeepSeek, Kimi K2, and other open source LLMs from Chinese institutions are currently dominating every LLM benchmark, personally I did expect policies such as Biden's 2022 CHIPS act and Trump's "China Initiative" to pose bigger setbacks. Regardless, with the new reality of  a multipolar AI landscape we are likely to see the continued unfortunate escalation of isolationist policies, making scientific collaboration increasingly difficult, and adding new bureaucratic hurdles<sup>1</sup>. US and China aside, Europe seems to be scrambling to shore up its meager AI ecosystem through "[The 28th Regime](https://www.europarl.europa.eu/committees/en/the-28th-regime-a-new-legal-framework-fo/product-details/20250603WKS06401)", which would act as a single legal framework for all EU countries (no more country specific regulation or employment rules). But it is unlikely these efforts will bear fruit before the 2030s at the earliest.

## iii. Rolling Stones

Many of the papers (not going to point fingers) could be summarized with *"We modified the self-attention block / loss function / training schedule into a much more complicated version and got a 0.5% improvement on a benchmark"*. Overall, the atmosphere lacked the excitement that was present even a year ago (for instance ICLR in April was buzzing about topological models) and there does not seem to be a hot research direction or idea driving the field. Personally, I would have been interested to see domain-specific applications of foundational models to low resource problems. But instead most papers seemed to focus on Generative Video benchmarks (with a big focus on generating realistic dancing for some reason).   

## iv. The Clankers are Coming

Many of the workshops, company booths, and papers focused on Egocentric computer vision and AI for robotics. If there is a new frontier forming, robotics might be it! However, instead of an [embodied android](https://en.wikipedia.org/wiki/After_Yang) with a local AI / memory bank learning from unique experiences we seem to be headed towards a subscription model with constant API calls to a foundational model / remote operator.


<img src="./Blog/BlogOther/ICCV_clankers.webp" style="width:60%;"></img>

## v. The Highlights

#### Embodied Learning
 Linda Smith gave an [excellent talk about embodied learning](https://www.youtube.com/watch?v=M9SE4j3IzTU). She argued that toddlers are able to curate their "training stimuli" to an extent by manipulating objects, moving around to get multi-angle views, and even interacting with others to get more information when needed. She hypothesized that this might finally explain the "poverty of the stimulus" dilemma, and offers a way for embodied robots to quickly learn novel concepts upon first encounter.

#### What Happened to Mamba?
Our brains process information sequentially, that is why, in recent work done in our lab, we argue that [selective state space models (i.e. Mamba) are better models of human language processing](https://github.com/Arsu-Lab/Human-Language-Network-Modeling-with-Selective-State-Spaces) than transformers based LLMs. This breaks down when it comes to images (we do not view images sequentially patch by patch) but in their ICCV paper [*Vamba: Understanding Hour-Long Videos with Hybrid Mamba-Transformers*](https://tiger-ai-lab.github.io/Vamba/) Ren et al. demonstrate that Mamba can be used on top of ViT image embeddings to model videos in linear time. 

Despite its strengths, Mamba continues to struggle to find a foothold in the crowded LLM space. Perhaps simply because a dedicated and robust ecosystem for deploying self-attention block exists making the cost-benefit of adopting SSMs questionable. It is nice to see that, at least in academia, researchers are continuing to develop these models, even if industry buy-in remains low. 

### MultiModal Embeddings, Why and How?

Multi-modal models (like CLIP) have achieved state of the art even on vision only benchmarks, leading researchers to postulate that language supervision introduces semantic structure that is impossible to learn from images alone. An ICCV paper from Meta challenges this notion. In the paper [*Scaling Language-Free Visual Representation Learning*](https://openaccess.thecvf.com/content/ICCV2025/papers/Fan_Scaling_Language-Free_Visual_Representation_Learning_ICCV_2025_paper.pdf) Fan et al. trained a self-supervised uni-modal model on the same data as CLIP, achieving competitive performance despite the lack of language supervision.

In a related paper from Apple titled [*Scaling Laws for Native Multimodal Models*](https://openaccess.thecvf.com/content/ICCV2025/papers/Shukor_Scaling_Laws_for_Native_Multimodal_Models_ICCV_2025_paper.pdf), Shukor et al. trained 457 multimodal models, and demonstrated that the standard late vision-language fusion approach does not yield an advantage over models where the modality fusion happens relatively early.

### Biometrics for Art Workshop

Serendipitously, I wandered into the *Biometrics for Art Workshop* on the second day. While all speakers presented interesting work, I particularly enjoyed a presentation by emeritus Prof Rick Johnson. Johnson taught at Cornell for forty years, with a five year hiatus beginning in 2007 during which he held an appointment at the Van Gogh Museum in Amsterdam. Since returning from the Netherlands, Johnson established a computational art history program at Cornell, pioneering many computational methods for art provenance analysis, before retiring in 2021 and moving to Hawaii. Prof Johnson shared many insights into biometrics applications in art, but also on successfully collaborating with people from diverse backgrounds. I highly recommend [watching his presentation](https://youtu.be/PguLFIyYKN4?si=7fWQ1tr4f4jyNyjr&t=3413).

### Footnotes
<p style="font-size:0.8em">
<ol>
<li>If you are interested in the geopolitical implications of the AI competition, Benjamin Bratton's idea of [The stack](https://en.wikipedia.org/wiki/The_stack_(philosophy)) is a useful framework.</li>
</ol>
</p>




