# nim-eth2-official-tests

This repo reformats the official Ethereum 2 tests to a format suitable for [Nimbus](https://github.com/status-im/nimbus)/[nim-beacon-chain](https://github.com/status-im/nim-beacon-chain).

## Cloning the repo

Due to upstream usage of [Git LFS](https://git-lfs.github.com) to store the large test vectors,
there is an extra step after cloning the repo:

```
git clone https://github.com/status-im/nim-eth2-official-tests
cd nim-eth2-official-tests
git lfs install
git submodule update --init --recursive
```

## Usage in nim-beacon-chain

This repository is meant to be submoduled in `nim-beacon-chain/tests/official/` with the `fixtures` name.

From the nim-beacon-chain project folder:

```sh
git submodule add https://github.com/status-im/nim-eth2-official-tests ./tests/official/fixtures
```


This repository allows use to workaround the current following limitations:
  - no YAML support in [nim-serialization](https://github.com/status-im/nim-serialization) library
    which allows well-tested serialization and deserialization into and from Ethereum types.
  - [NimYAML](https://nimyaml.org) uses int by default for numerals and cannot deserialize
    18446744073709551615 (2^64-1), the FAR_FUTURE_SLOT constant.
  - All those workarounds requires an intermediate reformatted JSON file, but the tests are huge (100k+ lines)
    and will cause review issues in the main repo.

## Commands

You can compile and run the `batch_convert.nim` file to convert the supported tests.
It needs to be run from the root of this project directory.

You can also convert individual files directly from the `fixture_utils.nim` file with the following format:
```
fixture_utils path/to/input.yam path/to/output.json
```

Due to limitation in NimYAML library you might need to stringify `2^64 - 1` as NimYAML interprets
all numbers as `int` (`int64` on 64-bit platforms) while we need `uint64`.

You can use the following comment in an UNIX environment
```
sed 's/18446744073709551615/"18446744073709551615"/g' path/to/original.yaml > path/to/stringified.yaml
```
