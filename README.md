# COVID-testing Project


### Proposed Approach: 

Lateral flow immunoassay tests appear like a manageable, affordable and easily deployable measure to allow at-home testing. Two main potential issues remain to consider:

    * *Interpretation of the results:* the results of the tests are less easily interpretable than similar tests (pregnancy, etc). It might as well be difficult sometimes to an untrained eye to distinguish between false positives and negatives (weak concentration marks, time since the test was done, etc). It thus seems necessary to couple the provision of test kits with a web app allowing the users to use a picture of the output of the test to get a more confident diagnostic.
    * *Accuracy of the test result:* Another issue comes from the sensitivity/specificity associated to the test.


### Goal

Coupling the app with a statistical algorithm, our goal is to mitigate these two issues by providing the user with \textbf{(a) the confirmation of the diagnostic, (b) confidence intervals associated with the uncertainty to shed more light on the uncertainty associated to the diagnostic,  and (c) provision (if need be) of the number of days the user should remain in quarantine}.

We thus propose to try fitting a simple logistic regression model using our environmental variables and image variables.
     $$\log(\frac{\mathbb{P}[Y=1|X]}{\mathbb{P}[Y=0|X]}= X\beta$$
This model has the advantage of being interpretable and naturally providing uncertainty quantification. 

Our dataset would consist in two types of variables, allowing the flexible integration of user-specific and county-specific information to infer the diagnostic and confidence intervals:

        + __Subject-specific variables__, based on the app questionnaire and photo of the test, assessing each individual's own level of risk:
            - Symptoms exhibited (quantifying fever, cough, runny noise, etc)
            - Size of household + nb of members infected
            - level of individual lockdown (in the past 5 days, how many times have you or a member of your household been grocery shopping)
            - {\bf Photo of the outcome of the test:} we intend to use a deep-learning architecture ({\bf ResNet-50}) to process the images and convert these images into feature vectors. Training a DL architecture to perform the classification task, we can use the last layer of the network as an "image-feature" vector that we can then input in any classical, potentially more itnerpretable statistical algorithm -- as summarized by the following workflow:
                1. Using pre-trained (on imagenet) ResNet50 layers, polish the training on immuno assay tests (simple classification task)
                2. As users progressively input their images and the dataset keeps growing, progressively finetune the algorithm so as to get more salient feature vectors.

        + __County-Specific variables__, which will be useful to update our prior on the individual being infected by the disease (as opposed to the flu or other COVIDs):
            - Level of the epidemic in the county (nb of infected cases, tested)
            - if possible, level of social distancing (e.g, through SafeGraph's social distancing metric)

    As the user database builds up, we hope to have more informative county statistics, thus refining the quality of the prior.
