## Vector quantization drawbacks

Codebook collapse : only 200 codes used out of 1024 codes
Large memory : Large codebooks = More parameters
Hard to scale : When scaling billions of tokens or multilingual data VQ becomes inefficient
If a vector is of size d and each dimension has k-levels then total tokens = $k^d$
But we don't store a codebook

FSQ is used in [[CosyVoice 2]]
# Example
$z = [1.2, -0.7]$
$d=2$
## RVQ
*Codebook 1*
$C1 = [1, 0]$
$C2 = [0, -1]$
$C3 = [1, -1]$

*Codebook 2*
$D1 = [0.3, 0]$
$D2 = [0, -0.3]$
$D3 = [0.2, -0.2]$

Step 1 : C3 
	$Residue =[0.2,0.3]$
Step 2 : D1


RVQ approximation
	C3 + D1  = $[1.3,-1]$

## FSQ

$L = [-2,-1,0,1,2]$

dim1 = 1 : L3
dim2 = -1: L1

Now convert it to BASE SizeOf(L) encoding
	$3*5^1 + 1*5^0 = 16$
	
The 16 is passed


# RVQ vs FSQ

RVQ have skewed distribution
- The usage of each code in very variable, most codes have very less token usage
	token 45 → 12%
	token 18 → 9%
	token 332 → 7%
	many tokens → <0.01%
	This creates a long tail distribution
- LLM sees these tokens rarely and can't train efficiently

- RVQ tokens are hard to predict
	No meaningful structure between tokens
	Small changes in vector may change the first codebook itself in RVQ but in FSQ the small changes will result in less drastic changes to the result

