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



























