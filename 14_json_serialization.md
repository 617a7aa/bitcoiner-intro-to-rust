# JSON Serialization

If you have set up Bitcoin Core and run a `bitcoin-cli` command, you will notice that the output is returned in a certain format. For example, 
```
bitcoin-cli decoderawtransaction 0100000002af0bf9c887049d8a143cff21d9e10d921ab39a3645c0531ba192291b7793c6f8100000008b483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569ffffffff0d088b85950cf484bbcd1114c8fd8ad2850dcf2784c0bbcff9af2b3377211de5010000008b4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87ffffffff02f6891b71010000001976a914764b8c407b9b05cf35e9346f70985945507fa83a88acc0dd9107000000001976a9141d1310fe87b53fec8dbc8911f0ebc112570e34b288ac00000000
``` 
will return the following:

```
{
  "txid": "54dc90aa618ea1c300aac021399c66f5f5152848a57984a757075036e3046147",
  "hash": "54dc90aa618ea1c300aac021399c66f5f5152848a57984a757075036e3046147",
  "version": 1,
  "size": 438,
  "vsize": 438,
  "weight": 1752,
  "locktime": 0,
  "vin": [
    {
      "txid": "f8c693771b2992a11b53c045369ab31a920de1d921ff3c148a9d0487c8f90baf",
      "vout": 16,
      "scriptSig": {
        "asm": "3045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9[ALL] 04da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569",
        "hex": "483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569"
      },
      "sequence": 4294967295
    },
    {
      "txid": "e51d2177332baff9cfbbc08427cf0d85d28afdc81411cdbb84f40c95858b080d",
      "vout": 1,
      "scriptSig": {
        "asm": "30450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d[ALL] 04e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87",
        "hex": "4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87"
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 61.92597494,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 764b8c407b9b05cf35e9346f70985945507fa83a OP_EQUALVERIFY OP_CHECKSIG",
        "desc": "addr(mrJSXr6UmkDgwmo4DRqzdBxgr9qoWcopZS)#fg3gny5q",
        "hex": "76a914764b8c407b9b05cf35e9346f70985945507fa83a88ac",
        "address": "mrJSXr6UmkDgwmo4DRqzdBxgr9qoWcopZS",
        "type": "pubkeyhash"
      }
    },
    {
      "value": 1.27000000,
      "n": 1,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 1d1310fe87b53fec8dbc8911f0ebc112570e34b2 OP_EQUALVERIFY OP_CHECKSIG",
        "desc": "addr(miAgjNZw9UbDy6TTjAqiCnrfTwjz4J6ntU)#uu8snmpj",
        "hex": "76a9141d1310fe87b53fec8dbc8911f0ebc112570e34b288ac",
        "address": "miAgjNZw9UbDy6TTjAqiCnrfTwjz4J6ntU",
        "type": "pubkeyhash"
      }
    }
  ]
}
```

This is the JSON format, a human-readable data-interchange format that transmits data objects consisting of key-value pairs and arrays. https://www.json.org/json-en.html

In Rust, there is a popular crate available for taking some data and converting it into or "**serializing**" it in the JSON format. We can use the `serde_json` crate for this: https://docs.rs/serde_json/latest/serde_json/

Let's add this to our Cargo.toml file. We'll need to bring in the `derive` feature from `serde` so that we can derive the `Serialize` implementation for our structs. We'll also need to bring `serde_json` so that we can convert our struct to a JSON formatted `String`.

```rust
[package]
name = "transaction_decoder_14"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
hex = "0.4"
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0.115"
```

We can follow the example from the documentation here: https://docs.rs/serde_json/latest/serde_json/index.html#creating-json-by-serializing-data-structures. 

First, we'll have to import the `Serialize` feature with the `use serde::Serialize;` statement. Then, we can add the `#[derive(Serialize)]` attribute to our `Input` struct and as long as all of the primitive types it contains can be serialized it will work. Finally, we'll call `to_string()` method on `serde_json` to convert inputs into a JSON formatted string. Let's try that and see what happens.

[comment]: <> (TODO: mention this note earlier in the course and offer a deeper explanation as to why that is)
*Note: We don't need to import top-level modules such as `serde_json` as those will already be included by default in our `main.rs` file.*

```rust
use std::io::Read;
use serde::Serialize;

#[derive(Debug, Serialize)]
struct Input {
    txid: [u8; 32],
    output_index: u32,
    script: Vec<u8>,
    sequence: u32,
}
...
...
fn main() {
    let transaction_hex = "0100000002af0bf9c887049d8a143cff21d9e10d921ab39a3645c0531ba192291b7793c6f8100000008b483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569ffffffff0d088b85950cf484bbcd1114c8fd8ad2850dcf2784c0bbcff9af2b3377211de5010000008b4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87ffffffff02f6891b71010000001976a914764b8c407b9b05cf35e9346f70985945507fa83a88acc0dd9107000000001976a9141d1310fe87b53fec8dbc8911f0ebc112570e34b288ac00000000";
    let transaction_bytes = hex::decode(transaction_hex).unwrap();
    let mut bytes_slice = transaction_bytes.as_slice();
    let version = read_u32(&mut bytes_slice);
    let input_length = read_compact_size(&mut bytes_slice);
    let mut inputs = vec![];

    for _ in 0..input_length {
        let txid = read_txid(&mut bytes_slice);
        let output_index = read_u32(&mut bytes_slice);
        let script = read_script(&mut bytes_slice);
        let sequence = read_u32(&mut bytes_slice);

        inputs.push(Input {
            txid,
            output_index,
            script,
            sequence,
        });
    }

    let json_inputs = serde_json::to_string(&inputs).unwrap();

    println!("Version: {}", version);
    println!("Inputs: {}", json_inputs);
}
...
```

Ok if we print this out now, we'll see that it's now JSON formatted, but it's still hard to read:

```shell
Version: 1
Inputs: [{"txid":[248,198,147,119,27,41,146,161,27,83,192,69,54,154,179,26,146,13,225,217,33,255,60,20,138,157,4,135,200,249,11,175],"output_index":16,"script":[72,48,69,2,33,0,144,74,46,14,143,89,127,193,204,39,27,98,148,176,151,166,237,201,82,227,12,69,62,53,48,249,36,146,116,151,105,168,2,32,24,70,76,34,91,3,194,135,145,175,6,188,127,237,18,157,202,174,255,158,200,19,90,218,31,177,23,98,206,8,30,169,1,65,4,218,40,145,146,176,132,93,91,137,206,130,102,93,136,172,137,215,87,207,197,253,153,123,29,232,174,71,247,120,12,230,163,34,7,88,59,116,88,209,210,243,253,107,58,59,132,42,234,158,183,137,226,190,165,123,3,212,14,104,77,142,30,5,105],"sequence":4294967295},{"txid":[229,29,33,119,51,43,175,249,207,187,192,132,39,207,13,133,210,138,253,200,20,17,205,187,132,244,12,149,133,139,8,13],"output_index":1,"script":[72,48,69,2,32,54,157,247,212,39,149,35,158,171,249,212,26,238,117,227,255,32,82,23,84,82,43,208,103,137,15,142,237,246,4,76,109,2,33,0,154,207,189,136,213,29,132,45,184,122,185,144,164,139,237,18,177,248,22,233,85,2,208,25,142,208,128,222,69,106,152,141,1,65,4,224,236,152,138,103,153,54,206,168,10,136,230,6,61,98,220,133,24,46,84,138,83,95,174,205,110,86,159,181,101,99,61,229,180,232,61,90,17,251,173,139,1,144,140,231,30,3,116,176,6,216,70,148,176,111,16,189,193,83,202,88,165,63,135],"sequence":4294967295}]
```

Our `txid` and `script` fields need to be converted to hexadecimal format so that they appear more human readable. There's no need to store them as an array or as a `Vec` since they are not being used for any internal purposes or calculations in our program. So we can leverage the `hex` library we've already included, and call `hex::encode` to encode both of these types as hex strings. Let's make a few changes to do that.

First, we'll update our `Input` string to change both of these fields to `String` types:
```rust
#[derive(Debug, Serialize)]
struct Input {
    txid: String,
    output_index: u32,
    script: String,
    sequence: u32,
}
```

Next, we'll update our `read_txid` and `read_script` methods to return a string and call `hex::encode` on the buffers:

```rust
fn read_txid(transaction_bytes: &mut &[u8]) -> String {
    let mut buffer = [0; 32];
    transaction_bytes.read(&mut buffer).unwrap();
    buffer.reverse(); // txids are formatted in big endian
    hex::encode(buffer)
}

fn read_script(transaction_bytes: &mut &[u8]) -> String {
    let script_size = read_compact_size(transaction_bytes) as usize;
    let mut buffer = vec![0_u8; script_size];
    transaction_bytes.read(&mut buffer).unwrap();
    hex::encode(buffer)
}
```

Let's run this now and see what happens.

```shell
Version: 1
Inputs: [{"txid":"f8c693771b2992a11b53c045369ab31a920de1d921ff3c148a9d0487c8f90baf","output_index":16,"script":"483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569","sequence":4294967295},{"txid":"e51d2177332baff9cfbbc08427cf0d85d28afdc81411cdbb84f40c95858b080d","output_index":1,"script":"4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87","sequence":4294967295}]
```

Ok, a little better but it's still hard to read. Let's look at the documentation and see if there's a better method we can use: https://docs.rs/serde_json/latest/serde_json/#functions. Looks like there's a method called `to_string_pretty`. Let's try that instead of `to_string` and see how that looks. 

```shell
Version: 1
Inputs: [
  {
    "txid": "f8c693771b2992a11b53c045369ab31a920de1d921ff3c148a9d0487c8f90baf",
    "output_index": 16,
    "script": "483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569",
    "sequence": 4294967295
  },
  {
    "txid": "e51d2177332baff9cfbbc08427cf0d85d28afdc81411cdbb84f40c95858b080d",
    "output_index": 1,
    "script": "4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87",
    "sequence": 4294967295
  }
]
```

Ok, that's way better! Starting to look much more similar to the output from `bitcoin-cli`.

Let's make one more modification to place all the different pieces of a transaction, such as the version, inputs and outputs all into one `Transaction` struct. 

We'll add the `Transaction` struct:
```rust
#[derive(Debug, Serialize)]
struct Transaction {
    version: u32,
    inputs: Vec<Input>,
}
```

Next, we'll update our `main` function:
```rust
fn main() {
    let transaction_hex = "0100000002af0bf9c887049d8a143cff21d9e10d921ab39a3645c0531ba192291b7793c6f8100000008b483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569ffffffff0d088b85950cf484bbcd1114c8fd8ad2850dcf2784c0bbcff9af2b3377211de5010000008b4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87ffffffff02f6891b71010000001976a914764b8c407b9b05cf35e9346f70985945507fa83a88acc0dd9107000000001976a9141d1310fe87b53fec8dbc8911f0ebc112570e34b288ac00000000";
    let transaction_bytes = hex::decode(transaction_hex).unwrap();
    let mut bytes_slice = transaction_bytes.as_slice();
    let version = read_u32(&mut bytes_slice);
    let input_length = read_compact_size(&mut bytes_slice);
    let mut inputs = vec![];

    for _ in 0..input_length {
        let txid = read_txid(&mut bytes_slice);
        let output_index = read_u32(&mut bytes_slice);
        let script = read_script(&mut bytes_slice);
        let sequence = read_u32(&mut bytes_slice);

        inputs.push(Input {
            txid,
            output_index,
            script,
            sequence,
        });
    }

    let transaction = Transaction {
        version,
        inputs,
    };
    
    println!("Transaction: {}", serde_json::to_string_pretty(&transaction).unwrap());
}
```

And now we should see an output with everything neatly printed out under one `Transaction` JSON object! 

```shell
Transaction: {
  "version": 1,
  "inputs": [
    {
      "txid": "f8c693771b2992a11b53c045369ab31a920de1d921ff3c148a9d0487c8f90baf",
      "output_index": 16,
      "script": "483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569",
      "sequence": 4294967295
    },
    {
      "txid": "e51d2177332baff9cfbbc08427cf0d85d28afdc81411cdbb84f40c95858b080d",
      "output_index": 1,
      "script": "4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87",
      "sequence": 4294967295
    }
  ]
}
```

Pretty neat! Let's keep moving and finish decoding the transaction in the next lesson. After that, we'll set up our program to handle user inputs and deal with handling errors. Onwards!

----------------------------------------------------------------------------------------------------------------------------------------------------

<div>
    <p align="right"><a href="15_reading_outputs_and_tuple_structs.md">>>> Next Lesson: Reading Outputs and Tuple Structs</a></p>
</div>
