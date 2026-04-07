# Introduction
## Main Problems
- Architectural complexity - Models require multiple stages for processing or "parallel streams" => Makes them slow and hard to integrate into standard text based LLMs
- Poor granular control - Difficult for users to precisely control pitch, gender or speaking rate. Only mimics the voice but can't perform tasks like "Make this voice 10% deeper"

## BiCodec
SparkTTS is based on BiCodec
Instead of turning speech into one messy stream of data BiCodec breaks them into two types
	Semantic Tokens - Low bitrate tokens which capture what is being said (meaning)
	Global Tokens - Fixed length tokens that capture who is saying it (Timbre, identity)
## Single stream
Both the tokens are in a single stream - so the model can function like standard text LLM (Qwen2.5)


# Chain of Thought
First predicts style or attribute (Gender, pitch)
Then those predictions are used for generation of the actual speech tokens
==This allows for both coarse grained(gender, pitch) and also fine grained(exact pitch value or talking speed)==

## VoxBox Dataset
Released 1,00,000 hours of speech with data like gender, pitch, speed is released by the authors

English 20,000+ hours
Mandarin 35,000+ hours
Other 45,000 hours include wide variety of other open source corpora


# Related works

## Multistage problems
Models like CosyVoice often require separate stages - one to generate the content and another refiner
Speech Tokens are the content and Optimal Flow matching model is the refiner


# BiCodec

50 tokens/sec capturing linguistic content
Fixed length global tokens - encoding speaker attributes and other global speech characteristics

![[SparkTTS-1775544633201.png]]

The BiCodec architecture follows a standard VQ-VAE encoder-decoder framework, augmented witha global tokenizer.

x = [-1,1]^{T}
![[SparkTTS-1775544776314.png]]

x - input audio with sample number of T
Es - Encoder of semantic tokenizer
F - pretrained wav2vec
Eg - Encoder of global tokenizer
Mel - Extract MelSpectrogram from x
h - sequence of learnable queries matching the length of the final global token sequence 
Qs - quantization layer with VQ
Qg - quantization layer with FSQ
Ag - Aggregation module with a pooling layer
G - decoder which reconstructs time-domain signal


## Model Structure

### Encoder and Decoder
Encoder of semantic tokenizer(Es) and decoder (G) are fully convolution neural networks built with ConvNeXt Blocks
Capture semantic information based on relationship between different layer features of wav2vec 2.0 and semantics 
	11th, 14th and 16th layers features are selected and averaged
		This serves as the input for semantic tokenizer
	11th + 14th layer features - strong correlation with words
		16th layer features - strongest correlation with phonemes

Global tokenizer's encoder Eg uses ECAPA-TDNN architecture following the implementation by Wespeaker upto final pooling layer
After encoding the global tokenizer extracts a fixed length representation gf using cross-attention mechanism with a set of learnable queries

### Quantization 
Semantic tokenizer employs single codebook vector quantization
Similar to DAC we use factorized codes to project the encoder's output to a low dimensional latent variable space before quantizing

Considering that the global tokenizer requires a set of discrete tokens to represent time-independent global information, FSQ is employed rather than VQ to mitigate the potential risk of training collapse associated with VQ



# Language modeling of SparkTTS

SparkTTS model adopts decoder only transformer architecture unified with typical textual language model
Employed pretrained Qwen2.5 0.5B model
Unlike CosyVoice we don't need another Optimal Flow Matching model to generate audio
	Instead we use decoder of BiCodec

## Tokenizer
### Text Tokenizer
BPE tokenizer similar to textual LLMs - Qwen2.5 tokenizer which supports many languages

###  Attribute Tokenizer 
To enable voice creation based on speech attributes, Spark-TTS encodes attribute information at two levels: 
- Coarse-Grained: Attribute labels representing high-level speech characteristics, including gender, pitch (categorized into five discrete levels), and speed (categorized into five discrete levels)
- Fine-Grained: Attribute values enabling precise control over pitch and speed, which are quantized by rounding to the nearest integer during tokenization.

### Speech Tokenizer 
The speech tokenizer consists of a global tokenizer and a semantic tokenizer. Us-ing both global and semantic tokens, the BiCodec decoder reconstructs the waveform signal.


![[SparkTTS-1775546533105.png]]
















