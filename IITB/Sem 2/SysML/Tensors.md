# Creating
```python
x = torch.zeros(2,3)
x = torch.ones(2,3)
x = torch.empty(2,3)
x = torch.rand(2,3)

torch.manual_seed(34)
x = torch.rand(2,3)
torch.manual_seed(34)
x = torch.rand(2,3)
```

# Copy shape
```python
torch.zeros_like()
torch.ones_like()
torch.rand_like()
torch.empty_like()
```
No need to enter shape in the brackets but instead enter a tensor whose shape you want to copy

# Change datatypes 
```python
x = torch.ones((2,3), dtype= torch.float32)
x = torch.zeros((2,3), dtype= torch.int64)
y = x.to(torch.int32)
```

# Arithmetic
```python
x = torch.ones(4,5)
x = x*2
x = x**2
x = (x - 7)/3
x = x+1
```


# From data
```python
x = torch.tensor((2,5,56))
x = torch.tensor([2,5,5])
x = torch.tensor(((2,34,5),[2,5,6]))
```
torch.tensor copies the data
























