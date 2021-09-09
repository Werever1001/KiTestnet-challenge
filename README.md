# KiTestnet-challenge
## Relayer between KiChain and Umee builded on Umee side

* Install relayer
```
git clone http@github.com:cosmos/relayer.git
git checkout v0.9.3
cd relayer && make install
```
* Init relayer config
```
rly config init
```

* Create config for two chains into directory relayer
```
cd relayer && mkdir rly-config
cd rly-config
```

groot-011.json
```
{  
 "chain-id": "umee-betanet-1",   
 "rpc-addr": "http://localhost:26657",    
 "account-prefix": "umee",   
 "gas-adjustment": 1.5,  
 "gas-prices": "0.025uumee",   
 "trusting-period": "48h" 
}
```

kichain-t-4.json
```
{
 "chain-id": "kichain-t-4",
 "rpc-addr": "https://rpc-challenge.blockchain.ki:443",
 "account-prefix": "tki",
 "gas-adjustment": 1.5,
 "gas-prices": "0.025utki",
 "trusting-period": "48h"
}
```

* Parse this config files into relayer config
```
rly chains add -f kichain-t-4.json
rly chains add -f groot-011.json
```

* Create both wallets or recover for chains(umee-betanet-1 and kichain-t-4) in relayer
```
rly keys add kichain-t-4 WALLET-KEY-NAME
rly keys add umee-betanet-1 WALLET-KEY-NAME
```

* Add this wallet in config relayer
```
rly chains edit kichain-t-4 key WALLET-KEY-NAME
rly chains edit umee-betanet-1 key WALLET-KEY-NAME
```

* Change timeout for transaction into relayer config
```
nano ~/.relayer/config/config.yaml
```
Change timeout value from 10s  to 60s

* Filling both wallets and checking balances
```
rly q balance kichain-t-4
rly q balance umee-betanet-1
```
    
* Init clients for both chains
```
rly light init kichain-t-4 -f
rly light init umee-betanet-1 -f
```
    
* Create 2 channels between chains (from kichain-t-4 to umee-betanet-1 and from umee-betanet-1 to kichain-t-4)
```
rly paths generate kichain-t-4 umee-betanet-1 fromki --port=transfer
rly paths generate umee-betanet-1 kichain-t-4 transfer --port=transfer
```
    
* Check is channels created
```
rly paths list -d
```
    
final config.yaml looks:
```
global:
  api-listen-addr: :5183
  timeout: 30s
  light-cache-size: 20
chains:
- key: tki127jvzlucwkltfrd4qpnugmfhg3p7an2knhyt7x
  chain-id: kichain-t-4
  rpc-addr: https://rpc-challenge.blockchain.ki:443
  account-prefix: tki
  gas-adjustment: 1.5
  gas-prices: 0.025utki
  trusting-period: 48h
- key: umeeWallet
  chain-id: umee-betanet-1
  rpc-addr: http://localhost:26657
  account-prefix: umee
  gas-adjustment: 1.5
  gas-prices: 0.025uumee
  trusting-period: 48h
paths:
  fromki:
    src:
      chain-id: kichain-t-4
      client-id: 07-tendermint-13
      connection-id: connection-17
      channel-id: channel-61
      port-id: transfer
      order: UNORDERED
      version: ics20-1
    dst:
      chain-id: umee-betanet-1
      client-id: 07-tendermint-1
      connection-id: connection-1
      channel-id: channel-0
      port-id: transfer
      order: UNORDERED
      version: ics20-1
    strategy:
      type: naive
  transfer:
    src:
      chain-id: umee-betanet-1
      client-id: 07-tendermint-1
      connection-id: connection-1
      channel-id: channel-0
      port-id: transfer
      order: UNORDERED
      version: ics20-1
    dst:
      chain-id: kichain-t-4
      client-id: 07-tendermint-13
      connection-id: connection-17
      channel-id: channel-61
      port-id: transfer
      order: UNORDERED
      version: ics20-1
    strategy:
      type: naive
 ```

* Check is chains ready 
```
rly chains list
```
output:
```
0: kichain-t-4          -> key(✔) bal(✔) light(✔) path(✔)
1: umee-betanet-1       -> key(✔) bal(✔) light(✔) path(✔)
```

* Open channels
```
rly tx link fromki
```
waiting output about succesful creatin channel
```
rly tx link transfer
```
waiting output about succesful creatin channel

* If outputs are succesful - sending tokens
from kichain-t-4 to umee-betanet-1:
```
rly tx transfer kichain-t-4 umee-betanet-1 1000000utki YOUR_UMEE_WALLET_ADDRESS --path fromki
```
from umee-betanet-1 to kichain-t-4:
```
rly tx transfer umee-betanet-1 kichain-t-4 1000000umee YOUR_KICHAIN_WALLET_ADDRESS --path transfer
```
    
# Congratulations!! We are done!

* hashes from transactions:

from kichain-t-4 to umee-betanet-1:

https://ki.thecodes.dev/tx/3A8B4800199BC88017E077671B0F629E8EB73F754AED1AEB2C7DAD9FBBAA6BCB
https://ki.thecodes.dev/tx/CF8EF1DAFD1B221961D6A16F1574AD51FDB84A272FF2C614CF53C59CB4A1E765
https://ki.thecodes.dev/tx/E980A4450CD51CC7082705559051FCF8F3B777ED76A30947E46A90DB31517FE2
https://ki.thecodes.dev/tx/CA7D63ED5A2074328F16E4E6E680D9CC077EBF57A901C7AA2E27E7CA447CBED0
https://ki.thecodes.dev/tx/62A13D3A66F2D93B64EACB8C717B811F5E336450DB366C625B89A9AF5333468F

from umee-betanet-1 to kichain-t-4:

https://explorer-umee.nodes.guru/transactions/2D9797DE98FF135233C57AA886011D569E8A2DF8EAF6D1BEA50B7619E31F9832
https://explorer-umee.nodes.guru/transactions/DFC0292B209BE707DE8CCFA9A531290278510D8A9C37A21F8AD95576650B8DBB
https://explorer-umee.nodes.guru/transactions/0634A6162713CAFB118BD294DD2F0A22AFED634BCA5BA9404F1A089A6ACDA29C
https://explorer-umee.nodes.guru/transactions/09ABD8F941D197C9B9A2F49DC9B1238474773A147E7290ACA234D2D660C11BDF
https://explorer-umee.nodes.guru/transactions/64216D8400B43141862218B50A42C5608B148DE5D9FFACAA566EC7594E7C8540
    
