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

































