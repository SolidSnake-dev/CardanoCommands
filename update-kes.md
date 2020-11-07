###BLOCK PRODUCER

NODE_HOME=/home/blockproducing/cardano-node
slotNo=$(cardano-cli shelley query tip --mainnet | jq -r '.slotNo')
slotsPerKESPeriod=$(cat $NODE_HOME/block-producing/mainnet-shelley-genesis.json | jq -r '.slotsPerKESPeriod')
kesPeriod=$((${slotNo} / ${slotsPerKESPeriod}))
startKesPeriod=$(( ${kesPeriod} - 0 ))
echo startKesPeriod: \${startKesPeriod}

### BLOCK PRODUCER

cd \$NODE_HOME
cardano-cli shelley node key-gen-KES \
 --verification-key-file kes.vkey \
 --signing-key-file kes.skey

## AIR GAPPED

POOLKEYS=/home/cardano-node/pool-keys

cardano-cli shelley node issue-op-cert \
--kes-verification-key-file $POOLKEYS/kes.vkey \
--cold-signing-key-file $POOLKEYS/cold.skey \
--operational-certificate-issue-counter $POOLKEYS/cold.counter \
--kes-period 99 \
--out-file $POOLKEYS/data/node.cert
