# Introductory Reading on AI Fairness

April 2023, Tags:  Fairness, Introductory Reading

![img](./Blog/BlogImg/April2023_Fairness.png)

The field of AI fairness has been exploding, with many conferences adding special tracks and requiring authors make statements regarding the potential social impact of their work. Here we review some philosophical, technical, and practical papers tackling fairness in machine learning applications.

## A Techno-Bureaucratic Complex?
Fashionably late, 2022 governments are finally began firing their regulatory engines in earnest. So far reactions in the ML field seem to positive. A fact that is not necessarily considering algorithms like [COMPAS](https://en.wikipedia.org/wiki/COMPAS_(software)) - a software used predict recidivism rates of criminal defendants - have been in use since the mid 2010s,  remain widely used despite evidence of discriminatory bias reported in early 2016 (analysis demonstrated that blacks are twice as likely as whites to be labeled a higher risk but not actually re-offend, see [Angwin's excellent ProPoblica article](https://www.propublica.org/article/machine-bias-risk-assessments-in-criminal-sentencing)).

One major new piece of regulation is [The Whitehouse Blueprint for an AI Bill of Rights](https://www.whitehouse.gov/ostp/ai-bill-of-rights/). Although not being an enforceable law, this document could (theoretically) heavily shape future legislation. Moreover, the framework outlined in the document integrates inputs form many ML researchers and stakeholders, and the document contains a "From Principles to Practice" section with example of successful applications of the proposed framework.

The first few pages are important for contextualizing the proposed framework. First, the authors emphasise that the principles outlined in the document *"are not intended to, and do not, prohibit or limit any lawful activity of a government agency, including law enforcement, national security, or intelligence activities"*\[1\].  Second, they note that *Systemic, fair, and just treatment must take into account the status of individuals who belong to underserved communities*. These communities are specifically defined by what U.S. law considers "protected characteristics" (disability, age, race, gender, religion, sexual orientation, maternity, partnership status ...). Hence the framework does not apply to algorithms that punish poor people for [shopping at Walmart rather than WholeFoods](https://www.nytimes.com/2009/01/31/your-money/credit-and-debit-cards/31money.html) for instance.

The document focuses on five points. First that system should be safe and effective, with clear organization levels plans for assessment and  testing. Second, there should be built in algorithmic discrimination protection. Designers, developers, and deployers of automated systems should take proactive and continuous measures to protect individuals and communities from algorithmic discrimination and to use and design systems in an equitable way. <u>Proactive equity assessments</u> should be part of the system design, and protections against <u> proxies for demographic features </u> should be implemented. Third, the document discusses data privacy, specifying that *"only data strictly necessary for the specific context is collected"* and that *"in sensitive domains, your data and related inferences should only be used for necessary functions"*. Clearly, this vague language leaves a wide open door for data misuse, as any data that might help increase in profits could be argued to be necessary[\2\]. Fourth, notice and explanation should be given when your data is used in an automated system. Finally, the document emphasises the importance of human alternatives and fallbacks, allowing access to a person instead of only the automated system.
 
This document might leave the reader in a state of ambivalence. While the emphasis on proactive measures for protecting communities, as well as individuals, is encouraging. The vague language  employed seems corporate friendly by design. This is especially an issue since, as we will next explore, the devil is in the details.   

## Quantifying Fairness

[Fairness Definitions Explained](https://fairware.cs.umass.edu/papers/Verma.pdf) and [Algorithmic Fairness: Choices, Assumptions, and Definitions](https://www.annualreviews.org/doi/abs/10.1146/annurev-statistics-042720-125902) are surveys of the most popular quantifications used to measure fairness.  Before proceeding, one should consider a few points regarding these definitions.

![img](./Blog/BlogOther/AlgorithmicFairness.png)

A naive approach to fairness might require 

## From Theory To Practice

Researchers have been  facilitate    


### Footnotes
<p style="font-size:0.8em">
<ol>
<li> This is especially an worrying as if, for instance, a facial recognition system places you in the crosshairs of a police investigation your lawyer can not ask for a suppression hearing, and there is no opportunity for cross examination.</li>
<li> The document does specify however that traditional "terms of service" are inadequate and that users are entitled to transparent and easy mechanisms to control how their data is used.</li>
</ol>
</p>
