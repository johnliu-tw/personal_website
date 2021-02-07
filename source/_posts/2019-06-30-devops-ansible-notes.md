---
layout: post
title: 'Devops - Ansible 設定'
date: 2019-06-30 07:36
comments: true
categories:
- Devops
---
Ansilbe 是基於 Python 的部署工具，
開源，且實現 infra as code 概念的工具，透過 ssh 溝通。

兩大概念: Control Machine, Managed Node
Control Machine 通常代表專門部署的機器，可能是本地的電腦，也可能是 cloud 上高權限機器
Manage Node 通常代表被部署的機器，常是客戶直接面對的機器

### Install

##### Mac os
```
sudo pip install ansible
```
or
```
sudo brew install ansible
```

##### Linux
CentOS
```
$ sudo yum -y install epel-release
$ sudo yum -y update
$ sudo yum -y install ansible
```

Ubuntu
```
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```

### Setting

#### 第一層
設置 playbook.yaml，格式為 yaml。
hosts 會讀取 server 位置，可利用 inventory
roles 為設定會跑的子 task 檔，path 通常是在根目錄建立 roles
tasks 為執行的指令

#### Task 層
name 為該 task 的名字
shell 內為該 task 執行的指令
register 則將該指令的結果給儲存下來在變數 `jenkins_url_result` 
再來可以在下個 task 中根據此變數做調整。

```
- hosts: server
  roles:
    - { role: docker, become: yes }
  tasks:
    - name: test connection
      ping:
      register: message

    - name: print debug message
      debug:
        msg: "{{ message }}"
    - name: print folder tree
      shell: "ls -lh"
    - name: Wait for jenkins to come up
      register: jenkins_url_result
```

設置 inventory 檔案，為 playbook.yaml 內 server 的 config 值
格式為:
[伺服器名稱]
{managed node 位址} ansible_user={登入身份}
```
[server]
172.0.0.1 ansible_user=root
~
```

### 部署指令
```
ansible-playbook -i inventory playbook.yml
```

### Config 設定
在根目錄下建立 ansible.cfg 檔案
```
[default]
inventory = inventory
roles_path = roles
```
path 的寫法為相對路徑
