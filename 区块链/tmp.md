 ```
 export CARDANO_NODE_SOCKET_PATH="$HOME/src/cardano-config/db/node.socket"
 export PATH=$PATH:/$HOME/src/cardano-node/bin
 mkdir privatenet
 cd privatenet
 mkdir cardano-env-config db nodeConfig
cd cardano-env-config/
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
cardano-node run \
--config $HOME/src/privatenet/cardano-env-config/config.json \
--database-path $HOME/src/privatenet/db/ \
--socket-path $HOME/src/privatenet/db/node.socket \
--host-addr 172.23.15.16 \
--port 3001 \
--topology $HOME/src/privatenet/nodeConfig/topology.json \
--shelley-kes-key $HOME/src/privatenet/cardano-env-config/delegate-keys/shelley.000.kes.skey \
--shelley-vrf-key $HOME/src/privatenet/cardano-env-config/delegate-keys/shelley.000.vrf.skey \
--shelley-operational-certificate $HOME/src/privatenet/cardano-env-config/delegate-keys/shelley.000.opcert.json \
--byron-delegation-certificate  $HOME/src/privatenet/cardano-env-config/delegate-keys/byron.000.cert.json \
--byron-signing-key   $HOME/src/privatenet/cardano-env-config/delegate-keys/byron.000.key
```

