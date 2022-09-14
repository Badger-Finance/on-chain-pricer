# Fair Selling

## TODO: Readme for V4

A [BadgerDAO](https://app.badger.com/) sponsored repo of Open Source Contracts for:、
- Calculating onChain Prices
- Executing the best onChain Swap

## Why bother

We understand that we cannot prove a optimal price because at any time a new source of liquidity may be available and the contract cannot adapt.

However we believe that given a set of constraints (available Dexes, handpicked), we can efficiently compute the best trade available to us

In exploring this issue we aim to:
- Find the most gas-efficient way to get the best executable price (currently 120 /150k per quote, from 1.6MLN)
- Finding the most reliable price we can, to determine if an offer is fair or unfair (Cowswap integration)
- Can we create a "trustless swap" that is provably not frontrun nor manipulated?
- How would such a "self-defending" contract act and how would it be able to defend itself, get the best quote, and be certain of it (with statistical certainty)

## Current Release V0.3 - Pricer

# Notable Contracts

## OnChainPricingMainnet

Given a tokenIn, tokenOut and AmountIn, returns a Quote from the most popular dexes

- `OnChainPricingMainnet` -> Fully onChain math to find best, single source swap (no fragmented swaps yet)
- `OnChainPricingMainnetLenient` -> Slippage tollerant version of the Pricer

### Dexes Support
- Curve
- UniV2
- UniV3
- Balancer
- Sushi

Covering >80% TVL on Mainnet. (Prob even more)

## Example Usage

BREAKING CHANGE: V3 is back to `view` even for Balancer and UniV3 functions

### isPairSupported

Returns true if the pricer will return a non-zero quote
NOTE: This is not proof of optimality

```solidity
    /// @dev Given tokenIn, out and amountIn, returns true if a quote will be non-zero
    /// @notice Doesn't guarantee optimality, just non-zero
    function isPairSupported(address tokenIn, address tokenOut, uint256 amountIn) external returns (bool)
```

In Brownie
```python
quote = pricer.isPairSupported(t_in, t_out, amt_in)
```

### findOptimalSwap

Returns the best quote given the various Dexes, used Heuristics to save gas (V0.3 will focus on this)
NOTE: While the function says optimal, this is not optimal, just best of the bunch, optimality may never be achieved fully on-chain

```solidity
    function findOptimalSwap(address tokenIn, address tokenOut, uint256 amountIn) external virtual returns (Quote memory)
```

In Brownie
```python
quote = pricer.findOptimalSwap(t_in, t_out, amt_in)
```


# Mainnet Pricing Lenient

Variation of Pricer with a slippage tollerance



# Notable Tests

## Proof that the math is accurate with gas savings

These tests compare the PricerV3 (150k per quote) against V2 (1.6MLN per quote)

```
brownie test tests/heuristic_equivalency/test_heuristic_equivalency.py

```

## Benchmark specific AMM quotes
TODO: Improve to just use the specific quote

```
brownie test tests/gas_benchmark/benchmark_pricer_gas.py --gas
```

## Benchmark coverage of top DeFi Tokens

TODO: Add like 200 tokens
TODO: Compare against Coingecko API or smth

```
brownie test tests/gas_benchmark/benchmark_token_coverage.py --gas
```

## Notable Test from V2

Run V3 Pricer against V2, to confirm results are correct, but with gas savings

```
brownie test  tests/heuristic_equivalency/test_heuristic_equivalency.py
