# Deploying to Cluster

## Creating the Clusters

Create 5 VMs with the following configuration:

![VM Configuration](imgs/vm.png)

## Connecting to Clusters

```powershell
# Add-VpnRoute.ps1
param(
  [parameter(mandatory = $true)]
  [string]$VmName,
  [parameter()]
  [string]$AzureSubscription
)

if (-not [bool](Get-Command -ErrorAction SilentlyContinue az)) {
  Write-Error "Azure CLI (az) not found. Please install it and run 'az login' before using this script"
  return
}

Write-Host "**Remember to run 'az login' first!"

$args = @('--name', $VmName)
if ($AzureSubscription) {
  $args += '--subscription', $AzureSubscription
}

$destination = az vm list-ip-addresses @args -o tsv --query "[0].virtualMachine.network.publicIpAddresses[0].ipAddress"
if (-not $destination) {
  Write-Error "Couldn't find the public IP address for VM '$VmName' in subscription '$AzureSubscription'"
  return;
}

$interface = Get-NetIPAddress -AddressFamily IPv4 -InterfaceAlias MSFTVPN-Manual | foreach { $_.IPAddress }
if (-not $interface) {
  Write-Error "Couldn't find a local network interface named 'MSFTVPN'"
  return;
}

Write-Host "Adding destination '$destination' to MSFTVPN interface '$interface'..."
route add $destination $interface

```

```powershell
# install.ps1

az account clear
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
az login

az account set --subscription iot-edge-sublib-003

. .\Add-VpnRoute.ps1 stress-1 iot-edge-sublib-003
#ssh -i azureKey.pem azureuser@20.127.195.100

. .\Add-VpnRoute.ps1 stress-2 iot-edge-sublib-003
#ssh -i azureKey.pem azureuser@20.127.148.26

. .\Add-VpnRoute.ps1 stress-3 iot-edge-sublib-003
#ssh -i azureKey.pem azureuser@20.127.144.89

. .\Add-VpnRoute.ps1 stress-4 iot-edge-sublib-003
#ssh -i azureKey.pem azureuser@20.127.145.182

. .\Add-VpnRoute.ps1 stress-5 iot-edge-sublib-003
#ssh -i azureKey.pem azureuser@20.127.146.229
```


## Installing K3S

[Install k3s](https://docs.k3s.io/quick-start)

```bash
mkdir ~/.kube 2> /dev/null
cd ~/.kube
touch config
sudo k3s kubectl config view --raw > config
chmod 600 config

export KUBECONFIG=~/.kube/config
```


