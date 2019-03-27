# ansible

## 1. 配置文件 /etc/ansible/ansible.cfg

## 2. 并发，超时，委托 

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
