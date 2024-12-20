#!/bin/bash

# Update package list
apt-get update

# Download Windows files
echo "Downloading Windows files..."
wget -O w7x64.img https://bit.ly/akuhnetw7X64

# Download ngrok
echo "Downloading ngrok..."
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz > /dev/null 2>&1
tar -xvf ngrok-v3-stable-linux-amd64.tgz > /dev/null 2>&1

# Set ngrok authentication token
read -p "Ctrl + V Authtoken: " CRP

# Start ngrok tunnel
echo "Starting ngrok tunnel..."
./ngrok authtoken $CRP
nohup ./ngrok tcp 3388 &>/dev/null &

# Wait for ngrok tunnel to establish
sleep 10

# Fetch ngrok public URL
echo "Fetching ngrok public URL..."
NGROK_URL=$(curl --silent http://127.0.0.1:4040/api/tunnels | jq -r '.tunnels[] | select(.proto=="tcp") | .public_url')

# Check if URL was fetched successfully
if [ -z "$NGROK_URL" ]; then
    echo "Failed to fetch ngrok URL. Please check ngrok setup."
    exit 1
fi

# Install qemu-system-x86
echo "Installing qemu-system-x86..."
apt-get install qemu-system-x86 -y

# Start Windows
echo "Starting Windows..."
qemu-system-x86_64 -hda w7x64.img -m 4G -smp cores=4 -net user,hostfwd=tcp::3388-:3389 -net nic -object rng-random,id=rng0,filename=/dev/urandom -device virtio-rng-pci,rng=rng0 -vga vmware -nographic &>/dev/null &

# Display RDP address
echo "RDP Address:"
echo $NGROK_URL

echo "===================================="
echo "Username: akuh"
echo "Password: Akuh.Net"
echo "===================================="
echo "===================================="
echo "Keep supporting akuh.net, thank you"
echo "You Got Free RDP now"
echo "Wait 2 minute to finish bot"
echo "You can close this tab"
echo "RDP runs for 50 hours"
echo "===================================="

# Wait for 50 hours
sleep 432000
