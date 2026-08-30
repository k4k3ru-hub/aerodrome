# Aerodrome

Go protocol clients for Aerodrome Slipstream.

## Supported deployments

- Base mainnet current Slipstream deployment
- Base mainnet initial/legacy Slipstream deployment

The legacy deployment remains supported because active pools retain liquidity and Swap activity. Deployment generation and current pool Swap fee are independent; retrieve the latter through `FactoryClient.GetSwapFee`.

## Go module

```text
github.com/k4k3ru-hub/aerodrome/go
```

The `slipstream` package provides:

- canonical `protocol.PoolKey` values based on token pair and tick spacing
- Factory-based pool resolution and current Swap fee lookup
- exact-input and exact-output QuoterV2 calls
- `slot0`, active liquidity, and tick-spacing reads
- block-range Swap filtering
- WebSocket Swap subscriptions

## Composition

Create one client for each deployment. Pool addresses must first be resolved from that deployment's Factory.

```go
configuredDeployment, err := deployment.ByID(
	deployment.IDAerodromeBaseMainnetCurrent,
)
if err != nil {
	return err
}

client, err := slipstream.NewClient(slipstream.ClientParams{
	HTTPRPCClient: httpRPCClient,
	WSRPCClient:   wsRPCClient,
	Deployment:    configuredDeployment,
	SwapSources: []slipstream.SwapSource{
		{
			PoolAddress: poolAddress,
			PoolKey:     poolKey,
		},
	},
})
if err != nil {
	return err
}
```

All amounts use token base units. State and Quote methods accept an explicit block number; `nil` uses the latest state. Swap fees use a `1e-6` denominator and may change dynamically.
