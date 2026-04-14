$\phi(n)$ = Count of numbers from 1 to n which are coprime with n

==**Co-prime**== : The GCD of two numbers is 1

$\phi(1)$ = 1 (1)
$\phi(2)$ = 1 (1)
$\phi(3)$ = 2 (1,2)
$\phi(4)$ = 2 (1,3)
$\phi(8)$ = 4 (1,3,5,7)

**For a prime number all the numbers coming before it are co-primes**  
=> $\phi(p)$ = p-1 

**For square of a prime number**
=> $\phi(p^2)$ = $p^2 - p$ 
	Out of p^2 numbers ->$p\times 1$  , $p\times 2$ , $p\times 3$ , $p\times 4$ ,..., $p\times (p-1)$ , $p\times p$
	Except these numbers all others are co-prime ^3fd976

**For power k of a prime number** $\phi(p^k)$
	For $p^k$ the number of factors are $p^k/p$ 
		= $p^{k-1}$
		Eg. $p^2$ factors are $p^2/p = p$ [[ETF - Euler Totient Function#^3fd976]]
		$p^3$ factors are $p^3/p = p^2$
	$\phi(p^k) = p^k - p^{k-1}$ 


$\phi(p^k) = p^k - p^{k-1}$
$\phi(a\times b) = \phi(a) \times \phi(b)$
$\phi(p1^{k1} \times p2^{k2} \times p3^{k3} ... \times pn^{kn}) = \phi(p1^{k1}) \times \phi(p2^{k2}) \times \phi(p3^{k3}) \times \phi(pn^{kn})$
$\implies (p1^{k1} - p1^{k1-1}) \times (p2^{k2} - p2^{k2-1}) ... \times (pn^{kn} - pn^{kn-1})$
$\implies (p1^{k1}\times p2^{k2} \times ... \times pn^{kn}) \times (1- 1/{p1})(1-1/{p2})...(1-1/{pn})$
$\implies n \times (1-1/{p1})(1-1/{p2})...(1-1/{pn})$

This implies for any number N
$\phi(N) =  N \times \prod_i^{j}(1-1/{pi})$
Where pi is a prime divisor of N

```c++
phi[N];
for(i=2;i<N;i++)phi[i] = i;

for(int i=2;i*i<N;i++){
	if(phi[i]==i){
		for(int j=i;j<N;j+=i){
			phi[j]=(phi[j]-phi[j]/i);
		}
	}
}
```

