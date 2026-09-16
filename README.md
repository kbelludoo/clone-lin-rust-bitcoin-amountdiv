# clone-lin-rust-bitcoin-amountdiv

**Class: EXPERIMENTAL.** This is not a wallet, not rust-bitcoin, and not Bitcoin Core.

LIN scalar kernel of [`Amount::checked_div`](https://github.com/rust-bitcoin/rust-bitcoin/blob/1cbf4bd62ea99c558c6d8ef794edbd984a2a4684/units/src/amount/unsigned.rs) / `checked_rem` / `div_by_weight_floor`.

- Upstream: [rust-bitcoin/rust-bitcoin](https://github.com/rust-bitcoin/rust-bitcoin) commit `1cbf4bd62ea99c558c6d8ef794edbd984a2a4684` (CC0-1.0)
- Results and proofs live in [kbelludoo/lin-open](https://github.com/kbelludoo/lin-open): `src/lin_rust_bitcoin_amountdiv.lin`, `test/prove_rust_bitcoin_amountdiv_external.py`, `docs/RUST_BITCOIN_AMOUNTDIV_CLONE.rulel`

## What this clone contains

| File | Role |
|---|---|
| `lin_rust_bitcoin_amountdiv.lin` | LIN kernel (Compiler 0 / C11 host) |
| `rust_bitcoin_amountdiv_c11.c` | gcc oracle (`unsigned __int128` + limb clone) |
| `rust_bitcoin_amountdiv_scalar.c` | pointer-free C subset for `lin_from_c` v2 |
| `rust_bitcoin_amountdiv_scalar.rs` | no_std scalar fixture for `lin_from_rust` v2 |

## Honest limits

- i64 satoshis: `MAX_MONEY = 21_000_000 * 100_000_000` fits; `Option::None` is `-1` so it does not collide with `Amount::ZERO`.
- `div_by_weight_floor` is `(sats * 4_000_000) / wu` (sat/MvB). The product overflows i64 on `MAX_MONEY`; LIN uses a 128-bit product and fail-closes if the FeeRate does not fit i64.
- Goldens: `checked_div(100,3)=33`, `checked_rem(100,3)=1`, `wfloor(10,200)=200000`, `wfloor(10,300)=133333` rem `100`.
- No PSBT, no script, no network.

## Reproduce (from lin-open)

```bash
make -C transpile/c c0
python3 test/prove_rust_bitcoin_amountdiv_external.py
```
