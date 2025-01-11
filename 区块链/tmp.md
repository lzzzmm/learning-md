 ```
 export CARDANO_NODE_SOCKET_PATH="$HOME/src/cardano-config/db/node.socket"
 export PATH=$PATH:/$HOME/src/cardano-node/bin
 mkdir privatenet
 cd privatenet
 mkdir cardano-env-config db nodeConfig
cd cardano-env-config/
```

```
wget -P template/ https://raw.githubusercontent.com/input-output-hk/iohk-nix/master/cardano-lib/testnet-template/alonzo.json
wget -P template/ https://raw.githubusercontent.com/input-output-hk/iohk-nix/master/cardano-lib/testnet-template/byron.json
wget -P template/ https://raw.githubusercontent.com/input-output-hk/iohk-nix/master/cardano-lib/testnet-template/config.json
wget -P template/ https://raw.githubusercontent.com/input-output-hk/iohk-nix/master/cardano-lib/testnet-template/shelley.json
wget -P template/ https://raw.githubusercontent.com/input-output-hk/iohk-nix/master/cardano-lib/testnet-template/conway.json
```


config.json:
```
  "EnableP2P": false,
  "ExperimentalProtocolsEnabled": false,
  "ExperimentalHardForksEnabled": false,
  "ApplicationName": "cardano-node"
```

shelley.json
```
  "updateQuorum": 0,
  "networkId": "Mainnet",
  "networkMagic": 1001,
  "slotLength": 1000,
```

conway.json
```
"committeeMinSize": 0,
```

```
cat > ~/src/privatenet/nodeConfig/topology.json <<EOF
{
   "Producers": []
 }
EOF
```

```
cardano-cli conway genesis create-cardano \
--genesis-dir ./ \
--gen-genesis-keys 1 \
--gen-utxo-keys 1 \
--start-time $(date -u -d "now + 2 minutes" +%FT%Tz) \
--supply 30000000000000000 \
--security-param 45 \
--slot-length 100 \
--slot-coefficient 5/100 \
--mainnet \
--byron-template template/byron.json \
--shelley-template template/shelley.json \
--alonzo-template template/alonzo.json \
--conway-template template/conway.json \
--node-config-template template/config.json
```

```
mv node-config.json config.json
mv delegate-keys/byron.000* delegate-keys/shelley.000* ../nodeConfig/
```

```
cardano-node run \
--config $HOME/src/privatenet/cardano-env-config/config.json \
--database-path $HOME/src/privatenet/db/ \
--socket-path $HOME/src/privatenet/db/node.socket \
--host-addr 127.0.0.1 \
--port 3001 \
--topology $HOME/src/privatenet/nodeConfig/topology.json \
--shelley-kes-key $HOME/src/privatenet/cardano-env-config/delegate-keys/shelley.000.kes.skey \
--shelley-vrf-key $HOME/src/privatenet/cardano-env-config/delegate-keys/shelley.000.vrf.skey \
--shelley-operational-certificate $HOME/src/privatenet/cardano-env-config/delegate-keys/shelley.000.opcert.json \
--byron-delegation-certificate  $HOME/src/privatenet/cardano-env-config/delegate-keys/byron.000.cert.json \
--byron-signing-key   $HOME/src/privatenet/cardano-env-config/delegate-keys/byron.000.key
```

目前链上是alonzo，需要升级的话是提交硬分叉的提案：
```
cardano-cli conway governance action create-hardfork \
--mainnet \
--governance-action-deposit 0 \
--deposit-return-stake-verification-key-file  ~/src/privatenet/cardano-node-con/stake-keys/stake.vkey \
--anchor-url "
https://github.com/cardano-foundation/CIPs/raw/refs/heads/master/CIP-0119/examples/drep.jsonld" \
--anchor-data-hash "fecc1773db89b45557d82e07719c275f6877a6cadfd2469f4dc5a7df5b38b4a4" \
--protocol-major-version 7 \
--protocol-minor-version 0 \
--out-file ~/src/privatenet/cardano-node-con/governance-action.json
```

```
cardano-cli conway stake-address key-gen --signing-key-file stake.skey --verification-key-file stake.vkey
```