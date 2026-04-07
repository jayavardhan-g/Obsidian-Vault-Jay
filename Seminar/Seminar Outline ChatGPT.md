Speech in Large Language Models: A Literature Review
# Abstract

Large Language Models (LLMs) have revolutionized natural language processing by enabling powerful text-based reasoning and generation. However, human communication is inherently multimodal, with speech being the most natural and widely used medium. Recent research has focused on integrating speech capabilities into LLMs, allowing them to process and generate spoken language alongside text. This report presents a comprehensive study of key approaches enabling speech in LLMs, including tokenization techniques such as Vector Quantization (VQ) and Finite Scalar Quantization (FSQ), as well as models like SpeechGPT, WavTokenizer, and CosyVoice. The report discusses the challenges of speech representation, alignment, and generation, and analyzes different architectural approaches. It concludes with insights into current limitations and future research directions.

# Introduction

Large Language Models (LLMs) have demonstrated exceptional capabilities in text understanding, reasoning, and generation. Despite their success, these models are inherently limited by their reliance on textual input and output. In contrast, human communication primarily occurs through speech, which carries both linguistic and paralinguistic information such as tone, emotion, and emphasis.

The integration of speech into LLMs aims to bridge this gap by enabling models to process and generate spoken language. This involves handling speech as both an input modality (speech-to-text or direct speech understanding) and an output modality (text-to-speech or speech generation). Such systems are essential for applications like virtual assistants, real-time translation, accessibility tools, and conversational AI.

However, incorporating speech into LLMs presents significant challenges. Speech signals are continuous and high-dimensional, whereas LLMs operate on discrete tokens. Aligning speech with text representations is complex due to differences in temporal structure. Additionally, real-time interaction requires low-latency processing.

This report focuses on key techniques and models that address these challenges, including tokenization methods such as VQ and FSQ, and systems like SpeechGPT, WavTokenizer, and CosyVoice.
# Background
2.1 Fundamentals of Speech

Speech is a continuous acoustic waveform characterized by variations in amplitude and frequency over time. It encodes linguistic content through phonemes and words, while also conveying prosodic features such as pitch, rhythm, and stress. Unlike text, speech is inherently temporal and context-dependent.

2.2 Fundamentals of Large Language Models

LLMs are based on transformer architectures and operate on sequences of discrete tokens derived from text. These tokens are processed using attention mechanisms, enabling the model to capture long-range dependencies. The autoregressive nature of LLMs allows them to generate coherent sequences of text.

2.3 Bridging the Gap Between Speech and Text

A major challenge in integrating speech into LLMs is converting continuous audio signals into discrete representations. This has led to the development of speech tokenization techniques that map audio waveforms to sequences of discrete tokens compatible with LLMs.

Two important approaches are:

Vector Quantization (VQ): This method uses a codebook of discrete vectors to represent continuous inputs. Each segment of the audio signal is mapped to the nearest codebook entry, producing a sequence of tokens.
Finite Scalar Quantization (FSQ): A simpler alternative to VQ, FSQ discretizes scalar values independently, reducing computational complexity while maintaining reasonable representation quality.

These techniques enable speech to be processed similarly to text within LLM frameworks.
# Problem Statements
3.1 Speech Representation

The first challenge is converting raw audio into discrete tokens without losing essential information. The representation must balance compression efficiency with audio quality.

3.2 Compression vs Quality Trade-off

Highly compressed representations reduce computational cost but may degrade speech quality. Conversely, high-fidelity representations require more tokens and computational resources.

3.3 Speech-Text Alignment

Aligning speech tokens with textual meaning is difficult due to differences in temporal and semantic structure. Accurate alignment is essential for tasks such as speech recognition and translation.

3.4 Speech Understanding

LLMs must be able to interpret speech inputs and extract meaningful information. This requires robust representations that capture both semantic and acoustic features.

3.5 Speech Generation

Generating natural and expressive speech involves modeling prosody, speaker characteristics, and emotional tone. This remains a challenging problem.

3.6 Real-Time Interaction

Speech-based systems often require real-time processing. Achieving low latency while maintaining quality is a key challenge.

3.7 Evaluation

Evaluating speech systems involves both objective metrics (e.g., Word Error Rate) and subjective metrics (e.g., Mean Opinion Score), making standardized evaluation difficult.

# Approaches
4.1 Tokenization Approaches

Tokenization is the foundation of speech integration in LLMs.

Vector Quantization (VQ): Widely used in neural audio codecs, VQ provides high-quality representations but can be computationally expensive due to codebook learning and lookup operations.
Finite Scalar Quantization (FSQ): FSQ simplifies the quantization process by discretizing scalar values independently. It offers improved scalability and efficiency, making it suitable for large-scale systems.
WavTokenizer: WavTokenizer is a neural audio tokenizer that converts speech into discrete tokens optimized for LLM compatibility. It achieves a balance between compression and fidelity, making it a practical solution for speech representation.
4.2 Multimodal LLM Approach
SpeechGPT: SpeechGPT represents a unified framework that integrates speech and text within a single LLM. It treats speech as a native modality and supports tasks such as speech recognition, synthesis, and translation. By leveraging instruction tuning, SpeechGPT enables flexible and interactive speech-based applications.
4.3 Speech Generation Approaches
CosyVoice: CosyVoice is a high-quality speech synthesis system designed to generate natural and expressive speech. It focuses on modeling prosody and speaker characteristics, making it suitable as the output component in speech-enabled LLM systems.
# Comparative Analysis

Different approaches to speech integration exhibit distinct strengths and limitations.

Tokenization methods such as VQ and FSQ form the foundation of speech representation. VQ offers high-quality encoding but at higher computational cost, while FSQ provides a more efficient alternative with slightly reduced fidelity. WavTokenizer builds upon these ideas to provide a practical and balanced solution.

SpeechGPT represents a shift toward unified multimodal models, enabling seamless integration of speech and text. However, it requires significant computational resources and large-scale training data.

CosyVoice excels in speech generation, producing natural and expressive audio. However, it is primarily a component in a larger pipeline rather than a standalone multimodal system.

A key trade-off exists between modular and end-to-end approaches. Modular systems allow flexibility and easier optimization of individual components, while end-to-end systems reduce latency and improve overall coherence.
# Discussion

The integration of speech into LLMs is driven by the need for more natural human-computer interaction. Tokenization plays a central role in this process, enabling speech to be represented in a form compatible with LLM architectures.

Recent trends indicate a shift toward unified multimodal models that can handle multiple input and output modalities. However, challenges related to computational cost, data requirements, and evaluation remain significant.

Hybrid approaches that combine efficient tokenization with powerful LLM architectures and specialized speech decoders appear to be the most promising direction.
# Open Challenges

Despite significant progress, several challenges remain:

Achieving real-time performance with minimal latency
Improving speech naturalness and emotional expressiveness
Supporting multilingual and low-resource languages
Reducing computational and data requirements
Developing standardized evaluation metrics
Ensuring robustness to noise and variability
# Conclusion
Speech integration in Large Language Models represents a significant step toward more natural and effective human-computer interaction. Techniques such as VQ, FSQ, and WavTokenizer provide the foundation for representing speech in discrete form, while models like SpeechGPT and CosyVoice demonstrate how speech can be integrated into LLM pipelines.

While current approaches have made substantial progress, the field is still evolving. Future research will likely focus on unified multimodal architectures, efficient training methods, and improved evaluation techniques. These advancements will enable the development of more capable and accessible AI systems.

# References

SpeechGPT: Empowering Large Language Models with Speech Capabilities
CosyVoice: High-quality speech synthesis models
WavTokenizer: Neural audio tokenization methods
Vector Quantization and Finite Scalar Quantization literature
Related works: AudioLM, VALL-E, and multimodal LLM research