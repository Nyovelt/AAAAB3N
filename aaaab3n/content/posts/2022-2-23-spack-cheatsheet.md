---
layout: post
title: "Spack CheatSheet"
date: 2022-2-23  23:33:59 +0800
category: Technology
tags: HPC
---

## spack environment

```bash
# For fish
$ . spack/share/spack/setup-env.fish
```

## spack commands

```bash
spack install -j (nproc) -vvvv  eccodes # spack 安装（或验证某包）并输出详细信息
spack find --path <packages> # 特定包的路径
```

## spack edits

### Environment

```python
def setup_build_environment(self, env):
        spec = self.spec

        # Please specify the location of python
        env.set('PYTHON_BIN_PATH', spec['python'].command.path)

def build(self, env): 
        # get build_dir
        # only works at build stage
        os.environ["RRTMGP_ROOT"] = os.getcwd()
        
```
