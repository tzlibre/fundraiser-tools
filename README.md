# Command-line tools 

This repository contains **Command-line tools** to generate proof of ownership for [TzLibre sign process](https://tzlibre.github.io/sign.html).

These tools are based on a fork of DLS [`fundraiser-tools`](https://github.com/tezos/fundraiser-tools).

The **Command-line tools** support you in:

1. recreate your tezos public key (namely "**Tezos public key**") in order to be able to verify digital signature
2. generating the digital signature of an ethereum address (namely **Cryptographic signature**) to prove ownership of a Tezos private key and therefore the right to receive TZL coins

To improve usability and security a sandboxed execution environment is provided as a Docker container, along with an invocation bash script.

This code has been informally audited [here](https://www.reddit.com/r/crypto/comments/8mygjg/is_pysodium_python_libsodium_ecdsa_safe/)

## Security

As a standard security practice when handling sensitive data you must:
1. Read the whole source beforehand and be fully responsible for the code you run on your computer.
2. Verify the computer you're using is secure and not compromised.

## Table of Contents

- [Repository](#repository): description of the directories and files of the repository
- [Signature generation](#signature-generation): instructions to generate your **Cryptographic signature**
- [Tech details](#tech-details): technical details of the tools used in the sandboxed execution environment

## Repository

This repository contains the following directories and files:

- `Dockerfile`: instructions to create the Docker-based sandboxed execution environment. A Docker environment increases safety and stability, and allows the user to avoid installing dependencies or configuring an environment.
- `gen-signature.sh`: Bash script that improves usability and security by interactively asking the user for inputs while running the `pykeychecker` tool. 
- `pykeychecker`: written in Python, this is an extended version (you can verify the [4 diffs](https://github.com/tezos/fundraiser-tools/compare/master...tzlibre:master?diff=split&name=master]#diff-e6c8e5e03826917a611ce5c5e23626fc)) of the broken DLS [`fundraiser-tools`](https://github.com/tezos/fundraiser-tools) which it also fixes. This is the only part of the code that interacts with private and sensible data. It then generates the signature for your Ethereum address. 


## Instructions

### Inputs
This information is required:

- "**Tezos public key hash (a/k/a Tezos address)**". You can find it in the PDF containing your contribution data.
- "**15 words secret**". You can find them in the PDF containing your contribution data.
- "**Email**" used when contributing to the Tezos ICO. You can find it in the PDF containing your contribution data.
- "**Password**" set during Tezos ICO.
- "**Current Ethereum address**, the Ethereum address you want to sign (and where we will airdrop TZL ERC20 tokens).

### Outputs
At the end of this process you'll get the following outputs to be copy-pasted in the [sign form](https://tzlibre.github.io/sign.html):

- "**Tezos public key hash (a/k/a Tezos address)**"
- "**Current Ethereum address**"
- "**Tezos public key**"
- "**Cryptographic signature**" (of the "Current Ethereum address")

### 1. Sandboxed execution environment generation
To improve security, simplify environment configuration and avoid installing dependencies, you can build a Docker image starting from our `Dockerfile`. 

The `Dockerfile` creates an environment and then prepares it to run `gen-signature.sh`. You can read the content of the file to verify its behavior.

#### 1.1 Install Docker
You'll need a working Docker environment. Docker installation instructions can be found [here](https://www.docker.com/community-edition#/download).

#### 1.2 Get the code

Clone the **Command-line tools** repository from GitHub:

```sh
git clone https://github.com/tzlibre/fundraiser-tools.git
```

then move to the repository folder:

```sh
cd fundraiser-tools
```

#### 1.3 Build the `tzlibre/cl-tools` Docker image
Once you cloned the repository and have Docker up and running, you should issue:

```sh
docker build . -t tzlibre/cl-tools
```

> Depending on your system configuration you might need to run Docker with admin privileges: (e.g. `sudo docker build . -t tzlibre/cl-tools`).

At the end of the build, you should see this message:

```sh
Successfully tagged tzlibre/cl-tools:latest
```

or

```sh
Successfully built <some-hex-numbers>
```

These messages confirm the environment has been successfully built.

### 2. Run Docker container to produce the signature

You have now built a Docker image that can use the `gen-signature.sh` script. 

> **THIS STEP WILL REQUIRE TO INPUT YOUR SENSITIVE INFORMATION. WE SUGGEST TO DISCONNECT FROM ALL NETWORKS BEFORE CONTINUING.**

To generate the signature issue this command and follow on screen instructions:

```sh
docker run -it --rm tzlibre/cl-tools
```

> According to your system configuration you might need to run Docker with admin privileges: (e.g. `sudo docker run -it --rm tzlibre/cl-tools`).

Here is a full usage example:

```sh
$ docker run -it --rm tzlibre/cl-tools

 *********
 * INPUT *
 *********
   
15 Word Secret Key:
word1 word2 word word4 word5 word6 word7 word8 word9 word10 word11 word12 word13 word14 word15
   
Email:
your@email.com
   
Password:
yourPassword
   
Current Ethereum address:
0x1234567890123456789012345678
   
   
 **********
 * OUTPUT *
 **********
	  
Tezos public key hash (a/k/a Tezos address):
tz1bG4r2PkzsxARQFnScVr51NaXrcVdF1Myg

Current Ethereum address:
0x1234567890123456789012345678
	  
Tezos public key:
012f1e1c8ed3eea205c75323c6db7f2a74b3273921c06a6629056331612d275e
	  
Cryptographic signature:
1a9871ca4357ef82ab8b427e428e1f430c78755f4d15b826d89bfc57e0309e3f468b5ea5b73921138f9a5e3d132a995c7bacd5a8a50800589e29232382e66c0d
	  
```

> If the field "**Tezos public key hash (a/k/a Tezos address)**" provided in output matches the one you find in your Tezos contribution PDF, the procedure has been completed successfully. 

- - -

## Tech details

This section explains technical details of the tools used in the sandboxed environment. It'll help you understand what happens under the hood: although the sendboxed environment enhances usability and security (and we encourage you to use it), you might choose to separately run each tool to produce the required signature.

### `pykeychecker`

The `pykeychecker` is a fork of DLS [`fundraiser-tools`](https://github.com/tezos/fundraiser-tools) which fixes a broken dependency in the DLS repo. It also upgrades it to sign your Ethereum address and the declaration reported above. You can check the [code diff](https://github.com/tezos/fundraiser-tools/compare/master@%7B1day%7D...tzlibre:claim#diff-e6c8e5e03826917a611ce5c5e23626fc)). Note that this is the only code that interacts with private and sensitive data.

#### Install system dependencies

- [Python 2.x](https://www.python.org/) (it is installed by default on many systems)
- [libsodium](libsodium.org) (on OSX via brew: `brew install libsodium`, on Ubuntu: `sudo apt install libsodium-dev`)

#### Install package dependencies

```sh
cd pykeychecker; pip install -r requirements.txt; cd ..
```
#### Run `pykeychecker` and save the output in a file named `keychecker_output.txt`

```sh
python pykeychecker/keychecker.py <15_words_mnemonic_seed> <your@email.com> <your_P4s5w0rd> <0x...yourEthAddress> > keychecker_output.txt
```
> If your password contains special characters (e.g. `&`, `\`, `|`, `!`, etc.), encapsulate it with `'`. 
> If your password contains `'`, prepend `'\'` (e.g.`I'mapassword -> 'I'\''mapassword'`).

Here is a usage example:

```sh
python pykeychecker/keychecker.py word1 word2 word word4 word5 word6 word7 word8 word9 word10 word11 word12 word13 word14 word15 your@email.com yourPassord 0x1234567890123456789012345678
```

#### Verify the computed Tezos address 

Open `keychecker_output.txt` and verify that the computed Tezos address is correct.

```sh
cat keychecker_output.txt | grep TZL_addr
```

If it does not match your Tezos address, please check input parameters again.

### `gen-signature.sh`

Instead of running directly `pykeychecker`, we suggest to use the `gen-signature.sh` script to improve usability and security. 

To do so, issue the command and follow the provided instructions:

```sh
bash gen-signature.sh
```

Here is an example:

```sh
$ bash gen-signature.sh

 *********
 * INPUT *
 *********
   
15 words secret key:
word1 word2 word word4 word5 word6 word7 word8 word9 word10 word11 word12 word13 word14 word15
   
Email:
your@email.com
   
Password:
yourPassword
   
Current Ethereum address:
0x1234567890123456789012345678
   
   
 **********
 * OUTPUT *
 **********

Tezos public key hash (a/k/a Tezos address):
tz1bG4r2PkzsxARQFnScVr51NaXrcVdF1Myg

Current Ethereum address:
0x1234567890123456789012345678
	  
Tezos public key:
012f1e1c8ed3eea205c75323c6db7f2a74b3273921c06a6629056331612d275e
	  
Cryptographic signature:
1a9871ca4357ef82ab8b427e428e1f430c78755f4d15b826d89bfc57e0309e3f468b5ea5b73921138f9a5e3d132a995c7bacd5a8a50800589e29232382e66c0d

```

We encourage you to read `gen-signature.sh` content to verify its behavior.
