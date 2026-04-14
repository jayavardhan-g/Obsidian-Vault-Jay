# Writing technical Articles
Process of writing and sharing information in professional manner
Clear, complete and effective manner
Easy to understand and read

## Structure
Beginning -> Middle -> End
Introduction -> Body -> Conclusion
(Technical article must never tell a suspense story)


- Tell them what you are going to tell them
- Tell them
- Tell them again what you told them


**Abstract**
**Introduction**
**Literature review**
**Methods**
**Results and discussion**
**Conclusion**
**References**

The 'write' order
- 1. Methods 2. Results
- 3. Discussions 4. Introduction
- 5. Title 6. Abstract

### Methods
Subheadings
Present and past tense
New methods must be described in sufficient detail
Established methods can be referenced
Save time and effort


### Results
Accurate brief clear
Subheadings
Past tense to describe your results
	When referring to figures and tables, use present tense
	Not for explaining results
Don't duplicate data among figures, tables and text
Present large amount of data efficiently and quickly
Present most significant result as a figure or table
Keep it simple - use separate panels if necessary
Label all parts
All figures must have legends
### Discussions
**So what?**
What do these findings mean?
Discussion -> Beginning -> Middle

**Beginning**
	Avoid repeating the results section
	Answer the research question posed
	Emphasize the major findings first
	What is your major conclusion, based on results you have presented?
	







# Presentation

## Speech in LLMs

Hi, I'm gonna present on the topic Speech Modality in LLMs. The traditional way of processing speech in LLMs is through a pipeline with an ASR and TTS. This method is not preferred because at the ASR the paralingual information is lost and also with only text tokens the TTS voice also seems mechanical and not natural. The pipeline method increases latency, need for computation resources and complexity. So instead of converting speech to text and text to speech we focus on a model which treats both text and speech as primary modalities - SpeechGPT. For this SpeechGPT treats speech tokens as its own extended vocabulary and text along with its speech tokens are used for training. In this way the LLM learns reasoning similar to text even in speech and can input/output speech/text. 
The problem with this is that to align text and speech the speech tokens have to be limited so they undergo extreme compression to get only few speech tokens per text. This results in semantic speech tokens and doesn't retain paralingual information. Although we were successful in making a model which inherently processes speech we failed at retaining paralingual information. In view of this limitation that speech tokens don't contain paralingual information we now look at the cosy voice paper which addresses the issue. Instead of extremely compressed speech tokens we extract speech tokens from an existing ASR model. This helps in two ways - first the tokens are supervised as ASR is trained in a supervised manner, second looking at the architecture we can say that the tokens contain both acoustic and semantic details as the tokens are extracted from the middle of the encoder. Now cosy voice is essentially a controlled tts model which uses LLM - and for this the model contains various modules - it uses a separate module to get the voice print of the speaker, uses LLM to predict the next speech token from the text we give, and then another module to generate audio from the speech tokens and voice print. Another such model is the SparkTTS model. Spark TTS model trains a bicodec on the voice and extracts speech and global tokens and on these tokens a decoder is trained to reconstruct the audio. In this the speech tokens are extracted from 3 layers of the wav2vec tokenizer. Both of these models suggested a huge advancement of using speech tokens from ASR like models which retain semantic as well as acoustic details. But latency is still not reduced. Both these models wait till all the speech tokens are produced and then give you the audio. For a step towards AGI we still need to reduce the latency. The researchers of cosyvoice took forward a step in reduction of latency by introducing cosyvoice2 with chunk aware processing. What they do is wait till the model produces a set of M speech tokens and then processes the tokens through attention with all previous tokens to produce a short audio clip. This process is repeated every time the model produces M tokens so we hear the text-to-speech audio on the go as we type.





























































