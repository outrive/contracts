# outrive/contracts

**Contract ABIs and on-chain integration config for OUTRIVE**

This repository contains the contract ABIs and chain configuration used by OUTRIVE to interact with Virtuals Protocol on Robinhood Chain.

---

## Chain

| Network | Chain ID | RPC | Explorer |
|---|---|---|---|
| Robinhood Mainnet | 4663 | https://rpc.mainnet.chain.robinhood.com | https://robinhoodchain.blockscout.com |
| Robinhood Testnet | 46630 | https://rpc.testnet.chain.robinhood.com | https://explorer.testnet.chain.robinhood.com |

---

## Contracts

### Virtuals Protocol Factory

The entry point for AI agent token launches on Robinhood Chain. Deployed and maintained by [Virtuals Protocol](https://app.virtuals.io).

- **Function**: `launch(name, ticker, metadataURI, tokenSupplyParams_, antiSnipeAmount, _agentTokenImplementation, _virtualLP)` - deploys a new bonding-curve agent token
- **Event**: `NewApplication(uint256 indexed applicationId, address indexed sender, address indexed token, ...)`
- **Source**: Verify address and ABI from [Robinhood Chain Blockscout](https://robinhoodchain.blockscout.com)

### AgentTaxV2

Handles creator fee accounting for launched agent tokens. The `projectTaxRecipient` field on each agent token points to this contract.

- **Function**: `claimable(address creator)` - returns unclaimed creator fees
- **Function**: `claim()` - claims accumulated fees to the creator wallet

### Bonding Curve

Each agent token launched through the factory has an associated bonding curve contract. Graduation occurs when the curve reaches the threshold (42,000 $VIRTUAL by default), at which point liquidity migrates to Uniswap V3.

---

## ABI Files

| File | Contract |
|---|---|
| `virtualsFactoryAbi.json` | Virtuals Protocol Instant Launch factory |
| `bondingCurveAbi.json` | Bonding curve (per-token) |
| `agentFactoryAbi.json` | Agent factory — internal factory reference |

> **Note**: These are working ABI stubs used by OUTRIVE. Always verify against the live contract on [Blockscout](https://robinhoodchain.blockscout.com) before production use.

---

## Usage

```typescript
import { createPublicClient, http } from 'viem';
import { defineChain } from 'viem/chains';
import factoryAbi from './virtualsFactoryAbi.json';

const robinhoodChain = defineChain({
  id: 4663,
  name: 'Robinhood Chain',
  nativeCurrency: { name: 'Ether', symbol: 'ETH', decimals: 18 },
  rpcUrls: {
    default: { http: ['https://rpc.mainnet.chain.robinhood.com'] },
  },
  blockExplorers: {
    default: { name: 'Blockscout', url: 'https://robinhoodchain.blockscout.com' },
  },
});

const client = createPublicClient({
  chain: robinhoodChain,
  transport: http(),
});

// Read factory data
const result = await client.readContract({
  address: process.env.VIRTUALS_FACTORY_ADDRESS as `0x${string}`,
  abi: factoryAbi,
  functionName: 'getApplicationInfo',
  args: [applicationId],
});
```

---

## Links

- OUTRIVE: [outrive.io](https://outrive.io)
- Virtuals Protocol: [app.virtuals.io](https://app.virtuals.io)
- Robinhood Chain Explorer: [robinhoodchain.blockscout.com](https://robinhoodchain.blockscout.com)
- Virtuals Protocol Docs: [whitepaper.virtuals.io](https://whitepaper.virtuals.io)

---

## License

MIT
