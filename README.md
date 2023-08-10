# cardano_installation
This repo is the cardano_installation guide  develop by Rushi Chavan

---

**How to Set Up and Run a Cardano Test and Main Node for Development**

🔧 **Step-by-Step Guide to Cardano Development Environment** 🔧

---

**1. Preparing the System**:
```bash
sudo apt-get update -y && sudo apt-get upgrade -y
sudo apt-get install automake build-essential pkg-config libffi-dev libgmp-dev libssl-dev libtinfo-dev libsystemd-dev zlib1g-dev make g++ tmux git jq wget libncursesw5 libtool autoconf -y
```

**2. Installing Haskell**:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```
*Note*: Follow the on-screen instructions to set up ghc and cabal, then proceed:

```bash
ghcup install ghc 8.10.7
ghcup set ghc 8.10.7
ghcup install cabal 3.6.2.0
ghcup set cabal 3.6.2.0
```
Verify installation:
```bash
ghc --version
cabal --version
```

**3. Cardano Dependencies**:
```bash
mkdir -p $HOME/cardano-src
cd $HOME/cardano-src
git clone https://github.com/input-output-hk/libsodium
cd libsodium
git checkout dbb48cc
./autogen.sh
./configure
make
sudo make install

# Repeat libsodium installation (as per the request)
git clone https://github.com/input-output-hk/libsodium
cd libsodium
git checkout dbb48cc
./autogen.sh
./configure
make
sudo make install
```
Set environment paths:
```bash
export LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
export PKG_CONFIG_PATH="/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH"
```

**4. Secp256k1 Library Setup**:
```bash
cd $HOME/cardano-src
git clone https://github.com/bitcoin-core/secp256k1
cd secp256k1
git checkout ac83be33
./autogen.sh
./configure --enable-module-schnorrsig --enable-experimental
make
make check
sudo make install
```

**5. Cardano Node Installation**:
```bash
cd $HOME/cardano-src
git clone https://github.com/input-output-hk/cardano-node.git
cd cardano-node
git fetch --all --recurse-submodules --tags
git checkout $(curl -s https://api.github.com/repos/input-output-hk/cardano-node/releases/latest | jq -r .tag_name)
cabal configure --with-compiler=ghc-8.10.7
```

**6. LLVM Setup**:
*Important for llvm*:
```bash
bash -c "$(wget -O - https://apt.llvm.org/llvm.sh)"
wget https://apt.llvm.org/llvm.sh
chmod +x llvm.sh
sudo ./llvm.sh all
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -
sudo add-apt-repository "deb http://apt.llvm.org/focal/ llvm-toolchain-focal-9 main"
sudo apt-get update
sudo apt-get install -y llvm-9 llvm-9-dev
sudo apt install clang-9 libnuma-dev
sudo ln -s /usr/bin/llvm-config-9 /usr/bin/llvm-config
sudo ln -s /usr/bin/opt-9 /usr/bin/opt
sudo ln -s /usr/bin/llc-9 /usr/bin/llc
sudo ln -s /usr/bin/clang-9 /usr/bin/clang
cabal update
cabal build all
mkdir -p $HOME/.local/bin
cp -p "$(./scripts/bin-path.sh cardano-node)" $HOME/.local/bin/
cp -p "$(./scripts/bin-path.sh cardano-cli)" $HOME/.local/bin/
```
Update PATH:
```bash
export PATH="$HOME/.local/bin/:$PATH"
```
Verify installations:
```bash
cardano-cli --version
cardano-node --version
```

**7. Running Cardano Node**:
```bash
cd $HOME/cardano-node/cardano-testnet/
mkdir config
# Download config files
curl -O -J [Insert URLs here]

# Run node
cardano-node run --topology $HOME/cardano-node/cardano-testnet/config/topology.json --database-path $HOME/cardano-node/cardano-testnet/db --socket-path $HOME/cardano-node/cardano-testnet/db/node.socket --host-addr 0.0.0.0 --port 1337 --config $HOME/cardano-node/cardano-testnet/config/config.json
```
*Note for new terminals*:
```bash
export CARDANO_NODE_SOCKET_PATH="$HOME/cardano-node/cardano-testnet/db/node.socket"
cardano-cli query tip --testnet-magic 1
```

---

If you found this guide helpful, please like, share, and drop a comment! 🔗🚀

*Remember*: Always ensure you understand the commands you're running, especially when executing with `sudo`.

---

There you have it! Remember to replace `[Insert URLs here]` with the list of config files you've provided. Feel free to customize as needed and post it on LinkedIn. Happy coding! 🚀👨‍💻👩‍💻
