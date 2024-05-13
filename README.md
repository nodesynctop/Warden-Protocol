# Warden-Protocol

Website: https://wardenprotocol.org/

Twitter: https://twitter.com/wardenprotocol

Telegram: https://t.me/wardenprotocol

Discord: https://discord.gg/wardenprotocol

Docs: https://docs.wardenprotocol.org/learn/

Explorer: https://warden-explorer.paranorm.pro/warden/staking

Explorer NodeSync: https://explorer.nodesync.top/Warden-Testnet/staking

**Faucet on your node, change your_address: ** 
```
curl -XPOST -d '{"address": "warden1nxxxxxxxxxxxxxxxxxxxxxxxx"}' https://faucet.buenavista.wardenprotocol.org
```

# 1. Minimum hardware requirement

4 Cores, 8G Ram, 200G SSD, Ubuntu 22.04

# 2. Auto Install
```
if ! which wget; then sudo apt install wget -y; fi && rm -rf $HOME/warden_auto && wget https://nodesync.top/warden_auto && chmod +x warden_auto && ./warden_auto
```
## 2.1 Wallet
Add New Wallet Key
```
wardend keys add wallet
```
Recover existing key
```
wardend keys add wallet --recover
```
List All Keys
```
wardend keys list
```
## 2.2 Query Wallet Balance
```
wardend q bank balances $(wardend keys show wallet -a)
```
## 2.3 Check sync status
False is synced
```
wardend status 2>&1 | jq .SyncInfo.catching_up
```
## 2.4 Create Validator

### Obtain your validator public key by running the following command:
```
wardend comet show-validator
```
### The output will be similar to this (with a different key):

{"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="}
### Then, create a file named `validator.json` with the following content:

```
nano $HOME/validator.json
```
### Change your info, from "pubkey" to "details"  and Save them

```
{    
    "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="},
    "amount": "9000000uward",
    "moniker": "your-node-moniker",
    "identity": "eqlab testnet validator",
    "website": "optional website for your validator",
    "security": "optional security contact for your validator",
    "details": "optional details for your validator",
    "commission-rate": "0.1",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
```
### Finally, we're ready to submit the transaction to create the validator:
```
wardend tx staking create-validator $HOME/validator.json \
--from=wallet \
--chain-id=buenavista-1 \
--fees=500uward
```

## 2.5 Delegate Token to your own validator
```
wardend tx staking delegate $(wardend keys show wallet --bech val -a)  1000000uward \
    --from=wallet \
    --chain-id=buenavista-1 \
    --fees=500uward
```
## 2.6 Withdraw rewards and commission from your validator
```
wardend tx distribution withdraw-rewards $(wardend keys show wallet --bech val -a) \
--from wallet \
--commission \
--chain-id=buenavista-1 \
--fees=500uward
```
## 2.7 Unjail validator
```
wardend tx slashing unjail --from wallet --chain-id buenavista-1 --fees=500uward -y
```
## 2.9 Services Management
```
# Reload Service
sudo systemctl daemon-reload

# Enable Service
sudo systemctl enable wardend

# Disable Service
sudo systemctl disable wardend

# Start Service
sudo systemctl start wardend

# Stop Service
sudo systemctl stop wardend

# Restart Service
sudo systemctl restart wardend

# Check Service Status
sudo systemctl status wardend

# Check Service Logs
sudo journalctl -u wardend -f --no-hostname -o cat
```
# 3. Backup Validator
Important
```
cat $HOME/.warden/config/priv_validator_key.json
```
# 4. Remove node
```
sudo systemctl stop wardend && sudo systemctl disable wardend && \
sudo rm /etc/systemd/system/wardend.service && sudo systemctl daemon-reload && \
rm -rf $HOME/.warden && \
rm -rf $HOME/wardenprotocol && \
rm -rf $HOME/warden_auto && \
rm -f $(which wardend) && \
rm -rf $HOME/warden
```


