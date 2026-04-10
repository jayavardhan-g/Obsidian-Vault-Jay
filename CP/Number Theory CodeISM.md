x % m if x is negative
	x % m = abs(x)%m * -1 + m 


# Some important techniques
## Binary exponentiation

```c++
int binaryExponentiation(int x,int n,int m) // x^n   O(logn)  
{  
	if(n==0){  
		return 1;  
	}  
	if(n%2==0){  
		return binaryExponentiation(((x%m)*(x%m))%m,n/2,m);  
	}  
	return ((x%m)*binaryExponentiation(((x%m)*(x%m))%m,(n-1)/2,m)%m)%m;  
}
```

## Finding GCD

```c++
int GCD(int a, int b){
	if(b==0)return a;
	
	return GCD(b, a%b);
}

```

## Finding LCM

$LCM(a,b) \times GCD(a,b) = a\times B$
Find GCD and then LCM
```c++
__gcd(a,b); // Gives GCD of a,b -> Inbuilt function
```

## Finding Prime - Sieve of Erasthones


```c++
isPrime[1]=0;  
isPrime[0]=0;  
for(int i=2;i*i<=1000000;i++){  
	if(isPrime[i]==1){  
		for(int j=i*i;j<=1000000;j+=i){  
			isPrime[j]=0;  
		}  
	}  
}
```
**Time complexity = $O(n \times log(log(n))$**

# Important built in functions

```c++
pow(n,x); // Finds n raised to the power x
sqrt(n); // Finds square root of n
```


# Important properties

Modulus
1. $(a+b)\%m =  ((a\%m)+(b\%m))\%m$
2. $(a-b)\%m = ((a\%m)-(b\%m)+m)\%m$
3. $(a*b)\%m = ((a\%m)*(b\%m))\%m$
4. $(a/b)\%m = ((a\%m)*(b^{-1}\%m))\%m$


 If $(a-b)\%k = (x-y)$ then $(a-x)\%k = (b-y)\%k$

