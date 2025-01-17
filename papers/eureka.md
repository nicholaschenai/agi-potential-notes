---
date: 2024-08-04
time: 09:03
author: 
title: "Eureka: Human-Level Reward Design via Coding Large Language Models"
created-date: 2024-08-04
tags: 
paper: https://arxiv.org/abs/2310.12931
code: https://github.com/eureka-research/Eureka
zks-type: lit
site: https://eureka-research.github.io
---
Iterative process where LLM designs reward functions, evaluates in RL env and reflects to design better reward functions
## Description of result
- benchmark: 29 RL environments across IsaacGym and Dexterity
- EUREKA outperforms human experts on 83% of the tasks, leading to an average normalized improvement of 52%. (main model: GPT-4-0314)
- Solves dexterous manipulation tasks that were previously not feasible by manual reward engineering (eg shadow hand pen spinning)
- Can also incorporate human feedback, combining Eureka with human feedback performs the best
- GPT 3.5: usually matches humans and occasional beat

---
## How it compares to previous work
Language to rewards (Yu et. al. 2023)

---
## Main strategies used to obtain results
![](assets/Pasted%20image%2020240804090510.png)

![](assets/Pasted%20image%2020240927165944.png)

In simpler terms:

training
- for $N$ (=5) iterations
	- prompt LLM $K$ (=16) times to get $K$ reward functions (temp=1) (reward function might have errors so multiple samples increases the chances of at least one executable reward fn). prompt inputs:
		- reward fn from previous iter best (Markov)
		- reflection over reward
		- env code (excluding reward code) / state info -- potential limitation
			- has automatic scripts to extract impt parts
		- mutation prompt

	- in parallel, run $K$ trials and pick the best
	- based on best reward fn and results, LLM reflection to try and improve reward model
	- update global best

eval
- For _ iterations
	- take global best reward fn and train model
- average best score across checkpoints


---

## Other

### Practical notes (esp when running on WSL)

#### isaacgym
##### Installation
in `isaacgym/python/isaacgym/torch_utils.py` and replace `np.float` by `np.float64` at line 135 as it causes errors later during Eureka training

##### when testing with `joint_monkey.py`

rather than call examples/joint_monkey.py
do
```bash
cd examples
python joint_monkey.py
```

as it imports some config stuff with relative paths.

**Error:**

```console
# ImportError: libpython3.8.so.1.0: cannot open shared object file: No such file or directory
```

solve with

```bash
export LD_LIBRARY_PATH=/PATH/TO/ANACONDA/envs/eureka/lib:$LD_LIBRARY_PATH
```

**Theres also this error** due to `libcuda.so` for windows host stubbed inside WSL2

```console
/buildAgent/work/.../source/physx/src/gpu/PxPhysXGpuModuleLoader.cpp (148) : internal error : libcuda.so!
```

solve with

```bash
cd /usr/lib/wsl/lib
sudo rm libcuda.so libcuda.so.1
sudo ln -s libcuda.so.1.1 libcuda.so.1
sudo ln -s libcuda.so.1 libcuda.so
sudo ldconfig
```


**the LD library path**'s `libstdc++.so.6` GLIBCXX does not contain ver GLIBCXX_3.4.32. you can check this via 
```bash
strings PATH/TO/LIBSTDC | grep GLIBCXX
```

the default one in ` /usr/lib/x86_64-linux-gnu/libstdc++.so.6` does, so copy that over to the conda env

**Error: segmentation fault**

>  WSL's graphics support, especially for complex OpenGL applications like IsaacGym, can be unstable. The segmentation fault occurs when both the graphics pipeline and viewer are trying to access the GPU through WSL's graphics translation layer.

main prob is the viewer -- comment out everything to do with it

#### Eureka installation
- comment out the numpy requirements in eureka cos need numpy 1.21 n above (package used 1.20) for the NDARRAY type which is used during Eureka training

#### Eureka training
This dependency was missed
```bash
pip install gpustat
```

##### On args
- Note the default settings are in env/config which is different from README!
- `capture_video` appears to always result in error? maybe due to the same display error in WSL as above?
- `num_eval` (evaluating the final reward fn) is run in **parallel** with a default of 5, takes 8GB VRAM so total 40GB VRAM -- if not enough, adjust that!

#### Pen spinning demo
Rmb to run in headless mode!
