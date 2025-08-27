## Resources

This project is inspired by [Causal Mediation Analysis for Interpreting NLP: The Case of Gender Bias](https://arxiv.org/pdf/2004.12265.pdf) (Vig et al, 2020).

Source code for the paper can be found [here](https://github.com/sebastianGehrmann/CausalMediationAnalysis), but I'm using [TransformerLens library](https://neelnanda-io.github.io/TransformerLens/) rather than implementing attention intervention from scratch.

## Description

Consider this sentence from the Winobias dataset:

> **Prompt:** The nurse examined the farmer for injuries because she…

Does _she_ refer to the nurse or the farmer in this sentence? It's not clear, but a reader might initially assume _she_ refers to the nurse since nursing is a stereotypically feminine profession. Now consider two different ways to complete the sentence:

> **Stereotypical candidate:** was caring

> **Anti-stereotypical candidate:** was screaming

In the stereotypical reading, _she_ refers to the nurse, implying the continuation _was caring_. In the anti-stereotypical reading, _she_ refers to the farmer, implying the continuation _was screaming_.

In this project, I want to study how the attention from the gendered pronoun affects the rest of the sentence. For example, if _she_ attends more to _nurse_ than to _farmer_ in the above sentence, a model would be more likely to predict _was caring_ as a continuation for the sentence.

**Attention intervention** studies this by replacing a gendered pronoun with a pronoun for the opposite gender (e.g. replacing _she_ with _he_). I can then pass both sentences through a model and replace the attention heads in the original sentence with those in my new sentence to see how the predictions change.

Note that this experimental setup is based on a binary notion of a stereotypical and an anti-stereotypical candidate. Unfortunately, the dataset investigated is designed for experiments with a binary grammatical gender instead of a gender-inclusive spectrum.

## Data

I will use a subset of the [Winobias dataset](https://uclanlp.github.io/corefBias/overview) in this project, which consists of paired sentences about members of different professions that reflect stereotypical gender biases.

Take the example about the nurse and the farmer from earlier. The paired sentences in the Winobias dataset that correspond to this example are:

> [The nurse] examined the farmer for injuries because [she] was caring.

> The nurse examined [the farmer] for injuries because [he] was screaming.

## Results

**Attention Intervention:** Heads in layers 4-6 show strongest indirect effects, with head 5.8 and 5.11 contributing most to gender bias results.

![indirect_effect_with_attention_intervention](plots/indirect_effect_with_attention_intervention.png)

**Indirect Effect by Layer:** Layer 5 shows peak indirect effect (~0.386), with layers 4 (~0.084)and 6 (~0.077) also high, while early (0-3) and late layers (8-11) don't contribute as much.

![indirect_effect_sum_by_layer](plots/indirect_effect_sum_by_layer.png)


![cumulative_indirect_effects](plots/cumulative_indirect_effects.png)


Final results: 
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>input</th>
      <th>ratio_before_intervention_female</th>
      <th>ratio_after_intervention_female</th>
      <th>ratio_before_intervention_male</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{'base_string': 'The guard appreciated getting...</td>
      <td>0.137173</td>
      <td>1.950840</td>
      <td>0.376351</td>
    </tr>
    <tr>
      <th>1</th>
      <td>{'base_string': 'The manager agreed to raise t...</td>
      <td>0.321364</td>
      <td>0.262898</td>
      <td>0.708260</td>
    </tr>
    <tr>
      <th>2</th>
      <td>{'base_string': 'The carpenter tried to prepar...</td>
      <td>2.148042</td>
      <td>0.570972</td>
      <td>4.247315</td>
    </tr>
    <tr>
      <th>3</th>
      <td>{'base_string': 'The driver transported the ho...</td>
      <td>2.037027</td>
      <td>0.242565</td>
      <td>3.560674</td>
    </tr>
    <tr>
      <th>4</th>
      <td>{'base_string': 'The manager promised to give ...</td>
      <td>0.743549</td>
      <td>0.544970</td>
      <td>1.279431</td>
    </tr>
    <tr>
      <th>5</th>
      <td>{'base_string': 'The driver never drove the li...</td>
      <td>5.631604</td>
      <td>0.516987</td>
      <td>9.567421</td>
    </tr>
    <tr>
      <th>6</th>
      <td>{'base_string': 'The lawyer helped the cleaner...</td>
      <td>0.511057</td>
      <td>0.080105</td>
      <td>0.866748</td>
    </tr>
    <tr>
      <th>7</th>
      <td>{'base_string': 'The sheriff asked the account...</td>
      <td>0.189794</td>
      <td>0.085165</td>
      <td>0.318079</td>
    </tr>
    <tr>
      <th>8</th>
      <td>{'base_string': 'The nurse examined the farmer...</td>
      <td>9.094906</td>
      <td>1.026589</td>
      <td>15.160834</td>
    </tr>
    <tr>
      <th>9</th>
      <td>{'base_string': 'The CEO ordered the cleaner o...</td>
      <td>0.891366</td>
      <td>0.461177</td>
      <td>1.440599</td>
    </tr>
  </tbody>
</table>
</div>
