# Abstract 
LLM Based TTS emerged into the mainstream
	Speech Signals are discretized into token sequences which are modeled by an LLM with text based vocoder to waveforms
Current speech tokens are learned in an unsupervised manner (Lacks explicit semantic information and alignment into the text)

Paper proposes to represent speech with supervised semantic tokens which are derived from a multi lingual speech recognition model by inserting vector quantization into encoder

CosyVoice = Codec-Based Synthesizer for Voice generation

Results:
	Supervised semantic tokens significantly outperform existing unsupervised tokens in terms of - content consistency, speaker similarity for zero-shot([[dictionary]]) voice cloning

Utilizing large scale data further improves speech synthesis performance

First attempt to involve supervised speech tokens into TTS Models

# Introduction
TTS developed so much that it has become nearly indistinguishable from human speakers
LLMs are increasingly using TTS for higher degree of naturalness and ability to synthesize voices in zero-shot fashion

LLM based TTS models functioning:
	Speech signals -> Sequences of tokens
		This is where encoders like EnCodec or WavTokenizer come in
		They learn the speech signal to discrete token representations
	LLM utilizes text as a condition to model these token sequences
		It predicts the speech tokens from the text
	Token vocoder is then employed to reconstruct raw waveforms from tokenized speech
		Again they are converted back into speech signals

As we saw in [[Wavtokenizer]], traditionally tokens are acquired through unsupervised learning which may not capture explicit semantic information or align well with corresponding text

So in CosyVoice we implement supervised semantic tokens extracted from multilingual speech recognition model, Whisper, by integrating vector quantization into encoder.
	Whisper is an ASR which converts speech into text, by integrating our vector quantizer directly into this we get speech tokens which align with the semantics.


## To understand
```
Early
studies have shown that quantizers with auxiliary
automatic speech recognition (ASR) loss outper-
form k-means clustering on the universal speech
model (USM) for speech-to-text translation and
ASR tasks, as demonstrated in Rubenstein et al.
(2023). Additionally, Ye et al. (2024) employed
Gumbel-Softmax vector quantization to extract dis-
crete speech representations that prioritize ASR-
relevant information for ASR tasks. However, the
impact of these approaches on text-to-speech (TTS)
remains unclear.

GPT Answer
**1. The "Old" Way (Unsupervised/K-means)** Previous models (like HuBERT used in SpeechGPT) created tokens using **k-means clustering**. This groups audio segments based on how they _sound_ (acoustic similarity).

• **The Limitation:** These tokens don't explicitly know "meaning." They just know that "Sound A" is mathematically similar to "Sound B".

**2. The "New" Way (Supervised/ASR Loss)** The studies mentioned (Rubenstein et al., Ye et al.) showed that if you force the tokenizer to predict **text** (ASR) during training, the resulting tokens represent **meaning** (semantics) much better.

• **The Benefit:** The tokens become aligned with words and phonemes rather than just raw sound waves.

**3. The "Unclear" Gap** The authors are noting a gap in scientific knowledge: While we knew these "semantic tokens" were great for _understanding_ speech (Speech-to-Text), nobody knew if they were good for _generating_ speech (Text-to-Speech).

• **The Fear:** There was a risk that by focusing so hard on meaning (semantics), the tokens might lose the rich acoustic details (tone, pitch, emotion) needed to make synthesized speech sound human.

**In short:** CosyVoice bets that "Supervised Semantic Tokens" are the missing link to better TTS, acting as a bridge that connects the text input much more effectively to the audio output

```

Leveraging these supervised tokens CosyVoice is made.
## Config

LLM for converting text into semantic token sequences

Conditional flow matching model for subsequent synthesis of speech from these tokens

Prior systems like TorToise TTS employs LLM in conjunction with denoising diffusioin probabilistic models (DDPM)

But CosyVoice utilizes a conditional flow matching approach
	Flow matching approach is demonstrated to accelerate both training and inference compared to traditional diffusion models

Even though some existing methods incorporate flow matching in TTS they rely on phoneme duration prediction -> needs supplementary phonemizers and forced aligners.

CosyVoice doesn't need this

## Diffusion vs Flow matching
Diffusion - Remove noise step by step from the random noise we generated
Flow matching - The noise is removed by calculating a straight efficient path

## Highlights

- First to integrate supervised speech tokens into TTS
- Scalable zero-shot TTS system that combines LLM for text to token generation with conditional flow matching model
- x-vector is implemented to refine the quality of generated speech
	- x-vector incorporated into LLM to separate modeling of speech into semantic, speaker, prosody components
	- LLM models semantic content and prosody and conditional flow matching model captures timbre and environmental information





# CosyVoice

**Consists of 4 Components**
1. Text encoder -> align semantic spaces of text and speech tokens
2. Speech tokenizer -> Extract semantic tokens
3. LLM -> Learn whole sequence of text encodings and speech tokens
4. Conditional flow matching model -> Used to convert speech tokens into Mel spectrogram via a denoising process on the optimal path.

## Supervised semantic tokens
Supervised ASR model is employed to derive the supervised semantic speech (S3) tokenizer for speech.
Trained on multilingual audio data and possesses rich audio content from understanding capabilities.

Encoder split into two parts and vector quantizer layer inserted between them

Mel spectrogram X as input
Undergoes positional encoding
Encoder1 obtains context-aware representations H
VQ layer involved to get discrete tokens
Discrete tokens passed through Encoder2

## LLM for TTS
Formulate TTS as autoregressive speech token generation problem
V is a speaker embedding vector extracted from the speech X with a pre-trained voice print model

Text and speech tokens lie at different semantic levels -> Text encoder is used to align their semantic spaces and benefit LLM modeling

At training state -> teacher forcing scheme is employed in which the left shifted sequence is employed as the node inputs and the original sequence serves as the expected outputs

Only cross entropy losses of the speech tokens are considered during training

## Optimal transport conditional flow matching
OT-CFM is employed to learn the distribution of Mel spectrogram and generate samples from it with generated speech tokens as conditions
OT-CFM can achieve better performance compared to diffusion probabilistic models(DPMs) with simpler gradients, easier training and faster generation


### Zero-shot In-context Learning

For prompt speech and input text in the same language -> Merge both into a unified input treating prompt speech tokens as pre-generated.
With this input LM iteratively predicts subsequent tokens

When both are of different languages -> Omit text and tokens associated with prompt to prevent prosodic characteristics of original language from influencing the target language. 

Prompt text from prompt speech's content can be transcribed either through human annotation or ASR models.

After generating speech tokens they are appended after prompt tokens -> forming a composite condition for the flow-matching model

Additionally 
	Speaker embedding and Mel spectrogram of prompt speech are incorporated to further enhance timbre and environmental consistency


# Generation with instruction
Add words into the prompt to make it more natural.
```
Selene ’Moonshade’, is a mysterious, elegant dancer with a connection to the night. Her movements are both mesmerizing
and deadly.<endofprompt>Hope is a good thing.

A happy girl with high tone and quick speech.<endofprompt>The sun is shining brightly today.

Well that’s kind of scary [laughter].

```


# Dataset

**Small scale single lingual dataset**
LibriTTS corpus - 585 hours from 2456 speakers
train-clean-100, train-clean-360, train-other-500

test-clean

**Large scale multi lingual dataset**
Collected from various sources and special inhouse tools for speech detection, signal to noise ratio, speaker separation,


# Experimental settings
## Supervised Semantic speech tokenizer
ESPNet conformer ASR model
After 6 encoder layers
Vector quantizer of single codebook of 4096 codes

# Experimental results
Quantizer effect on LibriTTS test sets
![[CosyVoice-1770709702803.png]]

Inserting vector Quantizer into ASR encoder only affects the recognition performance slightly
This indicates the tokenizers trained in a supervised manner can maintain sufficient semantic information and alignment to text

To assess multilingual s3 tokenizer's ability to preserve semantic information we compare recognition performance of quantizer augmented SenseVoice-L against its original version and Whsiper-Large V3 model




## Comparison with baselines

![[CosyVoice-1770710496707.png]]

Emotion controllability of CosyVoice
![[CosyVoice-1770710824653.png]]


# Conclusion

CosyVoice is a scalable multi-lingual speech generation model which supports zero-shot in-context learning, cross-lingual voice cloning, instructed generation and fine grained controlling of emotion.
Architecture of CosyVoice is important for speaker similarity
Text and speech tokenizers affect the content consistency much
Scaling up the model size and data volume can significantly increase the performance



# Architecture

![[CosyVoice-1773134266962.png]]


x Vector : Speaker embedding, contains information about (pitch, timbre etc.)
Text Y : Text
Text encoder : Convert text into linguistic features

Text-To-Token LM
	Large Language Model architecture like **Qwen**

## Training 
X vec is produced from the entire audio clip
Text Y is given
Speech is given ( Entire audio clip )
The text along with x vec are supplied along with their respective speech tokens
Text = What is he saying? 
X -vec = Who is saying it?
Speech X = Correct audio code for it

All these are provided and the model is trained on that
The flow matching model is also trained and creates an appropriate Mel-spectrogram and then the Mel-spectrogram is converted into an audio through a vocoder


## CosyVoice

The last paper I presented was about the WavTokenizer paper in which the goal was to maintain high fidelity of reconstruction even with high compression rate. 
It succeeded in doing so but as we saw one prominent drawback was that it couldn't retain the semantic information and only focused on acoustics

Now I'm going to present paper on CosyVoice which was published in 2024.
Although the goal of this paper is different we see how they managed to retain the semantic information along with compression

The goal of the paper is to create an artificial speech from text which is indistinguishable from human speech.

 Recently LLM based TTS have emerged into the mainstream. NotebookLM has audio overview and there are voice translators which translate on the go
	We provide text to the LLMs and they generate discrete speech tokens based and from the discrete tokens we can generate text 

These LLMs are trained to go from sounds to speech tokens but there is no semantic meaning in between and are learnt through unsupervised learning
These tokens contain acoustic details but not semantic details

The goal of this paper is to add semantic details to the sounds

CosyVoice gets supervised tokens. To get these tokens it integrates a VectorQuantizer directly into an ASR model this way the speech tokens are learnt in a supervised manner. 

When we give a prompt voice it converts voice to prompt tokens, then the input sentence is converted to text embeddings by the text encoder
The prompt tokens + text embeddings are given to LLM to generate speech tokens
Conditional flow matching model  (speech tokens ) = speech

Positioning of VQ
The codebook is of size 4096 and it is positioned in the middle of the ASR model


# Introduction
Our work introduces supervised semantic tokens extracted from a multilingual speech recognition model, Whisper (Radford et al., 2023), by integrating vector quantization into the encoder. This innovation allows for more accurate semantic representation and alignment with text

Early studies have shown that quantizers with auxiliary automatic speech recognition (ASR) loss out perform k-means clustering on the universal speech model (USM) for speech-to-text translation and ASR tasks
 
CosyVoice is comprised of an LLM for converting text into semantic token sequences and a conditional flow matching model for the subsequent synthesis of speech from these tokens

Prior systems like TorToise TTS (Betker, 2023), which employs an LLM in conjunction with a denoising diffusion probabilistic models
CosyVoice utilizes a conditional flow matching approach, as it has been demonstrated to accelerate both training and inference compared to traditional diffusion mode

Existing methods use flow matching in TTS but they rely on phenome duration prediction


# Components
1. Text Encoder - used to align the semantic spaces of text and speech
2. Speech tokenizer - Extract semantic tokens
3. LLM - learns whole sequence of text encodings and speech tokens (Reformulates TTS as autoregressive sequence generation problem given text as prompt)
4. Conditional flow matching model - Used to convert speech tokens into Mel-spectrogram via denoising process in optimal path
	Mel spectrogram is used as input for HiFi GAN vocoder to synthesize waveform


## Speech Tokenizer
S3 tokenizer = Supervised Semantic Speech tokenizer
SenseVoice ASR model
Trained on multilingual audio data

Mel Spectrogram X as input
	Undergoes positional encoding and Encoder 1 
	H = Encoder1 (PositionalEncoding(X))	


![[CosyVoice1-1774075760069.png]]

![[CosyVoice1-1774075733193.png]]
In training stage codebook embeddings are updated via exponentially moving average

![[CosyVoice1-1774075795881.png]]

![[CosyVoice1-1774075812199.png]]

## LLM

![[CosyVoice1-1774075923424.png]]


INPUT 
![[CosyVoice1-1774075983884.png]]


S, E denote start and end of sequence

v is a speaker embedding vector extracted from the speech X with a pre-trained voice-print model (sv-cam++)

Text encodings obtained by passing text through Byte Pair Encoding (BPE) and TextEncoder

![[CosyVoice1-1774076200368.png]]


Since text and speech tokens lie at different semantic levels Text Encoder is used to align their semantic spaces.

A start identifier T is used between text encodings and speech tokens 
These speech tokens are extracted from S3 tokenizer

### Teacher Forcing learning

x-vector calculated from the speech 
Text of the speech is provided
Then as the model autoregressively generates speech tokens - The actual speech token is compared to the output, if the output is wrong then the original token goes into the model again to generate the next output

## Optimal transport Condition flow matching

OT-CFM can achieve better performance compared to diffusion probabilistic models (DPMs) with simpler gradients, easier training and faster generation

OT-CFM model is employed to learn the distributions of Mel spectrogram and generated samples from it with generated speech tokens as conditions


# Inference

## Zero-shot in context learning
x - vector is needed to mimic the physical sound of vocal cords - pitch
Text is for emotion, vibe

For prompt speech and input text in the same language
	Merge them to form unified input and treat prompt speech tokens as pre generated



When prompt speech and input text are in different languages
	We omit text and tokens associated with the prompt
	To prevent prosodic (rythm of a language) characteristics of the original language from influencing the target language 



## To Mel spectrogram
After generating speech tokens the speech tokens are appended to prompt tokens
x-vector and Mel spectrogram of the prompt speech is also included


## Generation with instructions

Finetuned CosyVoice using this training data without incorporating speaker embedding


# Dataset

LibriTTS (585 hours)
	Train-clean-100, train-other-500,  train-clean-360
	dev-clean used for model selection

## Model
![[CosyVoice1-1774078674430.png]]
300 M parameters

## S3 Tokenizer
![[CosyVoice1-1774078780087.png|533x492]]


From the table, we can see that inserting a vector quantizer into the ASR encoder only affects the recognition performance slightly. As a result, the VQ-inserted Conformer ASR model achieves comparable WERs of 3.18% and 7.56% on “test-clean” and “test-other” sets, respectively.

This indicates that tokenizers trained in a supervised manner can maintain sufficient semantic information and the alignment to text.




# Presentation

The papers I'm presenting are CosyVoice 1 and CosyVoice 2
The goal of these papers are to create artificial speech from text which is indistinguishable from human speech

Already there are many LLM based TTS models but what they do is they just map the words to speech taking care of only the acoustic details and not the semantic details. So when the pronunciation of the word depends on the context they fail. Eg. project and project

So CosyVoice proposes the idea to represent speech tokens with supervised semantic tokens which are derived from multilingual speech recognition model.

This paper claims that this is the first attempt to incorporate supervised speech tokens into TTS models and supervised tokens performed significantly better than unsupervised tokens.

   In unsupervised tokens the model has to find patterns without exactly knowing what the audio says but in supervised tokens the model is trained in such a way that the tokens also have to find the correct word
   

Components of CosyVoice1 model are
Text encoder : This text encoder is used to align semantic spaces of text and speech - which means it converts text into embeddings which align with speech

Then we have speech tokenizer which gives us supervised semantic tokens based on the speech

We incorporate a Large model which learns the sequence of text encodings and speech tokens

Then we have a conditional flow matching model which creates a mel spectrogram from the speech tokens along with speaker embedding 
This mel spectrogram is used as input to a vocoder to create audio wave

To obtain these supervised speech tokens what we do is take an ASR model and put a vector quantizer in between the encoder. The position of the VQ is exactly at the middle. They put it in the middle because the early layers capture low level acoustic details and the later layers capture high level semantic details so placing them just in the middle gives us semantic as well as acoustic details


v is a speaker embedding vector extracted from the speech X with a pre-trained voice-print model sv-cam++

For the training process we use teacher forcing method
We give speaker embedding,  text of the speech, and model starts to generate the speech tokens 
The cross entropy loss between actual speech tokens and generated speech tokens is used to train the model

Training involved 210,000 training steps on eight A800 GPUs

During inference for zero shot voice cloning:
We provide the x vector of prompt speech and then prompt text and the text we have to generate audio for and also provide the speech tokens from the prompt speech. These prompt speech tokens are treated as pre generated and the model continues generating speech for the text we want


These speech tokens go into the conditional flow matching model to create a mel-spectrogram and using this mel-spectrogram as input into a suitable vocoder speech is generated


They mentioned the architecture of both the text encoder and Language Model 
The cosyVoice1 consists of about 300M parameters combining both Text encoder and Language model

It was trained on about 170,000 hours of data in which English was about 30,000 



## CosyVoice 2

Coming to CosyVoice 2, the idea is still the same but now they simplified the architecture, improved it for streaming, instead of VQ they used FSQ
They choose a pre trained LM Qwen2.5 0.5 B model

They removed the x-vector (Speaker embedding) due to information leaking (The speaker embedding sometimes leaking the information about rhythm of a language of the speaker etc. )

They also removed the text encoder which is used to align text with the semantic spaces of the speech because the Qwen2.5 0.5B model is capable enough to do that on its own.

They just tokenized the text in Byte Pair Encoding as BPE gave better results

Now coming to the S3 tokenizer
Instead of keeping a VQ at the middle they kept an FSQ. 
The intermediate outcomes of encoder 1 are projected down to d-dimensions (D is usually 8) and then they are bounded between [-K,K] using tanh function and then rounded off, the outcomes are then projected up and sent into the encoder2 in this way encoder 1 is trained

Using FSQ improves the utilization of the codebook and is much more efficient for LM

So coming to streaming and non streaming
Streaming means for real time speech generation, when we are typing and the model is synthesizing speech as we go but in non streaming i.e. offline use the entire text is given at a time and the speech is generated after that

During streaming we interleave the text and speech tokens for N text tokens we produce M speech tokens and then again N text tokens are given and so on


When the output speech tokens are received we process them in different ways for streaming and non streaming because when non streaming we get all the tokens at a time and we can attend to every speech token for a 


