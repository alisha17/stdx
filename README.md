<a id="list"></a>
# `stdx` - The missing batteries of Rust

New to Rust and don't yet know what crates to use?
[**stdx has the best crates**](#about-stdx).

Current revision: `stdx` 0.118.0-rc, for Rust 1.18, June 8, 2017.

| Feature                        | Crate                      |                    |
|--------------------------------|----------------------------|--------------------|
| Bitfields                      | [`bitflags = "0.9.1"`]     | [📖][d-bitflags]    |
| Byte order conversion          | [`byteorder = "1.0.0"`]    | [📖][d-byteorder]   |
| Date and time                  | [`chrono = "0.3.1"`]       | [📖][d-chrono]      |
| Command-line argument parsing  | [`clap = "2.24.2"`]        | [📖][d-clap]        |
| Error handling                 | [`error-chain = "0.10.0"`] | [📖][d-error-chain] |
| Compression - deflate (gzip)   | [`flate2 = "0.2.19"`]      | [📖][d-flate2]      |
| Global initialization          | [`lazy_static = "0.2.8"`]  | [📖][d-lazy_static] |
| C interop                      | [`libc = "0.2.23"`]        | [📖][d-libc]        |
| Logging                        | [`log = "0.3.8"`]          | [📖][d-log]         |
| Memory-mapped file I/O         | [`memmap = "0.5.2"`]       | [📖][d-memmap]      |
| Multidimensional arrays        | [`ndarray = "0.9.1"`]      | [📖][d-ndarray]     |
| Big, rational, complex numbers | [`num = "0.1.37"`]         | [📖][d-num]         |
| Random numbers                 | [`rand = "0.3.15"`]        | [📖][d-rand]        |
| Parallel iteration             | [`rayon = "0.7.1"`]        | [📖][d-rayon]       |
| Regular expressions            | [`regex = "0.2.2"`]        | [📖][d-regex]       |
| HTTP client                    | [`reqwest = "0.6.2"`]      | [📖][d-reqwest]     |
| Serialization                  | [`serde = "1.0.8"`]        | [📖][d-serde]       |
| JSON                           | [`serde_json = "1.0.2"`]   | [📖][d-serde_json]  |
| Tar archives                   | [`tar = "0.4.13"`]         | [📖][d-tar]  |
| Temporary directories          | [`tempdir = "0.3.5"`]      | [📖][d-tempdir]     |
| Configuration files            | [`toml = "0.4.1"`]         | [📖][d-toml]        |
| URLs                           | [`url = "1.4.1"`]          | [📖][d-url]         |

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="bitflags"></a>
### `bitflags = "0.9.1"` &emsp; [📖][d-bitflags]

The only thing this crate does is export the
[`bitflags!`](http://doc.rust-lang.org/bitflags/bitflags/macro.bitflags!.html#example)
macro, but it's a heckuva-useful macro. `bitflags!` produces typesafe
bitmasks, types with named values that are efficiently packed together
as bits to express sets of options.

**Example**: [`examples/bitflags.rs`]

```rust
#[macro_use]
extern crate bitflags;

bitflags! {
    struct Flags: u32 {
        const FLAG_A       = 0b00000001,
        const FLAG_B       = 0b00000010,
        const FLAG_C       = 0b00000100,
        const FLAG_ABC     = FLAG_A.bits
                           | FLAG_B.bits
                           | FLAG_C.bits,
    }
}

fn main() {
    let e1 = FLAG_A | FLAG_C;
    let e2 = FLAG_B | FLAG_C;
    assert_eq!((e1 | e2), FLAG_ABC);   // union
    assert_eq!((e1 & e2), FLAG_C);     // intersection
    assert_eq!((e1 - e2), FLAG_A);     // set difference
    assert_eq!(!e2, FLAG_A);           // set complement
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="byteorder"></a>
### `byteorder = "1.0.0"` &emsp; [📖][d-byteorder]

Functions for converting between numbers and bytes, in
little-endian, or big-endian orders.

**Example**: [`examples/byteorder.rs`]

```rust
extern crate byteorder;

use std::io::Cursor;
use byteorder::{BigEndian, ReadBytesExt};
use byteorder::{LittleEndian, WriteBytesExt};

fn main() {
    // Read unsigned 16 bit big-endian integers from a Read type:
    let mut rdr = Cursor::new(vec![2, 5, 3, 0]);
    // Note that we use type parameters to indicate which kind of byte
    // order we want!
    assert_eq!(517, rdr.read_u16::<BigEndian>().unwrap());
    assert_eq!(768, rdr.read_u16::<BigEndian>().unwrap());

    // Write unsigned 16 bit little-endian integers to a Write type:
    let mut wtr = vec![];
    wtr.write_u16::<LittleEndian>(517).unwrap();
    wtr.write_u16::<LittleEndian>(768).unwrap();
    assert_eq!(wtr, vec![5, 2, 0, 3]);
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="chrono"></a>
### `chrono = "0.3.1"` &emsp; [📖][d-chrono]

Date and time types.

**Example**: [`examples/chrono.rs`]

```rust
extern crate chrono;
use chrono::*;

fn main() {
    let local: DateTime<Local> = Local::now();
    let utc: DateTime<UTC> = UTC::now();

    let dt = UTC.ymd(2014, 11, 28).and_hms(12, 0, 9);

    assert_eq!((dt.year(), dt.month(), dt.day()), (2014, 11, 28));
    assert_eq!((dt.hour(), dt.minute(), dt.second()), (12, 0, 9));

    assert_eq!(dt.format("%Y-%m-%d %H:%M:%S").to_string(), "2014-11-28 12:00:09");
    assert_eq!(dt.format("%a %b %e %T %Y").to_string(), "Fri Nov 28 12:00:09 2014");

    assert_eq!(format!("{}", dt), "2014-11-28 12:00:09 UTC");
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="clap"></a>
### `clap = "2.24.2"` &emsp; [📖][d-clap]

Clap is a command line argument parser that is easy to
use and is highly configurable.

**Example**: [`examples/clap.rs`]

```rust
extern crate clap;
use clap::{Arg, App, SubCommand};
 
fn main() {
    let app = App::new("My Super Program")
        .version("1.0")
        .author("Kevin K. <kbknapp@gmail.com>")
        .about("Does awesome things")
        .arg(Arg::with_name("config")
             .short("c")
             .long("config")
             .value_name("FILE")
             .help("Sets a custom config file")
             .takes_value(true))
        .arg(Arg::with_name("INPUT")
             .help("Sets the input file to use")
             .required(true)
             .index(1))
        .subcommand(SubCommand::with_name("test")
                    .about("controls testing features")
                    .arg(Arg::with_name("debug")
                         .short("d")
                         .help("print debug information verbosely")));

    // Parse the command line arguments
    let matches = app.get_matches();
 
    let config = matches.value_of("config").unwrap_or("default.conf");
    let input = matches.value_of("INPUT").unwrap();

    // Handle subcommands
    match matches.subcommand() {
        ("clone",  Some(sub_matches)) => {
            if matches.is_present("d") {
                // ...
            }
        },
        ("push",   Some(sub_matches)) => {},
        ("commit", Some(sub_matches)) => {},
        _ => {},
    } 
}
```

**Alternatives**: [`docopt`]

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="error-chain"></a>
### `error-chain = "0.10.0"` &emsp; [📖][d-error-chain]

Rust programs that handle errors consistently are reliable programs.
Even after one understands [error handling] in Rust, it can be
difficult to grasp and implement its best practices. `error-chain`
helps you define your own error type that works with the `?` operator
to make error handling in Rust simple and elegant.

**Example**: [`examples/error-chain.rs`]

```rust
// `error_chain!` can recurse deeply
#![recursion_limit = "1024"]

#[macro_use]
extern crate error_chain;

// We'll put our errors in an `errors` module, and other modules in
// this crate will `use errors::*;` to get access to everything
// `error_chain!` creates.
mod errors {
    // Create the Error, ErrorKind, ResultExt, and Result types
    error_chain! { }
}

use errors::*;

fn main() {
    if let Err(ref e) = run() {
        use ::std::io::Write;
        let stderr = &mut ::std::io::stderr();
        let errmsg = "Error writing to stderr";

        writeln!(stderr, "error: {}", e).expect(errmsg);

        for e in e.iter().skip(1) {
            writeln!(stderr, "caused by: {}", e).expect(errmsg);
        }

        // The backtrace is not always generated. Try to run this example
        // with `RUST_BACKTRACE=1`.
        if let Some(backtrace) = e.backtrace() {
            writeln!(stderr, "backtrace: {:?}", backtrace).expect(errmsg);
        }

        ::std::process::exit(1);
    }
}

// Most functions will return the `Result` type, imported from the
// `errors` module. It is a typedef of the standard `Result` type
// for which the error type is always our own `Error`.
fn run() -> Result<()> {
    use std::fs::File;

    // Use chain_err to attach your own context to errors
    File::open("my secret file")
        .chain_err(|| "unable to open my secret file")?;

    // Use the `bail!` macro to return an error Result, ala `println!`
    bail!("giving up");
}
```

**Alternatives**: [`quick-error`]

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="flate2"></a>
### `flate2 = "0.2.19"` &emsp; [📖][d-flate2]

Compression and decompression using the [DEFLATE] algorithm.

**Example**: [`examples/flate2.rs`]

```rust
extern crate flate2;
extern crate tar;

use flate2::read::GzDecoder;
use std::env;
use std::fs::File;
use std::io::{self, BufReader};
use tar::Archive;

fn run() -> Result<(), io::Error> {
    let mut args = env::args().skip(1);
    let tarball = args.next().expect("incorrect argument");
    let outdir = args.next().expect("incorrect arguments");

    let archive = File::open(tarball)?;
    let archive = BufReader::new(archive);
    let archive = GzDecoder::new(archive)?;
    let mut archive = Archive::new(archive);

    archive.unpack(outdir)?;

    Ok(())
}

fn main() { run().unwrap() }
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="lazy_static"></a>
### `lazy_static = "0.2.8"` &emsp; [📖][d-lazy_static]

Rust has strict rules about accessing global state. In particular
there is no ['life before main'] in Rust, so it's not possible to
write a programmatic constructor for a global value that will be run
at startup. Instead, Rust prefers lazy execution for global
initialization, and the `lazy_static!` macro does just that.

**Example**: [`examples/lazy_static.rs`]

```rust
#[macro_use]
extern crate lazy_static;

use std::collections::HashMap;

lazy_static! {
    static ref HASHMAP: HashMap<u32, &'static str> = {
        let mut m = HashMap::new();
        m.insert(0, "foo");
        m.insert(1, "bar");
        m.insert(2, "baz");
        m
    };
    static ref COUNT: usize = HASHMAP.len();
    static ref NUMBER: u32 = times_two(21);
}

fn times_two(n: u32) -> u32 { n * 2 }

fn main() {
    println!("The map has {} entries.", *COUNT);
    println!("The entry for `0` is \"{}\".", HASHMAP.get(&0).unwrap());
    println!("A expensive calculation on a static results in: {}.", *NUMBER);
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="libc"></a>
### `libc = "0.2.23"` &emsp; [📖][d-libc]

If you need to talk to foreign code, you need this crate. It exports C
type and function definitions appropriate to each target platform Rust
supports. It defines the standardized C features that are common
across all platforms as well as non-standard features specific to the
platform C libraries. For more platform-specific FFI definitions
see [`nix`] and [`winapi`].

**Example**: [`examples/libc.rs`]

```rust
extern crate libc;

fn main() {
    unsafe {
        libc::exit(0);
    }
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="log"></a>
### `log = "0.3.8"` &emsp; [📖][d-log]

The most common way to perform basic logging in Rust, with the
`error!`, `warn!`, `info!`, and `debug!` macros. It is often
combined with the `env_logger` crate to get logging to the
console, controlled by the `RUST_LOG` environment variable.
This is the traditional logging crate used by `rustc`, and
its functionality was once built in to the language.

**Supplemental crates**: [`env_logger = "0.4.3"`]

**Example**: [`examples/log.rs`]

```rust
#[macro_use]
extern crate log;
extern crate env_logger;

use log::LogLevel;

fn main() {
    env_logger::init().unwrap();

    debug!("this is a debug {}", "message");
    error!("this is printed by default");

    if log_enabled!(LogLevel::Info) {
        let x = 3 * 4; // expensive computation
        info!("the answer was: {}", x);
    }
}
```

**Alternatives**: [`slog`], [`log4rs`]

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="memmap"></a>
### `memmap = "0.5.2"` &emsp; [📖][d-memmap]

Cross-platform bindings to memmory-mapped I/O, via the `mmap` syscall
on Unix, and the `CreateFileMapping` / `MapViewOfFile` functions on
Windows.

**Example**: [`examples/memmap.rs`]

[`examples/memmap.rs`]: examples/memmap.rs

```rust
extern crate memmap;

use memmap::{Mmap, Protection};
use std::env;
use std::io;
use std::str;

fn run() -> Result<(), io::Error> {
    let mut args = env::args().skip(1);
    let input = args.next().expect("incorrect argument");

    let map = Mmap::open_path(input, Protection::Read)?;
    unsafe {
        let all_bytes = map.as_slice();
        if let Ok(file_str) = str::from_utf8(all_bytes) {
            println!("{}", file_str);
        } else {
            println!("not utf8");
        }
    }
    Ok(())
}

fn main() { run().unwrap() }
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="ndarray"></a>
### `ndarray = "0.9.1"` &emsp; [📖][d-ndarray]

The ndarray crate provides an N-dimensional container for general
elements and for numerics. The multidimensional array, otherwise known
as a "matrix", is a core data structure for numerical applications,
and Rust does not have one in the language or standard library.

**Example**: [`examples/ndarray.rs`]

```rust
#[macro_use(s)]
extern crate ndarray;

use ndarray::{Array3, arr3};

fn main() {
    // Create a three-dimensional f64 array, initialized with zeros
    let mut temperature = Array3::<f64>::zeros((3, 4, 5));

    // Increase the temperature in this location, notice the
    // double-brackets indexing `temperature`
    temperature[[2, 2, 2]] += 0.5;

    // Create a 3-dimensional matrix,
    // 2 submatrices of 2 rows with 3 elements per row, means a shape
    // of `[2, 2, 3]`.
    let a = arr3(&[[[ 1,  2,  3],     // -- 2 rows  \_
                    [ 4,  5,  6]],    // --         /
                   [[ 7,  8,  9],     //            \_ 2 submatrices
                    [10, 11, 12]]]);  //            /
    //  3 columns ..../.../.../

    // This is a 2 x 2 x 3 array
    assert_eq!(a.shape(), &[2, 2, 3]);

    // Let’s create a slice of `a` with
    //
    // - Both of the submatrices of the greatest dimension: `..`
    // - Only the first row in each submatrix: `0..1`
    // - Every element in each row: `..`
    let b = a.slice(s![.., 0..1, ..]);

    // This is the result of the above slice into `a`
    let c = arr3(&[[[ 1,  2,  3]],
                   [[ 7,  8,  9]]]);
    assert_eq!(b, c);
    assert_eq!(b.shape(), &[2, 1, 3]);
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="num"></a>
### `num = "0.1.37"` &emsp; [📖][d-num]

Big integers, rational numbers, complex numbers, and numeric
traits. This is a rust-lang crate that has persisted through Rust's
evolution but is somewhat unloved.

**Example**: [`examples/num.rs`]

```rust
extern crate num;

use num::FromPrimitive;
use num::bigint::BigInt;
use num::rational::{Ratio, BigRational};

fn approx_sqrt(number: u64, iterations: usize) -> BigRational {
    let start: Ratio<BigInt>
        = Ratio::from_integer(FromPrimitive::from_u64(number).unwrap());

    let mut approx = start.clone();

    for _ in 0..iterations {
        approx = (&approx + (&start / &approx)) /
            Ratio::from_integer(FromPrimitive::from_u64(2).unwrap());
    }

    approx
}

fn main() {
    println!("{}", approx_sqrt(10, 4)); // prints 4057691201/1283082416
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="rand"></a>
### `rand = "0.3.15"` &emsp; [📖][d-rand]

Random number generators. The defaults are cryptographically strong.

**Example**: [`examples/rand.rs`]

```rust
extern crate rand;

use rand::Rng;

fn main() {
    let mut rng = rand::thread_rng();
    if rng.gen() { // random bool
        println!("i32: {}, u32: {}", rng.gen::<i32>(), rng.gen::<u32>())
    }

    let tuple = rand::random::<(f64, char)>();
    println!("{:?}", tuple)
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="rayon"></a>
### `rayon = "0.7.1"` &emsp; [📖][d-rayon]

When people say that Rust makes parallelism easy, this
is why. Rayon provides parallel iterators that make
expressing efficient parallel operations simple
and foolproof.

**Example**: [`examples/rayon.rs`]

```rust
extern crate rayon;

use rayon::prelude::*;

fn main() {
    let mut input = (0..1000).collect::<Vec<_>>();

    // Calculate the sum of squares
    let sq_sum: i32 = input.par_iter()
                      .map(|&i| i * i)
                      .sum();

    // Increment each element in parallel
    input.par_iter_mut()
        .for_each(|p| *p += 1);

    // Parallel quicksort
    let mut input = (0..1000).rev().collect::<Vec<_>>();
    quick_sort(&mut input);
}

fn quick_sort<T: PartialOrd + Send>(v: &mut [T]) {
    if v.len() <= 1 {
        return;
    }

    let mid = partition(v);
    let (lo, hi) = v.split_at_mut(mid);
    rayon::join(|| quick_sort(lo), || quick_sort(hi));
}

fn partition<T: PartialOrd + Send>(v: &mut [T]) -> usize {
    let pivot = v.len() - 1;
    let mut i = 0;
    for j in 0..pivot {
        if v[j] <= v[pivot] {
            v.swap(i, j);
            i += 1;
        }
    }
    v.swap(i, pivot);
    i
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="regex"></a>
### `regex = "0.2.2"` &emsp; [📖][d-regex]

Rust's regular expressions are [fast], like Rust is fast. Part of
their power comes from a careful design that disallows back-references
and arbitrary lookahead, creating predictable worst-case performance.

**Example**: [`examples/regex.rs`]

```rust
extern crate regex;

use regex::Regex;

fn main() {
    // Find a date
    let re = Regex::new(r"^\d{4}-\d{2}-\d{2}$").unwrap();
    assert!(re.is_match("2014-01-01"));

    // Iterating over capture groups
    let re = Regex::new(r"(\d{4})-(\d{2})-(\d{2})").unwrap();
    let text = "2012-03-14, 2013-01-01 and 2014-07-05";
    for cap in re.captures_iter(text) {
        println!("Month: {} Day: {} Year: {}", &cap[2], &cap[3], &cap[1]);
    }
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="reqwest"></a>
### `reqwest = "0.6.2"` &emsp; [📖][d-reqwest]

A simple HTTP and HTTPS client.

**Example**: [`examples/reqwest.rs`]

```rust
extern crate reqwest;

use std::collections::HashMap;
use std::io::{BufRead, BufReader};

fn main() {
    // Make a GET request
    let resp = reqwest::get("https://www.rust-lang.org").unwrap();
    assert!(resp.status().is_success());

    let lines = BufReader::new(resp)
                          .lines()
                          .filter_map(|l| l.ok())
                          .take(10);
    for line in lines {
        println!("{}", line);
    }

    // Make a POST request
    let client = reqwest::Client::new().unwrap();
    let res = client.post("http://httpbin.org/post")
        .body("the exact body that is sent")
        .send();

    // Convert to/from JSON automatically
    let mut map = HashMap::new();
    map.insert("lang", "rust");
    map.insert("body", "json");

    // This will POST a body of `{"lang":"rust","body":"json"}`
    let client = reqwest::Client::new().unwrap();
    let res = client.post("http://httpbin.org/post")
        .json(&map)
        .send();
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="serde"></a>
### `serde = "1.0.8"` &emsp; [📖][d-serde]

Serialization and deserialization of Rust datastructures is fast
and easy using the `serde` serialization framework. Simply
tag your data structures with `#[derive(Serialize, Deserialize)]`
and serde will automatically convert them between formats like
JSON, TOML, YAML, and more. To best understand serde, read
its documentation at [serde.rs].

**Supplemental crates**: [`serde_derive = "1.0.8"`],
                         [`serde_json = "1.0.2"`],
                         [`toml = "0.4.1"`]

**Example**: [`examples/serde.rs`]

```rust
#[macro_use]
extern crate serde_derive;
extern crate serde_json;

use serde_json::Value;

#[derive(Serialize, Deserialize, Debug)]
struct Contact {
    name: String,
    age: u32,
}

fn main() {
    let contact = Contact {
        name: "Brian".to_string(),
        age: 21,
    };

    // Serialize data structures to strings in JSON format
    let contact: String = serde_json::to_string(&contact).unwrap();
    println!("{}", contact);

    // Deserialize data structures from JSON strings
    let contact: Contact = serde_json::from_str(&contact).unwrap();
    println!("{:?}", contact);

    // Convert to arbitrary JSON `Value` type
    let contact: Value = serde_json::to_value(&contact).unwrap();
    println!("{:?}", contact);
}
```

**Alternatives**: [`rustc-serialize`]

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="serde_json"></a>
### `serde_json = "1.0.2"` &emsp; [📖][d-serde_json]

Access to [JSON], the "JavaScript Object Notation" format,
widely used for transmission and storage of data on the Internet.
This crate can be used for reading, writing, and manipulation
of arbitrary JSON in addition to it's use for automatic serialization
with [serde][serde.rs].

**Example**: [`examples/json.rs`]

```rust
extern crate serde_json;

use serde_json::Value;

fn main() {
    // Some JSON input data as a &str. Maybe this comes from the user.
    let data = r#"{
                    "name": "John Doe",
                    "age": 43,
                    "phones": [
                      "+44 1234567",
                      "+44 2345678"
                    ]
                  }"#;

    // Parse the string of data into serde_json::Value.
    let v: Value = serde_json::from_str(data).unwrap();

    // Access parts of the data by indexing with square brackets.
    println!("Please call {} at the number {}", v["name"], v["phones"][0]);
}
```

**Alternatives**: [`json`]

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="tar"></a>
### `tar = "0.4.13"` &emsp; [📖][d-tar]

The "tar" archive format is in common use on the web. It is most often
found in the form of `.tar.gz` files (called "tarballs") that have
been compressed with the [DEFLATE] algorithm, which the `tar` crate
can decompress when paired with the [`flate2`][flate2] crate.

**Example**: [`examples/tar.rs`]

```rust
extern crate flate2;
extern crate tar;

use flate2::read::GzDecoder;
use std::env;
use std::fs::File;
use std::io::{self, BufReader};
use tar::Archive;

fn run() -> Result<(), io::Error> {
    let mut args = env::args().skip(1);
    let tarball = args.next().expect("incorrect argument");
    let outdir = args.next().expect("incorrect arguments");

    let archive = File::open(tarball)?;
    let archive = BufReader::new(archive);
    let archive = GzDecoder::new(archive)?;
    let mut archive = Archive::new(archive);

    archive.unpack(outdir)?;

    Ok(())
}

fn main() { run().unwrap() }
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="tempdir"></a>
### `tempdir = "0.3.5"` &emsp; [📖][d-tempdir]

The most common way to create temporary directories in Rust,
this crate was once part of the standard library.

**Example**: [`examples/tempdir.rs`]

```rust
extern crate tempdir;

use std::fs::File;
use std::io::Write;
use tempdir::TempDir;

fn main() {
    // Create a directory inside of `std::env::temp_dir()`, named with
    // the prefix "example".
    let tmp_dir = TempDir::new("example").expect("create temp dir");
    let file_path = tmp_dir.path().join("my-temporary-note.txt");
    let mut tmp_file = File::create(file_path).expect("create temp file");
    writeln!(tmp_file, "Brian was here. Briefly.").expect("write temp file");

    // By closing the `TempDir` explicitly, we can check that it has
    // been deleted successfully. If we don't close it explicitly,
    // the directory will still be deleted when `tmp_dir` goes out
    // of scope, but we won't know whether deleting the directory
    // succeeded.
    drop(tmp_file);
    tmp_dir.close().expect("delete temp dir");
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="toml"></a>
### `toml = "0.4.1"` &emsp; [📖][d-toml]

[TOML](https://github.com/toml-lang/toml) is a common format for
configuration files, like [Cargo.toml]. It's easy on the eyes, simple
to parse, and serializes from Rust types with [`serde`](#serde).

**Example**: [`examples/toml.rs`]

```rust
extern crate toml;

use toml::Value;

fn main() {
    let toml = r#"
    [test]
    foo = "bar"
"#;

    let value = toml.parse::<Value>().unwrap();
    println!("{:?}", value);
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="url"></a>
### `url = "1.4.1"` &emsp; [📖][d-url]

The URL parser and type, originally created for [Servo].

**Example**: [`examples/url.rs`]

```rust
extern crate url;

use url::{Url, Host};

fn main() {
    let issue_list_url = Url::parse(
        "https://github.com/rust-lang/rust/issues?labels=E-easy&state=open"
    ).unwrap();

    assert!(issue_list_url.scheme() == "https");
    assert!(issue_list_url.username() == "");
    assert!(issue_list_url.password() == None);
    assert!(issue_list_url.host_str() == Some("github.com"));
    assert!(issue_list_url.host() == Some(Host::Domain("github.com")));
    assert!(issue_list_url.port() == None);
    assert!(issue_list_url.path() == "/rust-lang/rust/issues");
    assert!(issue_list_url.path_segments().map(|c| c.collect::<Vec<_>>()) ==
            Some(vec!["rust-lang", "rust", "issues"]));
    assert!(issue_list_url.query() == Some("labels=E-easy&state=open"));
    assert!(issue_list_url.fragment() == None);
    assert!(!issue_list_url.cannot_be_a_base());
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


## About `stdx`

Rust has a lovely and portable standard library, but it is not
featureful enough to write software of any great
sophistication. Compared to common platforms including Java, Python,
and Go, Rust's standard library is small.

In Rust, the libraries we use for even simple tasks live and evolve on
[crates.io]. This affords the Rust community freedom to experiment -
discovering the Rustiest solutions to even common problems can take
quite some iteration - but it also means that we're in for a slow
evolutionary process to converge around the best of those solutions. In
the meantime, you just have to know which crates to use for what.

`stdx` contains some of the most important crates in Rust. I mean
it. If Rust had a more expansive standard library, many of the `stdx`
crates would be in it, or at least the features they provide. Many of
the crates of `stdx` are maintained by the same authors as the Rust
standard library, and they are designed to be idiomatic and
interoperable. These are core elements of the crate ecosystem that
all Rusticians should be aware of.

[crates.io]: https://www.crates.io

## How to use `stdx`

`stdx` is primarily a teaching tool. New and old Rust programmers
alike will get the most from it by digesting [the list](#list) of
`stdx` crates, each entry of which links to a description of the crate
along with _an example of its basic use_.

These examples are full working source and are intended to get you
up and running with any of the `stdx` crates _immediately_. Just
copy the crate name and version exactly as written into the `dependencies`
section of your `Cargo.toml` like so:

```toml
[dependencies]
bitflags = "0.9.1"
```

Then copy the full example into your `examples` directory, like
so:

**Example**: [`examples/bitflags.rs`]

```rust
#[macro_use]
extern crate bitflags;

bitflags! {
    struct Flags: u32 {
        const FLAG_A       = 0b00000001;
        const FLAG_B       = 0b00000010;
        const FLAG_C       = 0b00000100;
        const FLAG_ABC     = FLAG_A.bits
                           | FLAG_B.bits
                           | FLAG_C.bits;
    }
}

fn main() {
    let e1 = FLAG_A | FLAG_C;
    let e2 = FLAG_B | FLAG_C;
    assert_eq!((e1 | e2), FLAG_ABC);   // union
    assert_eq!((e1 & e2), FLAG_C);     // intersection
    assert_eq!((e1 - e2), FLAG_A);     // set difference
    assert_eq!(!e2, FLAG_A);           // set complement
}
```

Then execute the following:

```sh
cargo run --example bitflags
```

And suddenly you are a slightly-experienced user of that crate.
Now click on the [📖][d-bitflags] icon to get the rest of the story.

Convinced? [Go check out that list](#list).


## Why use `stdx`?

As a learning tool, I hope the benefit will be evident from a straight
read-through. But `stdx`, and tools like it, may provide important
benefits to users in the future.

To be clear, `stdx` is experimental. A lot of the below is
speculative.

`stdx` provides assurances that the versions of crates it specifes
work together correctly in a wide variety of configurations. Today
those assurances are few, but they will grow. And these types of
assurances will become increasingly valuable to Rust.

As of now, the only validation `stdx` provides is that the exact
versions of the `stdx` crates resolve correctly by Cargo, and that
they build on Linux and Windows. That is already beneficial by
uncovering problematic combinations and incorrect semver
specifications. Here are some other assurances that `stdx` will
enable:

* Additional integration test cases between the `stdx` crates
* Testing of all `stdx` crates' own test suites using the `stdx` version lock
* Testing on all tier 1 platforms
* Testing on tier 2 platforms
* Enforcement and coverage of `serde` features and interop
* Enforcement of other compile-time feature standards
* `stdx` as version lock - you don't even have to call into it. Just
  link to it and it locks down a chunk of your crate graph to
  known-good combinaitons.
* Ecosystem wide testing using `stdx` version lock - eventually we
  will be able to say which crates are known to work correctly
  with `stdx`.
* The more people use the `stdx` version lock the more assurance they
  get they get. This plays into future Rust's LTS directions.

By applying high quality standards to a small selection of critical
crates we can create a high degree of confidence in a larger core of
the Rust ecosystem.


## Selection criteria

The criteria for inclusion in `stdx` is conservative, and fuzzy. It's
mostly crates that are pretty super important, considering criteria
like

- portability
- quality
- conformance to conventions
- documentation
- interoperability with other crates
- reliability of maintainers
- de-facto adoption
- historical context and precedent

`stdx` is focused on core features, crates that are quintessentially
Rust and relied on by many Rust programs. It is intentionally
limited for the sake of simplicity and ease of comprehension.

All crates must work on Rust's tier-1 platforms, currently x86 Linux,
OS X, and Windows.


## Contributing

See [CONTRIBUTING.md].


## License

`stdx` and the crates it links to are licensed under various
[permissive, BSD-like][perm] licenses. In lay-terms these licenses
allow their code to be used and distributed freely, and are compatible
with [Rust's own license (MIT/Apache 2)][rustlice].

`stdx` itself is dual MIT/Apache 2 licensed, like Rust, and the
copyright is owned by its contributors.

[perm]: https://en.wikipedia.org/wiki/Permissive_free_software_licence
[rustlice]: https://github.com/rust-lang/rust/blob/master/COPYRIGHT


<!-- links -->

<!-- stdx crates -->

[`bitflags = "0.9.1"`]: #bitflags
[`byteorder = "1.0.0"`]: #byteorder
[`chrono = "0.3.1"`]: #chrono
[`clap = "2.24.2"`]: #clap
[`error-chain = "0.10.0"`]: #error-chain
[`flate2 = "0.2.19"`]: #flate2
[`serde_json = "1.0.2"`]: #serde_json
[`lazy_static = "0.2.8"`]: #lazy_static
[`libc = "0.2.23"`]: #libc
[`log = "0.3.8"`]: #log
[`memmap = "0.5.2"`]: #memmap
[`ndarray = "0.9.1"`]: #ndarray
[`num = "0.1.37"`]: #num
[`rand = "0.3.15"`]: #rand
[`rayon = "0.7.1"`]: #rayon
[`regex = "0.2.2"`]: #regex
[`reqwest = "0.6.2"`]: #reqwest
[`serde = "1.0.8"`]: #serde
[`tempdir = "0.3.5"`]: #tempdir
[`tar = "0.4.13"`]: #tar
[`toml = "0.4.1"`]: #toml
[`url = "1.4.1"`]: #url

<!-- stdx crate doc links -->

[d-bitflags]: https://docs.rs/bitflags/0.9.1/bitflags/
[d-byteorder]: https://docs.rs/byteorder/1.0.0/byteorder/
[d-chrono]: https://docs.rs/chrono/0.3.1/chrono/
[d-clap]: https://docs.rs/clap/2.24.2/clap/
[d-error-chain]: https://docs.rs/error-chain/0.8.1/error_chain/
[d-flate2]: https://docs.rs/flate2/0.2.19/flate2/
[d-lazy_static]: https://docs.rs/lazy_static/0.2.8/lazy_static
[d-libc]: https://docs.rs/libc/0.2.23/libc/
[d-log]: https://docs.rs/log/0.3.8/log/
[d-memmap]: https://docs.rs/memmap/0.5.2/memmap/
[d-ndarray]: https://docs.rs/ndarray/0.9.1/ndarray/
[d-num]: https://docs.rs/num/0.1.37/num/
[d-rand]: https://docs.rs/rand/0.3.15/rand/
[d-rayon]: https://docs.rs/rayon/0.7.1/rayon/
[d-regex]: https://docs.rs/regex/0.2.2/regex/
[d-reqwest]: https://docs.rs/reqwest/0.6.2/reqwest/
[d-serde]: https://docs.rs/serde/1.0.8/serde/
[d-serde_json]: https://docs.rs/serde_json/1.0.2/serde_json/
[d-tar]: https://docs.rs/tar/0.4.13/tar/
[d-tempdir]: https://docs.rs/tempdir/0.3.5/tempdir/
[d-toml]: https://docs.rs/toml/0.4.1/toml/
[d-url]: https://docs.rs/url/1.4.1/url/

<!-- examples -->

[`examples/bitflags.rs`]: examples/bitflags.rs
[`examples/byteorder.rs`]: examples/byteorder.rs
[`examples/chrono.rs`]: examples/chrono.rs
[`examples/clap.rs`]: examples/clap.rs
[`examples/error-chain.rs`]: examples/error-chain.rs
[`examples/flate2.rs`]: examples/flate2.rs
[`examples/lazy_static.rs`]: examples/lazy_static.rs
[`examples/libc.rs`]: examples/libc.rs
[`examples/log.rs`]: examples/log.rs
[`examples/ndarray.rs`]: examples/ndarray.rs
[`examples/num.rs`]: examples/num.rs
[`examples/rand.rs`]: examples/rand.rs
[`examples/rayon.rs`]: examples/rayon.rs
[`examples/regex.rs`]: examples/regex.rs
[`examples/reqwest.rs`]: examples/reqwest.rs
[`examples/serde.rs`]: examples/serde.rs
[`examples/json.rs`]: examples/json.rs
[`examples/tar.rs`]: examples/tar.rs
[`examples/tempdir.rs`]: examples/tempdir.rs
[`examples/toml.rs`]: examples/toml.rs
[`examples/url.rs`]: examples/url.rs

<!-- Supplemental crates -->

[`env_logger = "0.4.3"`]: https://docs.rs/env_logger/0.4.3/env_logger/
[`serde_derive = "1.0.8"`]: https://docs.rs/serde_derive/1.0.8/serde_derive

<!-- Alternative crates -->

[`json`]: https://docs.rs/json
[`log4rs`]: https://docs.rs/log4rs
[`rustc-serialize`]: https://docs.rs/rustc-serialize
[`slog`]: https://docs.rs/slog
[`quick-error`]: https://docs.rs/quick-error
[`docopt`]: https://docs.rs/docopt

<!-- other links -->

[DEFLATE]: https://en.wikipedia.org/wiki/DEFLATE
[error handling]: https://rust-lang.github.io/book/ch09-00-error-handling.html
['life before main']: https://isocpp.org/wiki/faq/ctors#static-init-order
[fast]: http://blog.burntsushi.net/ripgrep/
[flate2]: #flate2
[JSON]: http://json.org/
[Cargo.toml]: http://doc.crates.io/manifest.html
[Servo]: https://servo.org
