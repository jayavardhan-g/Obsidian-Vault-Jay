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


