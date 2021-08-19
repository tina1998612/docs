# Compiler & Toolset

We've developed a toolset for the Move VM and language which allows for:

* **Compiling** Move code into bytecode.
* **Testing** Move code with tests and formal verification.
* **Viewing** Move resources (stored data) from remote blockchain nodes.
* **Disassembling** Move binary and even read human readable result.

## Installation

All tools stored in our [Move-Tools](https://github.com/pontem-network/move-tools) Github repository.

You can download all tools from the [releases page](https://github.com/pontem-network/move-tools/releases).

After downloading, rename tool(s) and move the binary to `/usr/local/bin`.

For example, to install dove on Linux, execute the below command after downloading `dove-1.2.8-linux-x86_64` from the [releases page](https://github.com/pontem-network/move-tools/releases)(remember to update the below command to the latest downloaded dove version):

```sh
mv ./dove-1.2.8-linux-x86_64 ./dove
chmod +x ./dove
mv ./dove /usr/local/bin
```

For Mac:

```sh
mv ./dove-1.2.8-darwin-x86_64 ./dove
chmod +x ./dove
mv ./dove /usr/local/bin
```

For Windows:

1. Go to **"Program Files"** directory.
2. Create there **"dove"** directory.
3. Rename the downloaded file to **"dove"** and put it into **"dove"** directory.

Now dove is available from the command line.

To build tools from sources, see [README](https://github.com/pontem-network/move-tools#move-tools).

## Dove

[Dove](https://github.com/pontem-network/move-tools/tree/master/dove) is Move's compiler and package manager. With Dove, you can create your own Move smart contract projects.

Let's create first project (**replace address with your address**):

```sh
dove new first_dove_project --dialect pont  --address <your_address>
```

If you don't have an address yet, the easiest way is to create a new account with the [Polkadot JS Extension](https://polkadot.js.org/extension/).

The `first_dove_project` folder will be generated under the current directory.


Navigate to `first_dove_project` folder and see what's automatically generated inside:

```sh
cd ./first_dove_project
ls -la
```

* `Dove.toml` - configuration file/.
* `modules` - place modules here.
* `scripts` - place scripts here.
* `tests` - place tests here.

Let's see what contains `Dove.toml`:

```toml
[package]
name = "first_dove_project"
account_address = "<your_address>"
dialect = "pont"

dependencies = [
    { git = "https://github.com/pontem-network/move-stdlib", tag = "v0.1.2" }
]
```

* `name` - name of project.
* `account_address` - address of your account, used during compilation.
* `dialect` - supported address format, it can be `Diem`, or `pont`. If `Diem` is chosen, you can't use [SS58](https://wiki.polkadot.network/docs/learn-accounts#address-format) (the Polkadot address format used in Substrate-based chains). If `pont` is chosen, you can use either SS58 or the Diem address format.
* `dependencies` - list of dependencies, git (tag or branch also supported) or local folder (use `path`).

Let's create empty script and build it:

```sh
vim ./scripts/test.move
```

Put some Move code into it:

```rustc
script {
    fun test() {

    }
}
```

Build your empty project:

```sh
dove build # Build script without providing arguments, so can't be used in pallet.
dove tx 'test()' # Build script with arguments.
```

There is a difference between `build` and `tx` commands, if you want just to `build` your scripts use `build`, if you want to send transactions to the pallet use `tx` and provide arguments. Use `tx` only for scripts, build modules with `build` command.

```sh
dove tx --help
```

See your artifacts built in `./artifacts` folder:

```sh
ls ./artifacts/scripts # Script built without providing arguments.
ls ./artifacts/transactions # Script built with arguments for pallet.
```

### Script Transaction

Command `tx` allows you to create transactions for a Polkadot based chain with Move Pallet.

`tx` takes script identifier, type parameters, and arguments and creates a transaction file as an artifact of work:

Example:

```sh
dove tx 'store_u64(60)'
```

This command searches for the script by name 'store_u64' in the script directory. Then it compiles it and creates a transaction file.

This command will fail if:

* There is no script with the name given name 'store_u64'.
* There is more than one script with the name 'store_64'.
* The passed parameters or type parameters do not match the script parameters.
* There are syntax errors in the script.


You can use type parameters like in the Move language.

Example:

```sh
dove tx 'create_account<0x01::PONT::T>()'
```

You are also allowed to use addresses:

```sh
dove tx 'send_payment(1exaAg2VJRQbyUBAeXcktChCAqjVP9TUxF3zo23R2T6EGdE)'
```

Supported types:

**Numbers (u8, u64, u128):**

```sh
dove tx 'my_script(10, 1024)'
```

**Boolean:**

```sh
dove tx 'my_script(true, false)'
```

**Addresses:**

```sh
dove tx 'my_script(1exaAg2VJRQbyUBAeXcktChCAqjVP9TUxF3zo23R2T6EGdE, 0x1CF326C5AAA5AF9F0E2791E66310FE8F044FAADAF12567EAA0976959D1F7731F)'
```

**Vectors:**

```sh
dove tx 'my_script([10, 20, 1024])' // Vector u64
dove tx 'my_script([1exaAg2VJRQbyUBAeXcktChCAqjVP9TUxF3zo23R2T6EGdE, 0x1CF326C5AAA5AF9F0E2791E66310FE8F044FAADAF12567EAA0976959D1F7731F, 0x01])' // Vector addresses.
```

See more in [Dove documentation](https://github.com/pontem-network/move-tools#arguments).

### More

To learn more commands see help:

```sh
dove --help
```

To continue with Dove and create your first scripts and modules read our [Move VM](../move_vm/scripts.md) pallet documentation.


## Language Server

Move language server used mostly in Ide extensions, like our:
  
* [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=PontemNetwork.move-language) 
* [IntelliJ IDEA Extension](https://plugins.jetbrains.com/plugin/14721-move-language).

See [documentation](https://github.com/pontem-network/move-tools#language-server).

## Resource viewer

Resource viewer allows developers to read resources from blockchain storage. In case of Pontem it's Substrate storage.

* [Instruction](https://github.com/pontem-network/move-tools/blob/master/resource-viewer/README.md) to install resource viewer.
* [Tutorial](../tutorials/watch_resources.md) how to use Move resource viewer.

## Disassembler

Allows to dissable compiled `.mv` (modules/scripts) files:

```sh
disassembler --input <path to compiled module or script>
```
