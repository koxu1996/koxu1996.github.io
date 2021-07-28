Follow these commands to install a Casper node on a RedHat / CentOS dev machine (without npm).

Install the pre-requisite tools.

`sudo yum install gcc cmake git pkg-config make openssl-devel gcc-c++`

Ensure the cmake version is higher than 3.4. Check https://www.rust-lang.org/tools/install.

`cmake --version`

Install the Rust toolchain.

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

As indicated after install, check your cargo environment:

`source $HOME/.cargo/env`

Or exit the shell and re-enter to load cargo in your PATH. 

Then build _wabt_ for drastically reducing the size of the wasm.

```
BRANCH=1.0.23
git clone --branch ${BRANCH} https://github.com/WebAssembly/wabt.git "wabt-${BRANCH}"
cd "wabt-${BRANCH}" 
git submodule update --init 
cd - 
cmake -S "wabt-${BRANCH}" -B "wabt-${BRANCH}/build" 
cmake --build "wabt-${BRANCH}/build" --parallel 8 
sudo cmake --install "wabt-${BRANCH}/build" --prefix /usr --strip -v 
rm -rf "wabt-${BRANCH}"
```