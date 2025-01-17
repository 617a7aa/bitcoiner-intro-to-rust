# Reading Outputs and Tuple Structs

Reading the outputs is fairly straightforward now that we have all the other pieces in place. Similar to inputs, there is a compactSize integer which indicates the number of outputs. An output is comprised of an amount, indicating how many satoshis to be transferred. This field is 8 bytes in length, which we'll represent as a `u64` type. This is followed by a compactSize integer indicating the length of the *output script*. The script, also known as the `scriptPubKey` contains the conditions that that will need to be  fulfilled in order to spend those satoshis. For example, if an output has a `P2PKH` script, then the spender will need to present a signature and a public key in order to unlock those funds. https://learnmeabitcoin.com/technical/script/p2pkh/

So all we need is to do now is the following:
1. Create a new `Output` struct
2. Modify our `Transaction` struct 
3. Write a new `read_u64` function to return the `amount`
4. Use our existing `read_script` function to read the `script_pubkey`.

Let's make those changes:

```rust
...

#[derive(Debug, Serialize)]
struct Transaction {
    version: u32,
    inputs: Vec<Input>,
    outputs: Vec<Output>,
}

...

#[derive(Debug, Serialize)]
struct Output {
    amount: u64,
    script_pubkey: String,
}

...

fn read_u64(transaction_bytes: &mut &[u8]) -> u64 {
    let mut buffer = [0; 8];
    transaction_bytes.read(&mut buffer).unwrap();

    u64::from_le_bytes(buffer)
}

...

fn main() {
    let transaction_hex = "0100000002af0bf9c887049d8a143cff21d9e10d921ab39a3645c0531ba192291b7793c6f8100000008b483045022100904a2e0e8f597fc1cc271b6294b097a6edc952e30c453e3530f92492749769a8022018464c225b03c28791af06bc7fed129dcaaeff9ec8135ada1fb11762ce081ea9014104da289192b0845d5b89ce82665d88ac89d757cfc5fd997b1de8ae47f7780ce6a32207583b7458d1d2f3fd6b3a3b842aea9eb789e2bea57b03d40e684d8e1e0569ffffffff0d088b85950cf484bbcd1114c8fd8ad2850dcf2784c0bbcff9af2b3377211de5010000008b4830450220369df7d42795239eabf9d41aee75e3ff20521754522bd067890f8eedf6044c6d0221009acfbd88d51d842db87ab990a48bed12b1f816e95502d0198ed080de456a988d014104e0ec988a679936cea80a88e6063d62dc85182e548a535faecd6e569fb565633de5b4e83d5a11fbad8b01908ce71e0374b006d84694b06f10bdc153ca58a53f87ffffffff02f6891b71010000001976a914764b8c407b9b05cf35e9346f70985945507fa83a88acc0dd9107000000001976a9141d1310fe87b53fec8dbc8911f0ebc112570e34b288ac00000000";
    let transaction_bytes = hex::decode(transaction_hex).unwrap();
    let mut bytes_slice = transaction_bytes.as_slice();
    let version = read_u32(&mut bytes_slice);

    // Read inputs
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

    // Read outputs
    let output_length = read_compact_size(&mut bytes_slice);
    let mut outputs = vec![];

    for _ in 0..output_length {
        let amount = read_u64(&mut bytes_slice);
        let script_pubkey = read_script(&mut bytes_slice);

        outputs.push(Output {
            amount,
            script_pubkey,
        });
    }

    let transaction = Transaction {
        version,
        inputs,
        outputs,
    };
    
    println!("Transaction: {}", serde_json::to_string_pretty(&transaction).unwrap());
}

...
```

Let's run this with `cargo run` and take a look at the printout.

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
  ],
  "outputs": [
    {
      "amount": 6192597494,
      "script_pubkey": "76a914764b8c407b9b05cf35e9346f70985945507fa83a88ac"
    },
    {
      "amount": 127000000,
      "script_pubkey": "76a9141d1310fe87b53fec8dbc8911f0ebc112570e34b288ac"
    }
  ]
}
```

Not bad! We're starting to see most of the transaction details. The amounts are a little hard to read in Satoshis and are typically printed in Bitcoin so let's update that. There are 100,000,000 satoshis in one bitcoin, so we'll modify the calculation. Let's try something like the following and see what happens `let amount = read_u64(&mut bytes_slice) / 100_000_000;`. 

We'll get a result we don't want. 

```shell
...

  "outputs": [
    {
      "amount": 61,
      "script_pubkey": "76a914764b8c407b9b05cf35e9346f70985945507fa83a88ac"
    },
    {
      "amount": 1,
      "script_pubkey": "76a9141d1310fe87b53fec8dbc8911f0ebc112570e34b288ac"
    }

...
```

The amounts are missing the decimal values because we're doing math on unsigned integers which are whole numbers. What we really want to do is first convert the integer types into floating types. We could do some basic math by first converting the `u64` to an `f64` and then dividing that by `100_000_000.0`. But let's take a look at some open source code and how a popular library, such as [Rust-Bitcoin](https://github.com/rust-bitcoin/rust-bitcoin) deals with the amount field. 

From the source code, it appears they create an `Amount` tuple struct to represent the Satoshi / Bitcoin value. https://docs.rs/bitcoin/latest/src/bitcoin/amount.rs.html#498

A tuple struct is a struct type that wraps a tuple type. A tuple is a comma-separated list of different types in parentheses `()`. The values are accessed by calling `.0`, `.1`, etc. based on their position in the tuple. For example,
```rust
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
```

More here: https://doc.rust-lang.org/book/ch03-02-data-types.html#the-tuple-type

A **tuple struct** is simply a tuple wrapped in a struct. This allows us to write methods for that type. 

So let's implement a basic version of Rust-Bitcoin's `Amount` struct here:

```rust
struct Amount(u64);

impl Amount {
    pub fn to_btc(&self) -> f64 {
        self.0 as f64 / 100_000_000.0
    }
}

#[derive(Debug, Serialize)]
struct Output {
    amount: f64,
    script_pubkey: String,
}
```

Two things to note here:
1. Notice how the `to_btc` method has a `self` argument. The `self` is the specific *instance* of this struct. It will be passed in by default when the instance calls the `to_btc` method. This allows us to access the `u64` value of that instance with `self.0`.
2. We're setting the `self` argument as a *shared reference* with the `&` sign. We're doing this because we don't need ownership of the instance nor do we need to mutate the actual instance. All we need to do is read its contents and output an `f64` result.
2. We made sure to change the `amount` type in `Output` to `f64` from `u64`.

Let's now update our `read_u64` method. Since this is only being used for the amount, we'll rename it to `read_amount` and return the `Amount` type:

```rust
...

fn read_amount(transaction_bytes: &mut &[u8]) -> Amount {
    let mut buffer = [0; 8];
    transaction_bytes.read(&mut buffer).unwrap();

    Amount(u64::from_le_bytes(buffer))
}

...
```

Lastly, we'll replace the `read_u64` call with `read_amount` and chain that with a call to `to_btc()`.

```rust
...

    for _ in 0..output_length {
        let amount = read_amount(&mut bytes_slice).to_btc();
        let script_pubkey = read_script(&mut bytes_slice);

        outputs.push(Output {
            amount,
            script_pubkey,
        });
    }

...
```

If we run `cargo run` now, we should get a nice printout with the amounts in bitcoin and not satoshis. Great!

But there's a problem here. We're storing the amount in `Output` as an `f64` type. Ideally, we would keep this as an `Amount` type for internal purposes, readability and type safety. What we really want is to keep it as the `Amount` type, but convert it to an `f64` denominated in Bitcoin for serialization and display purposes. This will require us to do some custom serialization which we'll talk about in the next lesson. Onwards!

----------------------------------------------------------------------------------------------------------------------------------------------------

<div>
    <p align="right"><a href="16_custom_serialization_and_generic_functions.md">>>> Next Lesson: Custom Serialization and Generic Functions</a></p>
</div>
