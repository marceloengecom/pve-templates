# pve-templates
Comandos para criação de templates Cloud-Init para o Proxmox VE

**1. DOWNLOAD DA IMAGEM DO UBUNTU 22.04 NO HYPERVISOR PROXMOX**
cd /tmp
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img

**2. INSTALAÇÃO DOS PACOTES NECESSÁRIOS, CASO AINDA NÂO OS TENHA INSTALADO NO PROXOMOX**
sudo apt update -y
sudo apt install libguestfs-tools -y

**3. INSTALAR O QEMU-GUEST-AGENT NA IMAGEM RECÉM BAIXADA**
virt-customize -a jammy-server-cloudimg-amd64.img --install qemu-guest-agent


**4. CRIAR UMA MÁQUINA VIRTUAL PROXMOX A PARTIR DA IMAGEM RECÉM MODIFICADA - **Conferir os parâmetros utilizados (negrito)**

qm create **9001** --name **Ubuntu22.04-CloudInit** --ostype **l26** --cores **2** --sockets **1** --memory **2048**  --net0 virtio,bridge=**vmbr1**
qm importdisk **9001** /tmp/jammy-server-cloudimg-amd64.img local-lvm

qm set **9001** --scsihw **virtio-scsi-pci** --virtio0 **local-lvm:vm-9001-disk-0**
qm set **9001** --ide2 **local-lvm:cloudinit**
qm set **9001** --boot c --bootdisk **virtio0**
qm set **9001** --serial0 socket --vga serial0
qm set **9001** --agent **enabled=1**
qm disk resize **9001** **virtio0 +20G**
qm template **9001**

rm /tmp/jammy-server-cloudimg-amd64.img



