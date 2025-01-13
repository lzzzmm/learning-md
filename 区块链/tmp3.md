重新委托创世密钥：
```
cardano-cli alonzo genesis key-gen-delegate \
--verification-key-file delegate-keys/new.shelley.delegate.000.vkey \
--signing-key-file delegate-keys/new.shelley.delegate.000.skey \
--operational-certificate-issue-counter-file delegate-keys/new.shelley.delegate.000.certificate.counter

cardano-cli key non-extended-key \
--extended-verification-key-file genesis-keys/shelley.000.vkey \
--verification-key-file genesis-keys/non.e.shelley.000.vkey

cardano-cli alonzo  governance  create-genesis-key-delegation-certificate \
  --genesis-verification-key-file genesis-keys/non.e.shelley.000.vkey \
  --genesis-delegate-verification-key-file delegate-keys/new.shelley.delegate.000.vkey \
  --vrf-verification-key-file delegate-keys/shelley.000.vrf.vkey \
  --out-file genesis-keys/genesis.delegation.cert

cardano-cli query utxo --address $(cat utxo-keys/convert_shelley.addr) --testnet-magic 42 --socket-path $HOME/src/privatenet/db/node.socket
 CHANGE=$((30000000000000000 - 1000000))

cardano-cli alonzo transaction build-raw \
--fee 1000000 \
--tx-in $(cardano-cli query utxo --address $(cat utxo-keys/convert_shelley.addr) --socket-path $HOME/src/privatenet/db/node.socket  --testnet-magic 42 --out-file  /dev/stdout | jq -r 'keys[]') \
--tx-out $(cat utxo-keys/convert_shelley.addr)+$CHANGE \
--certificate-file genesis-keys/genesis.delegation.cert \
--out-file transactions/tx9.raw

cardano-cli alonzo transaction sign \
--tx-body-file transactions/tx9.raw \
--signing-key-file utxo-keys/convert_shelley.skey \
--signing-key-file genesis-keys/shelley.000.skey \
--out-file transactions/tx9.signed \
--testnet-magic 42

cardano-cli alonzo transaction submit \
--testnet-magic 42 \
--tx-file transactions/tx9.signed \
--socket-path $HOME/src/privatenet/db/node.socket
```


```
cardano-cli alonzo governance action create-protocol-parameters-update \
--genesis-verification-key-file genesis-keys/non.e.shelley.000.vkey \
--out-file transactions/update.v7.proposal \
--epoch $(cardano-cli query tip --socket-path $HOME/src/privatenet/db/node.socket --testnet-magic 42 | jq .epoch) \
--protocol-major-version "7" \
--protocol-minor-version "0" 


CHANGE=$(($(cardano-cli query utxo --socket-path $HOME/src/privatenet/db/node.socket --address $(cat utxo-keys/convert_shelley.addr) --testnet-magic 42 --out-file  /dev/stdout | jq -cs '.[0] | to_entries | .[] | .value.value.lovelace') - 1000000))


cardano-cli alonzo transaction build-raw \
--fee 1000000 \
--invalid-hereafter $(expr $(cardano-cli query tip --socket-path $HOME/src/privatenet/db/node.socket --testnet-magic 42 | jq .slot) + 1000) \
--tx-in $(cardano-cli query utxo --address $(cat utxo-keys/convert_shelley.addr) --testnet-magic 42 --socket-path $HOME/src/privatenet/db/node.socket --out-file  /dev/stdout | jq -r 'keys[]') \
--tx-out $(cat utxo-keys/convert_shelley.addr)+$CHANGE \
--update-proposal-file transactions/update.v7.proposal \
--out-file transactions/update.v7.proposal.txbody


cardano-cli alonzo transaction sign \
--tx-body-file transactions/update.v7.proposal.txbody \
--signing-key-file utxo-keys/convert_shelley.skey \
--signing-key-file delegate-keys/new.shelley.delegate.000.skey \
--signing-key-file delegate-keys/shelley.000.skey \
--out-file transactions/update.v7.proposal.txsigned \
--testnet-magic 42 

cardano-cli alonzo transaction submit --testnet-magic 42 --tx-file transactions/update.v7.proposal.txsigned --socket-path $HOME/src/privatenet/db/node.socket
```