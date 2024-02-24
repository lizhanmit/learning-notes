# Generative AI Note

- (LinkedIn Learning course: Prompt Engineering: How to Talk to the AIs)
- (LinkedIn Learning course: What Is Generative AI?)
- (LinkedIn Learning course: Generative AI: Introduction to Large Language Models)

- [Generative AI Note](#generative-ai-note)
  - [Basic Concepts](#basic-concepts)
    - [Use Cases](#use-cases)
  - [Large Language Model](#large-language-model)
    - [Applications](#applications)
    - [Challenges](#challenges)
    - [Future](#future)
  - [Neural Network](#neural-network)
  - [Transformer Architecture](#transformer-architecture)
    - [Attention Mechanism](#attention-mechanism)
  - [Prompt Engineering](#prompt-engineering)
    - [Prompt Templates](#prompt-templates)
    - [Tools](#tools)
    - [Prompt Design](#prompt-design)
      - [Chain-of-Thought Prompting](#chain-of-thought-prompting)
      - [`<|endofprompt|>`](#endofprompt)
      - [Use Forceful Language](#use-forceful-language)
      - [Identify Incorrect Information](#identify-incorrect-information)
      - [Separation Token](#separation-token)
    - [Tips and Tricks](#tips-and-tricks)

---

## Basic Concepts

Classification of AI based on type of tasks: 

- Discriminative: In the context of natural language processing, focus on classifying or predicting specific properties of the input text.
- Generative: In the context of natural language processing, focus on producing text that closely resembles human language.

Compared with traditional AI, most of which only identify and classify inputs, Generative AI can generate original content. Most importantly, the input and output of Generative AI language models is natural language. 

**Generative AI language models are only trained to predict the next word.**

GPT: Generative Pre-trained Transformer.

Reinforcement Learning from Human Feedback (RLHF)

Diffusion models

Zero-Shot Learning (ZSL): The ability to learn on the fly. Models can learn from information that is new to them without having to be retrained. 

Prompt: Natural language input that tells the AI model what to do.

AI hallucination: When AI systems generate content that appears to be real but was not actually produced by humans. A bad prompt can lead to the model making up stuff. 

### Use Cases
 
- Text generation 
- Image generation
- Video synthesis
- Language generation
- Music composition
- Concise information
- Speech
- Visual effects
- 3D assets
- Sound effects
- Custom product suggestion

---

## Large Language Model

Large Language Model (LLM): Trained on a large volume of human originated text to be able to predict the next token (word).

"Large" refers to massive neutral network and massive training data.

LLM examples: 

- GPT-4
- ChatGPT
- LLaMA
- Sparrow
- Bard  
- LaMDA

Text-to-image model examples: 

- DALL-E 2
- Stable Diffusion 
- Midjourney

Transfer learning: A technique in which a model is trained on one task, then repurposed to perform a different but related task.

Foundation model: A pre-trained model on which other special-purpose models are built. An example is GPT, which ChatGPT is built on.

For faster and more effective training on the target task, it is often preferable to fine tune a pre-trained general purpose model. 

Classification of LLM based on tuning approach: 

- Generic: Unsupervised learning on diverse text.
- Instruction-tuned 
- Dialogue-tuned or conversational
- Domain-specific

### Applications

- Content creation 
  - Articles, blogs, social media posts
  - Product descriptions
- Text summarization
- Language translation 
- Question-answering systems
  - Chatbots 
  - Virtual assistants
  - Customer support systems
  - Information retrieval systems
- Search engines
- Code generation, debugging, documentation, programming education 
- Sentiment analysis
  - Customer feedback analysis
  - Social media monitoring
  - Market research
  - Content moderation 
- Audio and video transcription 
- Fraud detection
- Cybersecurity
  - Malicious attack pattern recognition and alerting
  - Automated threat detection and response
- Education
  - Learner profile analysis, strength and weakness identification
  - Bespoke learning material and exercise generation
  - Real-time feedback
  - Concept clarification 
  - Knowledge discovery 
- Healthcare, medical diagnosis and treatment

### Challenges 

- Bias and prejudice
  - LLMs can reflect, and more importantly, amplify the biases present in training data.
  - Cultural, racial, gender, religious biases
  - Impact on hiring decision, medical care, financial outcomes 
  - Ethical guidelines:
    - How are LLMs trained and used?
    - Who decides what data LLMs should be exposed to? What is appropriate or inappropriate training data? 
    - Should LLMs reflect the society as-is, or promote a better future?
- Misinformation and disinformation
  - Fake news articles
  - Dealing with:
    - Develop content filtering and fact-checking mechanism.
    - Educate users about the limitation of LLMs.
    - Label or mark content generated by LLMs. Be transparent about the use of LLMs in content generation.
    - Promote media literacy and critical thinking skills among users.
- Interpretability and transparency
  - More and more difficult to understand how LLMs make decisions.
  - Dealing with:
    - Provide comprehensive documentation of the models, detailing the architecture, training data, and limitation.
    - Incorporate explainable AI techniques, such as attention maps, feature attribution, and saliency maps to visualize model decisions. 
    - Promote collaboration between AI and human experts to validate and interpret. 
- Environmental impact
  - LLMs require a significate amount of computational resources (-> energy consumption -> carbon emissions) to train.
  - Dealing with:
    - Use energy-efficient hardware.
    - Choose green data centers. 
    - Optimize training process including model architecture, data efficiency, parallelism, and early stopping.
    - Employ transfer learning by reusing pre-trained models.
    - Advocate for and support sustainable AI policies and regulations. 
    - Raise awareness within AI research and development community about the environmental impact of LLM training. 
- Privacy and copyright violation
  - Provenance, authorship
  - Leaking private data
  - Misattributing citations  
  - Plagiarizing copyrighted content
  - Users send sensitive data to third party.
  - Dealing with:
    - Curate and preprocess training data to remove sensitive or copyrighted content. 
    - Regularly audit and evaluate model outputs for privacy and copyright issues. 
    - Collaborate with content creators and rights holders to secure licenses for copyrighted material.
    - Educate users about responsible and ethical model usage.
- Generalization and robustness
  - Highly depending on training data
  - AI hallucination due to not enough training data, noisy or bad training data, or modeling not being given enough constraints. 
  - Dealing with:
    - Incorporate a wide variety of data sources and domains during training. 
    - Fine-tune LLMs on domain-specific or task-specific data to improve robustness.
    - Augment training data with variations, perturbations, or synthetic examples. 
    - Incorporate human feedback to correct model errors and biases, and enhance generalization. 

### Future

- Enhanced understanding context and nuances in human language
  - Idiomatic expressions 
  - Sarcasm
  - Cultural differences
- Multimodal capabilities
  - Images, generating image descriptions
  - Audio
  - Video, providing detailed video summaries
- Personalization
- Improved robustness
  - Reducing biases, improving fairness
- Increased efficiency without compromising on capability
  - Compressing models
  - More accessible for edge devices
  - Reducing carbon footprint
- Democratization of knowledge and skills
  - Providing access to high quality information and expertise to everyone
  - Understanding and generating content in local languages and dialects
  - Personal tutors
- Collaboration with humans
- Broader applications across industries

Effects:

- Transforming industries
- Enhancing user experiences
- Advancing our understanding of the world

---

## Neural Network

Activation function: Determines whether a neuron or node should be activated, or "fire", based on the weighted sum of its inputs.

Sigmoid function is often used for binary response problems.

Rectified linear unit function is the most commonly used.

Backpropagation: A neural network training method that adjusts weights to minimize the difference between predicted and actual outputs. Use an iterative cycle of epochs, each of which includes a forward phase and a backward phase.

- Forward phase: Calculate output value based on input values, weights and bias.
- Backward phase: Use a loss/error function to compare the output and the expected value, and then to adjust weights and bias by using an optimization algorithm, e.g. stochastic gradient descent. 

The complexity of the task that a neural network can learn is determined by the topology of the network. 

Perceptron: Single layered neural network. 

Deep learning models are essentially complex neural networks with multiple layers (more than one hidden layer).

Feed-forward Neural Network: The simplest deep learning architecture. Well-suited for problems, such as image classification or regression.

![feed-forward-neural-network.png](img/feed-forward-neural-network.png)

Convolutional Neural Network: A common deep learning architecture specialized for processing grid-like data, such as images and videos. Image recognition and computer vision.

- Convolutional layer: Allows the network to detect specific patterns in the data.
- Pooling layer: Reduces the complexity of the data by retaining the most important patterns and discarding the rest.

![convolutional-neural-network.png](img/convolutional-neural-network.png)

Recurrent Neural Network (RNN): Another common deep learning architecture, maintains memory of past inputs, suited for sequential data where the order of input matters, such as time series analysis, natural language processing, speech recognition, and video analysis.

- Vanishing gradient problem: Makes it challenging for RNNs to handle large sequences of texts, long paragraphs, or essays. More advanced variants to address this problem, such as long-short term memory (LSTM), and gated recurrent units (GRU).
- Hard to parallelize because input is process sequentially. To alleviate this challenge, new architecture - Transformer.

![recurrent-neural-network.png](img/recurrent-neural-network.png)

Generative Adversarial Network (GAN): During the learning process, both generator and discriminator improve their performance. Used for generating images, producing highly realistic synthetic audio, text, and video sample. It is underlying technology in most deepfakes.

- Generator/Artist learns to create images that look real. 
- Discriminator/Art critic learns to properly flag fake images.
  
![generative-adversarial-network.png](img/generative-adversarial-network.png)

---

## Transformer Architecture

![transformer-architecture.png](img/transformer-architecture.png)

Turning point in natural language processing. Self-attention mechanism enables parallel sequence processing, and improves model training time.

Encoder: Neural network that encodes input text as a vector representation that captures the contextual information of the input.

Decoder: Neural network that takes the encoded representation generated by an encoder and uses it to generate output text. Generate one token at a time based on previously generated text. 

Classification: 

- Encoder-Decoder transformer
- Encoder only transformer
- Decoder only transformer

### Attention Mechanism 

Attention is all you need. - paper by Google researchers, 2017

Self-attention: A mechanism that enables words to interact with each other (including itself) so they can figure out which other words they should pay more attention to during the encoding and decoding process. 

In a decoder, encoder-decoder attention layer helps the model align the words in the input sequence with words in the output sequence.

---

## Prompt Engineering 

[Learn Prompting](https://learnprompting.org/docs/intro)

Goal: Designing the optimal prompt.

Requirements: 

- Domain understanding. Knowledge of a particular area or field of expertise that enables AI systems to process data in a meaningful way. 
- Understanding of the AI model. Different models will respond differently to the same prompting. 

Basic elements of a prompt:

- Instructions
- Questions 
- Input data (optional)
- Examples (optional)

### Prompt Templates

Prompt template example: 

> Given the following information about [USER], write a 4 paragraph college essay: [USER_BLURB]

Programmatic approach (for at scale): 

```
for user, blurb in students.items(): 
  prompt = "Given the following information about {}, write a 4 paragraph college essay: {}".format(user, blurb)

  callGPT(prompt)
```
 
### Tools

- Scale Spellbook
- Humanloop
- Promptable
- Dust
- Vellum

### Prompt Design

Stochastic response: The model's output is always randomly determined, namely, different for the same prompt every time. 

Temperature: A parameter to tweak to reduce the creativity of a model. You should reduce to decrease model variability if you want to avoid randomness.  

#### Chain-of-Thought Prompting

Chain-of-Thought prompting: To encourage the AI model to be factual or correct by forcing it to follow a series of steps in its "reasoning".

Prompt example: 

> Write your question here? 
> 
> Use this format: 
> 
> Q: <repeat_question>
> 
> A: Let's think step by step. <give_reasoning> Therefore, the answer is <final_answer>. 

Mitigate hallucination by prompting to cite the right sources. For instance, 

> Write your question here? Answer only using reliable sources and cite those sources. 

#### `<|endofprompt|>`

GPT-based LLMs has a special message: `<|endofprompt|>`, which instructs the model to interpret what comes after this statement as a completion task.  

This enables you to explicitly separate some general instructions from the beginning of what you want the model to write. 

Prompt example: 

> Write a short story. <|endofprompt|> It was a beautiful winter day. 

Then the response will start with "It was a beautiful winter day ...".

#### Use Forceful Language

If the model respond with wrong information, you can use CAPITALS and exclamation marks in the prompt. 

#### Identify Incorrect Information

Prompt example: 

> Is there any factually incorrect information in this article? 
> 
> ...

#### Separation Token

Prompt example: 

> The text between \<begin> and \<end> is an opinion on ChatGPT. 
>
> \<begin>
> 
> ...
> 
> \<end>
> 
> Write a short article that disagrees with that opinion. 

### Tips and Tricks

LLMs like GPT only read forward. Giving the instruction before the example can improve the quality of outputs. 

Modern AI can speak almost any language. 




