```python
sentence -> encode -> ids
model.generate(ids, max_tokens, temperature, top_k)
Generate(ids):
	for i in range max_tokens
		ids = ids if ids.size(1)<= block_size else ids[:,-block_size:]
		logits, _ = self(ids) # self meaning forward function

Forward(ids):
	b, t = ids.size() # b = batch size, t = input sentence size
	pos = torch.arange(0,t) # creates a position vector -> [0,1,2,3, .. , t-1]
	
	token_embeddings = self.wte(ids)
	position_embeddings = self.wpe(pos)
	"""
	self.wte is a layer which connects vocabulary to embeddings 
	
	it acts as a lookup table because connecting a one hot vector an multiplying
	by 0s is waste. So it just outputs the vector of size embedding based on the
	word
	
	self.pte is a layer which connects position to embeddings
		Same as above for position
	"""
	
	x = token_embeddings + position_embeddings	
	
	for block in self.h
		x = block(x)
	
block(embeddings):
	x = layer_norm(x)
	x += self.attention(x)
	x = layer_norm(x)
	x += self.mlp(x)
	return x

attention(x): # CasualAttention module
	b,t,c = x.size() # batch size, no. of tokens, embedding size of each token
	
	self.c_attn= nn.Linear(embedding_size, 3* embedding size)
	#takes the embedding, outputs three times the embedding for each token for each batch
	# if x.size() = (10,10, 20) -> 10 sentences of 10 words each and each word is represented with a vector of size 20
	# it outputs (10,10, 60) -> 10 sentences -> 10 words -> each word has 20 K values, 20 Q values, 20 V values
	
	# split along the last dimension into 3 parts to separate k,q, v
	
	forward(x):
		q, k, v = self.c_attn(embedding_size, 3* embedding_size).split(embedding_size, dim=2)
	
		#dividing the embedding into equal parts so that each head takes some part of the embedding	
		
		q = q.view(b,t,no_of_heads,c//no_of_heads)
		# converts the (Number of sentences, sentence length, embedding size) into (no.of sent, sent length, heads, embeddings/heads)
		# (10,10,20) -> (10, 10, 4, 5) 
		# there are 4 attention heads and each one gets a part of embedding (embedding divided into 4 equal parts of size 5)
		
		k = v.view(b,t,no_of_heads,c//no_of_heads)
		v = k.view(b,t,no_of_heads,c//no_of_heads)
		# // represents division gives integer result instead of the default float
		q= q.transpose(1,2) # brings head to the 1st dimension and token dimension is changed into 2nd
		v= v.transpose(1,2) # brings head to the 1st dimension and token dimension is changed into 2nd
		k= k.transpose(1,2) # brings head to the 1st dimension and token dimension is changed into 2nd
		
		#Now the batches are made such that under each attention 10 words of 5 embedding part comes
		#Batch -> Head -> Tokens -> Embeddings
		#(10,4,10,5)
		
		att = q @ k.transpose(-2,-1)
		# this transposes the last two dimensions. Eg. let's say single attention head -> K = [1,2,3] Q = [4,5,6] it transposes K
		# for each attention we get different attention values. 
		"""
		For the previous example
			10,4,10,5	
			
			q = 10,4,10,5
			k.transpose(-2,-1) = 10,4,5,10
			resulting matrix = 10, 4,10,10 = Batch size, Attention heads, No. of tokens/words, No. of tokens/words
		"""
		att = att.masked_fill(mask_matrix[:,:,T,T]==0),float('-inf')) 
		# for the mask_matrix i.e lower triangular matrix. If matrix value is 0 then it fills -inf in the att result
		"""
		q1k1 , q1k2, q1k3
		q2k1 , q2k2, q2k3
		q3k1 , q3k2, q3k3
		
		
		q1k1 , -inf, -inf 
		q2k1 , q2k2, -inf
		q3k1 , q3k2, q3k3
		
		soft max applied over the row q1, q2, q3 separately
		"""
		
		y = att @ v
		"""
		Attention matrix = B, Heads ,T, T #10,4,10,10
		value matrix = B, Head, T, Divided embedding # 10,4,10,5
		Resulting matrix = B, Heads, T, Divided embedding size # 10, 4, 10,5
		
		q1k1.v1 + q1k2.v2 + q1k3.v3
		q2k1.v1 + q2k2.v2 + q2k3.v3
		q3k1.v1 + q3k2.v2 + q1k3.v3
		
		qxky is a scalar
		v1 is a vector of size (divided embedding)
		
		So the total result is T,v[i].size = T, Divided Embedding size
		
		"""
		
		y = y.transpose(1,2).view(b,t,c)
		#Revert all the dividing into attention
		return y
```


```python

```