# ansible

## 1. ansible 基本概念

* 配置管理工具

* 幂等性:结果为导向

* 管理端安装ansible，客户端无需任何agent，只需要按照ssh

* ansible 常见使用方式

`172.30.81.193 ansible_user=root ansible_ssh_pass=1`

```
ansible <target> -m <module> -a <args>

ansible-playbook playbook.yaml

ansible-doc show <module-name>
```

## 2. ansible常见模块

* 命令模块 （command，shell，script）

* 文件模块（file，fetch，copy）

* 包模块（yum，apt）

* 系统模块（ping，cron，user，group，setup，service）

## 3. ansible playbook

* 多hanler

```
---
- hosts: 172.30.81.193
  user: root
  serial: 5
  gather_facts: false
  vars:
    - name: xj
    - id: 1
  tasks:
    - name: shell
      shell: touch xiaotech
      notify: 
        gtask
  handlers:
    - name: show msg1   # 单handler
      listen: gtask   # 多handler
      debug:
        msg: changed1
    - name: show msg2
      listen: gtask
      debug:
        msg: changed2
```

* tags 

*执行tags ansible-play t.yaml tags="t1,t2"*

*跳过tags ansible-play t.yaml --skip-tags="t1,t2"*


## 变量

* vars指定变量

```
---
- hosts: 172.30.81.193
  user: root
  serial: 5
  gather_facts: false
  vars:
    - name: xx
    - id: 1
```

* 变量文件分类

```
---
- hosts: 172.30.81.193
  user: root
  serial: 5
  gather_facts: false
  vars_files:
    - file_path
```

* 注册变量

```
- name: shell
  shell: pwd
  register: result
- name: show result
  debug:
    msg: "{{result.stdout_lines}}"
```

* 命令行传入变量 -e key=value

* 交互式输入变量

```
---
- hosts: 172.30.81.193
  user: root
  serial: 5
  gather_facts: false
  vars_prompt:
    - name: "name"
      prompt: "Your name"
  tasks:
    - name: show
      debug:
        msg: "{{name}}"
```

## 4. 并发，超时，委托 

*超时*

```
---
- hosts: all
  user: root
  gather_facts: false
  tasks:
    - name: test
      shell: sleep 5
      async: 3   # 设置每次轮寻的超时时间，0代表一直等待
      poll: 2    # 设置轮寻的间隔，0代表不轮寻，直接成功结束
```

*并发*

```
---
- hosts: all
  user: root
  serial: 3      # 设置一次执行多少个
  gather_facts: false
  tasks:
    - name: test
      shell: sleep 5
```

*委托*

```
---
- hosts: all
  user: root
  gather_facts: false
  tasks:
    - name: test
      shell: sleep 5
      delegate_to: 172.30.81.194 #委托哪一台执行
      run_once: true  # 设置只执行一次，多个委托，只执行一次
```
