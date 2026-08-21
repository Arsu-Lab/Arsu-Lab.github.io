# Learning Before the Task: Pre-Train Your Model

August 2026, Tags: misc <br>
Author: Nicolas Miarka


The past decade has shown tremendous improvements in AI applications across the board. Whether it is generative AI that creates social media content, an analytical model that detects cancer in X-rays faster and more reliably than any human expert, or the mass surveillance and automated facial recognition we see in the United States with Palantir: Artificial Intelligence is dictating many aspects of our lives. In spite of the fact that many people have only heard of Artificial Intelligence with the release of ChatGPT in late 2022, the field of Machine Learning has existed for decades. So why have we only recently achieved such fundamental breakthroughs and improvements across so many domains? The answer might lie in the **P** of GPT: *Pre-Training*, a crucial step in the modern model training pipeline.

In 2012, AlexNet won the ImageNet challenge by a landslide. Shortly after, researchers realized that the weights learned by AlexNet could be transferred to other tasks, establishing supervised pre-training and fine-tuning as the new industry standard. While supervised pre-training and fine-tuning became the go-to for many models, it came at a particular cost. Labeled data is expensive and not always readily available. In 2018, NLP made a gigantic leap with the introduction of self-supervised pre-training. Models like BERT were trained by masking words in articles and text and guessing what was missing. With this training regime, no human labeling is needed, drastically reducing the cost of getting the data, allowing the models to pre-train on the entirety of the Internet.

## Same Idea, Different Domain

The idea of self-supervised pre-training did not remain exclusive to NLP for long. The appeal of eliminating the need for expensive, human-labeled data made researchers adapt this approach to other disciplines, such as computer vision<sup>1</sup>. Although a similar idea to BERT’s masking would later emerge in the form of MAE, the first advances in the field were made by Google’s ”Simple framework for Contrastive Learning of Visual Representations” [(SimCLR)](https://github.com/google-research/simclr) released in 2020. SimCLR showed the world that self-supervised pre-training for visual representations is not only possible, but also highly effective.
In order to achieve self-supervised learning for visual representation, SimCLR uses the concept of a contrastive loss function. The intuition is to group the data into positive and negative pairs. The model then tries to put the positive pair as close together as possible in the embedding space and negative pairs as far away as possible. Since negative and positive pairs can be very similar to each other, the model has to become good at encoding visual information into vectors.

<img src="/Blog/BlogOther/SimCLR.webp" style="width:50%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em">From <a href="https://github.com/google-research/simclr">The original SimCLR github</a>. Embeddings of different views of the same image are attracted to each other, while views from different images are repelled.</p>

The pairs get created with visual data augmentation functions. The authors change the image by cropping and resizing, flipping, turning, color distorting and blurring the images. The same data augmentation functions get applied twice to each image in the dataset. If two images belong to the same original image, they form a positive pair, otherwise a negative pair. They experimented with different combinations of augmentation functions and found out that a combination of color distortion and cropping yielded the best results. Color distortion was very important to this pipeline because without it the model tried to cheat the system by heavily relying on the color histogram to pair positive pairs together. By adding a color distortion function, the model can no longer rely on this easy approach.

Additionally, the model used a non-linear projection head. It acts as an additional transformation for the base encoder. It filters different features from the vector and projects it into a different embedding space, where the vectors are invariant to features like color or orientation. Without this extra layer, the base encoder would have to drop this information entirely, since the loss function would penalize it for pairing similar colored images together after adding the color distortion. With the projection head, the base encoder can purely focus on improving at vector encoding.

After training this architecture, the projection head gets thrown away and the model gets fine-tuned on labeled data. Using ImageNet classification as a metric, the SimCLR architecture was far ahead of every other self-supervised vision model at the time and comparable to supervised learning models that use the same base encoder architecture. It also showed that self-supervised pre-training greatly benefits from wider and deeper architectures compared to supervised models. The benefits shown from non-linear projection heads and a combination of data augmentation functions were also novel.

While SimCLR has been one of the greatest milestones for visual representation models in recent years, it comes with a few disadvantages. SimCLR uses massive batch sizes, loaded directly into memory. This removes the need for memory banks, but introduces the problem of computational requirements. Google is able to train this model because they have access to basically unlimited computational power with their GPU/TPU clusters. However, training this model locally is close to impossible. It is also very sensitive and heavily reliant on the specific data augmentations being used. This makes it barely applicable in certain fields, like cancer detection in X-rays, as the color distortion can destroy important medical information. Later self-supervised methods like BYOL showed that negative pairs are not necessarily required.

SimCLR has without a doubt revolutionized visual representation models by showing that a simple architecture can use self-supervised learning to yield very good results. While it is not perfect, it has laid a very solid foundation that later models built and improved upon. SimCLR proved that if pre-training is good enough, the fine-tuning phase requires significantly less labeled data and effort, shifting the importance towards pre-training. While pre-training is technically not needed for architectures, it allows them to scale and accelerates the research and development of new models.


## The Modern Day

As mentioned previously, SimCLR was the first milestone in self-supervised pre-training for visual representations. Since then, the field rapidly evolved to address its limitations and fundamentally shifted how models interact with visual data.

<img src="/Blog/BlogOther/MaskedAutoEncoders.webp" style="width:70%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em">From <a href="https://arxiv.org/pdf/2111.06377">Masked Autoencoders Are Scalable Vision Learners</a>. Masked image patches are reconstructed from unmasked ones.</p>

While SimCLR heavily relied on aggressive image augmentations and comparing whole images against each other, other researchers looked back at NLP for inspiration. [Masked Autoencoders (MAE)](https://github.com/facebookresearch/mae) took the exact same idea that BERT used for text and applied it to vision. Instead of contrastive loss, MAE masks image patches (sometimes up to 75% of the image is masked) and learns to reconstruct the missing pixels from the remaining patches. Since this method does not require negative pairs, it completely avoids the problem of reliance on gigantic batch sizes. While SimCLR learned by comparing whole concepts, MAE learns by understanding how local pixels and structures relate to each other.

Another foundational work in self-supervised pretraining was [Meta’s DINOv2](https://github.com/facebookresearch/dinov2). While frameworks such as SimCLR and MAE focus on loss functions and masking, Meta's work concentrates on ensuring the quality of the pre-training data. It combined previous self-supervised ideas, like teacher-student learning and patch-level masking, with a clean curated dataset of 142 million images (LVD-142M) chosen out of 1.2 billion raw web images. This combination allows DINOv2 to produce universal visual representations. The resulting representations are so rich that many downstream tasks - like semantic segmentation - the model requires minimal fine-tuning, heavily supporting the argument that pre-training has become the most critical (even if computationally expensive) element in the pipeline.

<img src="/Blog/BlogOther/dinoV2_downstream.webp" style="width:70%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em">From the <a href="https://github.com/facebookresearch/dinov2">DinoV2 project page</a>. Decent performance on downstream tasks can be achieved after training a decoder for as little as 20 epochs.</p>


Finally, models such as SimCLR, MAE, and DINOv2 share one major limitation: They are designed around static 2D image inputs and do not explicitly model temporal information. However, our real world is not a static frozen picture, it has the dimension of time. More recent literature, namely “*Temporal Slowness in Central Vision Drives Semantic Object Learning*” attempts to address this. This work takes self-supervised pre-training a step further by using [biology-inspired learning](https://arxiv.org/pdf/2602.04462)  on egocentric video rather than single images. The model first uses gaze estimation to extract a crop of central vision centered around the gaze. It then aligns these crops across a temporal window of one to five seconds, requiring that crops from temporally adaject frames get embedded into similar vectors. This biology-inspired approach significantly improves detailed object recognition and contextual organization without relying on isolated static frames.


## Conclusion

When looking at the history of visual representation from AlexNet’s supervised learning to the beginning of self-supervised learning with SimCLR, modern models like DINOv2 and the biology-inspired temporal slowness approach, it becomes clear how the focus in Machine Learning has shifted. The training and fine-tuning on labeled data used to be the main bottleneck for these architectures. But by shifting to self-supervised pre-training, the field removed the need for expensive human labels and allowed models to learn massive amounts of information without supervision. While task-specific training is still needed to make a model usable for a specific job, it is the massive self-supervised pre-training phase that acts as the true catalyst and gives the model its core intelligence in the first place.

#### Footnotes
<p style="font-size:0.7em">
1. Actually, self-supervised learning did not necessarily emerge in NLP rather than computer vision. Famously, at NeurIPS 2016, Yann LeCun used the cake analogy, arguing that self-supervised pre-training is akin to the base of the cake, while supervised and reinforcement learning are the icing and the cherry on top.
</p>


