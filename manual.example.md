Example:

<project_name> = seid
<service_name> = sei
<folder_name> = sei
<type> = pruned
<stage> = mainnet
<domain> = st-snap-1.stakeme.pro
<snapshots-file> = from api-snap (ex. cosmos_data_sei_20250222_020001.tar.lz4)

## Manual
# System Dependencies & Configuration
#-----------------------------------------
sudo apt update > /dev/null 2>&1
sudo apt install curl tmux jq lz4 unzip aria2 wget htop net-tools -y > /dev/null 2>&1
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.<service_name>/config/config.toml
# Download Node Snapshots
#-----------------------------------------
cd $HOME
sudo systemctl stop <project_name>
aria2c -x 16 -s 16 -o <folder_name>-<type>-snap.tar.lz4 https://<domain>/<folder_name>/<stage>/<type>/<snapshots-file>
# Restore Sei Chain Data
#-----------------------------------------
cp $HOME/.<service_name>/data/priv_validator_state.json $HOME/.<service_name>/priv_validator_state.json.backup
rm -r $HOME/.<service_name>/data
tar -I lz4 -xvf ~/<folder_name>-<type>-snap.tar.lz4 -C $HOME/.<service_name>
mv $HOME/.<service_name>/priv_validator_state.json.backup $HOME/.<service_name>/data/priv_validator_state.json
# Monitor, cleanup and restart services
#-----------------------------------------
sudo systemctl restart <project_name>
rm $HOME/<folder_name>-<type>-snap.tar.lz4
sudo journalctl -u <project_name> -f --no-hostname -o cat

## Link

wget https://<domain>/<folder_name>/<stage>/<type>/<snapshots-file>

