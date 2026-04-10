Modern LLM-based TTS systems can't generate raw waveforms directly. Pipeline they use is
```
Speech->Encoder->Continuous embeddings->Quantization->Discrete tokens
```

# Introduction

Despite success of zero-shot TTS models, they operate in non streaming mode (offline) which involves input of complete text.
This results in high latency.

CosyVoice 2
- Unifies streaming and non streaming synthesis in a single framework 
- Simplified LM architecture by removing text encoder, speaker embedding by allowing pre trained textual large language models to serve as backbone
- Replace vector quantization with finite scalar quantization improving codebook utilization and capturing more speech information
- Upgrades instructed TTS capacity to support more instructions including emotion, accent, role style, fine grained control.


# CosyVoice 2

## Text tokenizer
Uses raw text as input directly

Tokenized using BPE-based text tokenizer - No need for a frontend model that obtains phonemes via graphene-to-phoneme transformation

CosyVoice 2 masks out the one-to-many tokens - prevents pronunciation of a token from becoming excessively large

### BPE

**BPE (Byte Pair Encoding)** is a method used by many LLMs (like GPT-style models) to convert **text into tokens** that the model can process.

LLMs cannot read raw text directly. They operate on **numbers**, so text must first be converted into **token IDs**.

```text → tokenizer → tokens → token IDs → LLM```

Based on vocabulary vocabulary size becomes too large
BPE does watermelon = water+melon
## Supervised Semantic Speech Tokenizer

Rounding
K-ary representation

Operates at 25Hz - 25 tokens per second
![[CosyVoice 2-1772773320220.png]]
## Unified text-speech language model
Remove speaker embedding to avoid information leaking

![[CosyVoice 2-1772773455395.png]]

For non streaming mode:
	S - Start , T - Turn off speech
	After T speech tokens generated
For streaming mode:
	N:M ratio
	For every N text tokens M speech tokens are generated	


## Chunk aware flow matching

Non causal mask
	Used in offline mode where all the tokens are known beforehand
Full causal mask
	Designed for scenarios required in extremely low latency
Chunk-M mask
	Trade off between latency and performance - leverages information of past M and future M tokens
Chunk-2M mask
	Achieve approximate performance of offline mode by sacrificing latency

## Multi speaker fine tuning

Fine tuning the pre-trained model on specific speakers can further improve the generation quality and speaker similarity

mSFT is introduced
	Pretrained model is fine tuned on multiple speakers simultaneously rather than single speaker
	To avoid timbre confusion between various speakers we prepend speaker prompt tags "SpeakerA<|endofprompt|>"
	If training sample is not labled "unknown<|endofprompt|>" is used

## Learning

Speaker Similarity
Word error rate


# Datasets

## Speech tokenizer
Chinese 110,884 hours
English 99,918 hours

## CosyVoice 2
Chinese 130,000 hours
English 30,000 hours
Japanese 4,600 hours
Korean 2,200 hours

## Evaluation settings
LibriTTS test-clean
SEED test sets on 2000 Chinese, 1000 English samples from CommonVoice datasets - test-zh, test-en
![[CosyVoice 2-1772775312391.png]]

# Conclusion

CosyVoice 2 not only delivers superior synthesis quality but also losens deployment requirements making it suitable for both streaming and non streaming applications

# Limitations

It supports only a limited number of languages
For languages with overlapping character sets, synthesis performance may degrade 
CosyVoice can't control acoustic characters like timber, through external textual instructions
Doesn't perform well when tasked with singing





# Re-read

Recent zero-shot models can be broadly divided into three categories:
	Codec language models
	Feature diffusion models
	Hybrid systems ( Codec + Diffusion )

Inspired by advances in image generation below two are introduced into Non-Auto-Regressive(NAR) speech synthesis
	Denoising diffusion
	Flow matching models


## Length of the pronunciation
Early diffusion based TTS models require duration prediction for each text (phone) to address the length disparity between text and speech features.
	This rigidness affected naturalness (Based on emotion we extend some pronunciation and shorten some)	

To mitigate this issue cross attention DiT (Diffusion transformers) have been introduced into NAR TTS models
	Transformer tells the pace of the words based on the context around it


### Newest approach
If we have a 5 word sentence and we want a 5 second audio the model just adds "filler" tokens (padding) until text string is same length as audio

Padding is added based on two methods
											Either
Internal duration prediction module 
	The model looks at text, based on training calculates how much time it takes for a person to say it - eg. 1.7 seconds, per second if 100 frames are generated - 170 frames
	If the text has only 10 characters 160 filler tokens are added
											Or
User control
	User tells the audio has to be 2 seconds long






















Here vector quantization is used traditionally




In CosyVoice 2 we use [[FSQ vs RVQ]]




Recent zero-shot TTS models can be broadly divided into three categories:
	codec language models
	feature diffusion models
	their hybrid systems


CosyVoice 2 contributions :

Unifying streaming and non-streaming synthesis in a single framework and proposing the unified text-speech language model and chunk-aware causal flow matching model, leading to lossless streaming synthesis compared to offline mode.

Simplifying the LM architecture by removing the text encoder and speaker embedding, allowing pre-trained textual large language models (LLMs) to serve as the backbone, enhancing context understanding.

Replacing vector quantization (VQ) in the speech tokenizer with finite scalar quantization (FSQ), improving codebook utilization and capturing more speech information.

Upgrading the instructed TTS capacity to support more instructions, including emotion, accent, role style, and fine-grained control. In CosyVoice 2, the instruction and zero-shot capacity are integrated into a single model, enabling more versatile and vivid synthesis.


![[CosyVoice2-1774081460406.png]]


![[CosyVoice2-1774081608877.png]]
![[CosyVoice1-1774075923424.png]]



# Components

## Text tokenizer
Uses raw text directly which is tokenized using a BPE based text tokenizer

This approach not only simplifies the data preprocessing workflow but also enables the model to learn the pronunciations of words within various contexts in an end-to-end manner


## S3 Tokenizer

The intermediate representations are projected onto a D(8 usually) dimensional low rank space
Values of each dimension are quantized into [-K, K] with the bounded round operation ROUND

More dimensions -> Less dimensions (FSQ Down)
All the values of vector bounded to [ -1, 1 ] then multiplied by number of levels K so [-K, K]
Now each value is rounded to the closes level

Speech token obtained by calculating index of quantized low rank representation using ( 2K + 1 ) ary system

## Unified Text-Speech Language model

**Pre-trained textual LLM, Qwen2.5-0.5B** is used

Speaker embedding is removed to avoid information leaking
We find that such utterance-level vector contains not only speaker identify but also language and paralanguage information, which harms the prosody naturalness and cross-lingual capability of the text-speech LM.


We also abandon the text encoder of the previous CosyVoice
	because we find that the Qwen2.5-0.5B model is powerful enough to align the text and speech tokens, and the text encoder is no longer needed

### Streaming and Non streaming

![[CosyVoice2-1774082895906.png]]


![[CosyVoice2-1774082946112.png]]


Every N Text tokens followed by M speech Tokens  (N = 5, M = 15)

We provide N text tokens, M speech tokens are predicted then one filling token is predicted ( Which means model is waiting for next N text tokens)


#### ICL Non streaming
[S] Prompt text + Text [T]+ Prompt voice (Treated as pregenerated)

#### ICL Streaming

[S] Prompt text  + Prompt voice + text [ N tokens ] [T] -> Continues

#### SFT Non streaming
LM is fine tuned on a specific speaker 
Prompt text, Prompt speech not needed
S, Text, T

#### SFT Streaming
S, Text_N


## Chunk Aware Flow matching

Mel-spectrogram operates at 50Hz
CosyVoice2 operates at 25Hz

So we up-sample the speech tokens by a ratio of 2

Before we up-sample we add an additional look ahead convolution layer to provide future information for the following causal modules

### 1 D Convolution layer
Kernel size = P+1, Padding = P

Eg.
[A,B,C] 
Padding = 1
Kernel size = 1 + 1 = 2

=> [A,B,C,0]
[A,B] , [B,C], [C,0]


## Causal Masks

### Non Causal
All the words can be seen
<u>A</u>,B,C,D
A,<u>B</u>,C,D
A,B,<u>C</u>,D
A,B,C,<u>D</u>

### Full Causal Mask
Only past frames can be attended
<u>A</u>
A,<u>B</u>
A,B,<u>C</u>
A,B,C,<u>D</u>

### Chunk M causal mask
Latency trade off 
M - future and all past tokens
If M = 1

<u>A</u>,B
A,<u>B</u>,C
B,<u>C</u>,D
C,<u>D</u>

### Chunk 2 M causal mask

Approximate offline performance but more latency sacrifice

**For each training case in mini batch we randomly pick one of the masks**






# Limitations
For languages with overlapping character sets - synthesis performance may degrade
Can't control acoustic characteristics like timbre through textual instructions
Doesn't perform well with singing tasks


# Model capabilities

Already Qwen2.5-Omni is there which has a <u>thinker</u> to predict next token and a <u>talker</u>