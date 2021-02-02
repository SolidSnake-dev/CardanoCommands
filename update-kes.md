### BLOCK PRODUCER

NODE_HOME=/home/blockproducing/cardano-node

slotNo=$(cardano-cli query tip --mainnet | jq -r '.slotNo')

slotsPerKESPeriod=$(cat $NODE_HOME/block-producing/mainnet-shelley-genesis.json | jq -r '.slotsPerKESPeriod')

kesPeriod=$((${slotNo} / ${slotsPerKESPeriod}))

startKesPeriod=${kesPeriod}

echo startKesPeriod: ${startKesPeriod}

### BLOCK PRODUCER

cd /home/blockproducing/pool-keys

cardano-cli node key-gen-KES \
 --verification-key-file kes.vkey \
 --signing-key-file kes.skey

## AIR GAPPED

POOLKEYS=/home/cardano-node/pool-keys
chmod u+rwx POOLKEYS

cardano-cli node issue-op-cert \
--kes-verification-key-file $POOLKEYS/kes.vkey \
--cold-signing-key-file $POOLKEYS/cold.skey \
--operational-certificate-issue-counter $POOLKEYS/cold.counter \
--kes-period 99 \
--out-file $POOLKEYS/data/node.cert


cd $NODE_HOME
chmod u+rwx $HOME/cold-keys
cardano-cli node issue-op-cert \
    --kes-verification-key-file kes.vkey \
    --cold-signing-key-file $HOME/cold-keys/node.skey \
    --operational-certificate-issue-counter $HOME/cold-keys/node.counter \
    --kes-period <startKesPeriod> \
    --out-file node.cert
chmod a-rwx $HOME/cold-keys
