# Environments
```bash
conda env list
```
Get a list of present environments


```
conda create --name myenv python=3.10
```
Create a new environment

```
conda activate myenv
```
Activate virtual environment


```
conda remove --name my_old_env --all
```
Remove the VE
# Installing global Jupyter Lab

Installing Jupyter Lab in every environment is a waste of space so we install Jupyter lab and notebook in the base environment and install all other project specific libraries in other project specific environments

```
conda activate base
conda install jupyterlab
```

```
conda activate myenv
conda install ipykernel
```

```
python -m ipykernel install --user --name myenv --display-name "Python (My Project)"
```

To remove a kernel go into the base environment
```
jupyter kernelspec list
jupyter kernelspec uninstall my_old_env
```


# CUDA Compatible
```
conda install pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
```

```
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
```

Conda or Pip









