# Shortcut Learning In Medical Diagnostics

January 2025, Tags:  Introductory Reading, AI Fairness, Interpretability

<img src="./Blog/BlogImg/January2025_Shortcuts.webp" style="width:70%; object-fit: contain;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em;text-align:center;"">Midjourney generated image. Prompt "Bias in AI"</p>

Any machine learning practitioner has heard the adage `Neural Networks are a black box' repeatedly since their first *Intro to AI* course; train a classification model with labeled images of vehicles and it would start to rely on the presence of water and clouds to classify boats and planes. This reliance on simple correlated spurious features is an example of *Shortcut Learning*, and is an active area of research that touches upon Fairness in ML, explainability, and multi-modality. Here we review some recently developed approaches for shortcut mitigation, and discuss their applicability for medical diagnostics tasks.

One interesting sidenote: recent research have demonstrated that while shortcut learning can still be found in [Transformers](https://arxiv.org/abs/2501.00942), these models are [immune to many of the shortcuts](https://arxiv.org/abs/2203.09125) that plague convolution based neural networks. The exact reason behind this remains a mystery, but it seems like the global nature of the attention mechanism in Transformers is the likely culprit. Here we will put aside the distinction between the architectures, instead we encourage the interested reader consults the two aforementioned works before diving into shortcut learning research.

## How to Align?

Humans often intuitively perceive some attributes as core features intrinsic to a class (such as the shape of the object) and others as spurious (presence of clouds in the background). However, formalizing a method for differentiation between core and spurious features is semi-impossible in any non-trivial setting, as the same feature can be spurious or core depending on the task; For instance, use of ethnic background information could be beneficial in medical diagnostic settings, but discriminatory when calculating the risk of recidivism. In consequence, several shortcut learning research frameworks have emerged, each adopting various - implicit and explicit - assumptions regarding the nature of spurious and core attributes. This frameworks can largely belong to one of three categories. 

<img src="./Blog/BlogOther/shortcuts_diagram.webp" style="width:60%; object-fit: contain;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em;text-align:center;"">Approaches used to align models with what human precieve as core features</p>

### In-Training Approaches

Rather than fine-tune the model after the training is complete, researchers have instead proposed frameworks that modify the training process to produce models that are robust to shortcut learning. 

Interesting examples include [two](https://arxiv.org/pdf/2310.18590) [papers](https://arxiv.org/pdf/2301.13293) from Google India. The essential observation underlying this line of research is intuitive: the spurious features must be simpler to learn than the core feature, otherwise the model would have discovered the core features first and not have learned any shortcut. Building on this observation, the authors argued that shortcuts are learned in early network layer, and shortcut learning appears in images that early layers (erroneously) classify with high confidence.  

<img src="./Blog/BlogOther/
shortcut_google.png" style="width:60%;"></img>
<p class="text-sm-center" style="margin:0em 8em 1em; font-size:0.8em; text-align:center;"">
Left: <a href="https://arxiv.org/pdf/2310.18590">Using Early Readouts to Mediate Featural Bias in Distillation</a> images with shortcuts are confidently classified by early network layers. Right:<a href="https://arxiv.org/pdf/2301.13293">Overcoming Simplicity Bias in Deep Networks using a Feature Sieve</a> uses extra loss to force early layers to 'forget' simple spurious features.
</p>

Another example, [MaskTune](https://arxiv.org/abs/2210.00055) uses GradCAM to identify and mask the image regions used to classify the image, forcing the model to explore alternative features that can be used to infer the label.    

These examples highlight important attributes of In-Training approaches: they involve blind feature suppression, and it is impossible to identify or control which features are suppressed.  Generally, these methods are not suitable for diagnostic settings where model interpretability is not only desirable, but often legally mandated (for instance in the EU by [Article 22 of the General Data Protection Regulation](https://gdpr-info.eu/art-22-gdpr/)).

### Post Training Fine-Tuning Approaches

Perhaps the most common approach for shortcut mitigation. These methods start with a biased model which is fine tuned to reduce reliance on spurious signals. These approaches either assume access to group annotation (labels for both the class and the presence or absence of the shortcut) or can be completely unsupervised.

Perhaps the most famous example of such approach is [Deep Feature Reweighting](https://arxiv.org/abs/2204.02937) which simple retrains the last model layer with a balanced dataset (the shortcut appears in the same percentage of examples for each class).  Unsupervised approaches such as [Just Train Twice](https://arxiv.org/abs/2107.09044) and [Correct-N-Contrast](https://arxiv.org/pdf/2203.01517) usually use misclassification as a proxy signal for shortcut presence. Specifically, assuming the spurious feature is present in the misclassified images, researchers then upweight these examples, or use contrastive learning to realign their representations.


### Data Editing Approaches

If the spurious feature is known, it is possible to mitigate the shortcut learning by editing the data. For instance the authors of ['Discover and Cure'](https://arxiv.org/pdf/2305.00650) look at a concept bank and discover correlated concept pairs (dogs and parks or cats and beds). The authors then generate new images by transposing images to create a balanced dataset (for instance by transposing images of dogs and beds) that can be used to train shortcut free models

This approach is especially suited for clinical applications. As it guarantees that the models do not rely on shortcuts. For instance, work done by the Seifert lab in Marbug demonstrated that model neurons are often polysemantic, [encoding both spurious and core features](https://arxiv.org/pdf/2204.02937) and approaches such as Deep Feature Reweighting fail to completely eliminate model reliance on spurious correlations, an outcome that was achieved when they used [instead data editing](https://www.mdpi.com/2075-4418/12/1/40).     

## Which Approach Can Be Used For Medical Diagnostics?

In-Training and Unsupervised Post-Training Approaches run into a the same  [regulatory wall](https://gdpr-info.eu/art-22-gdpr/). Moreover, since the status of a feature as core vs spurious is task dependent and subjective (skin color can be relevant or spurious when diagnostic different types of cancer) blind feature suppression is a less than ideal solution.

On the other hand, Data Editing and Supervised Post Training Fine-Tuning require access to data annotation. Which is not a feasible solution in diagnostic settings where data labeling requires deep domain expertise, and access to data requires clearing many institutional and bureaucratic hurdles.

Recently, we attempted to remedy these issues by leveraging the unique characteristics of [Transformers](https://arxiv.org/abs/2501.00942) for unsupervised interactive and resource efficient shortcut mitigation. The proposed framework consists of a two step approach which first leverages LLM based concept bottlenecks to identify shortcuts and then uses patch ablation to edit out the spurious features within images during interface. 

Another approach we had success with involved [using Sparse Auto-Encoders (SAEs) to crowbar apart core and spurious features](https://github.com/Arsu-Lab/Weakly-Supervised-Shortcut-Mitigation-Sparse-AutoEncoders/) by disintangling the representations into monosemantic neurons in the sparse space. Simple correlation is then used to identify and mute the sparse-space neurons that encode spurious feature (see figure below). The correlation analysis works with as little as 25 images with/out spurious feature even when they only have a single label. In other words, the method requires only week partial supervision, which is often all that is available in real-world diagnostic settings.

<img src="https://raw.githubusercontent.com/Arsu-Lab/Weakly-Supervised-Shortcut-Mitigation-Sparse-AutoEncoders/refs/heads/main/SparseMuting_method.png" style="width:80%; object-fit: contain;"></img>

## Conclusion

Despite significant progress, major challenges remain in deploying machine learning models in real-world settings - particularly in diagnostics. For junior researchers seeking impactful problems that are closely aligned with the current state of the field - and that, unlike large language model (LLM) research, don’t require massive computational resources - shortcut learning represents a particularly promising and accessible area of study. Especially since many top tier conferences have been adding special [shortcut learning workshops](https://iclr.cc/virtual/2025/workshop/23995) and [Tracks](https://icml.cc/virtual/2023/workshop/21493). 











