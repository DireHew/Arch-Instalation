# Guia de instalacao do Arch Linux

## . configurando o relogio do sistema
```bash
timedatectl set-ntp true
```
* sincroniza o relogio com a internet.

## . listando os discos
```bash
lsblk
```
* mostra os discos e particoes disponiveis no pc.

## . criando particoes
```bash
cfdisk /dev/sda
```
* abre a ferramenta de particionamento. voce precisa criar as seguintes particoes:
  - sda1: particao fat32 (para boot efi)
  - sda2: particao ext4 (para sistema raiz /)
  - sda3: particao ext4 (para /home)
  - sda4: swap (memoria virtual)

## . formatando as particoes
```bash
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
mkswap /dev/sda4
```
* formata cada particao no formato correto:
  - fat32 para o boot.
  - ext4 para sistema e home.
  - swap para memoria virtual.

## . montando as particoes
```bash
mount /dev/sda2 /mnt
mkdir /mnt/home/
mount /dev/sda3 /mnt/home/
swapon /dev/sda4
```
* monta as particoes para preparar a instalacao:
  - `/mnt` e a raiz do sistema.
  - `/mnt/home` sera a pasta do usuario.
  - `swapon` ativa a particao swap.

## . instalando o sistema base
```bash
pacstrap /mnt base linux linux-firmware nano vim
```
* instala o sistema basico, kernel linux, firmware e editores nano e vim.

## . gerando o fstab
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
* cria o arquivo fstab, que define como as particoes serao montadas no boot.

## . entrando no sistema
```bash
arch-chroot /mnt
```
* entra no ambiente do novo sistema instalado.

## . configurando fuso horario
```bash
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
hwclock --systohc
```
* ajusta o fuso horario para sao paulo e sincroniza o relogio.

## . configurando o idioma
```bash
vim /etc/locale.gen
```
* descomente a linha `en_US.UTF-8 UTF-8`.
```bash
locale-gen
```
* gera os idiomas configurados.
```bash
vim /etc/locale.conf
```
* adicione a linha:
```plaintext
LANG=en_US.UTF-8
```

## . configurando o hostname
```bash
vim /etc/hostname
```
* insira o nome do seu pc (ex: `meupc`).

```bash
vim /etc/hosts
```
* adicione:
```plaintext
127.0.0.1    localhost
::1          localhost
127.0.1.1    NOMEDOPC.localdomain NOMEDOPC
```

## . gerando a imagem do kernel
```bash
mkinitcpio -P
```
* cria a imagem inicial do kernel.

## . instalando pacotes essenciais
```bash
pacman -S grub base-devel efibootmgr os-prober mtools dosfstools linux-headers networkmanager nm-connection-editor pulseaudio pavucontrol dialog
```
* instala grub (bootloader) e pacotes necessarios para o sistema.

## . configurando o bootloader grub
```bash
mkdir /boot/EFI
mount /dev/sda1 /boot/EFI
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```
* instala o grub e cria o arquivo de configuracao.

## . ativando o networkmanager
```bash
systemctl enable NetworkManager
```
* ativa o gerenciador de rede.

## . criando usuario e configurando sudo
```bash
useradd -m -G wheel NOMEDOUSUARIO
EDITOR=nano visudo
```
* descomente a linha `%wheel ALL=(ALL) ALL` para dar permissao de sudo.
```bash
passwd root
passwd NOMEDOUSUARIO
```
* define senhas para root e usuario.

## . instalando interface grafica
```bash
pacman -S xorg lightdm lightdm-gtk-greeter
systemctl enable lightdm
```
* instala o servidor grafico xorg e o gerenciador de login lightdm.

### drivers de video:
* amd:
```bash
pacman -S xf86-video-amdgpu
```
* nvidia:
```bash
pacman -S nvidia nvidia-utils
```

## . escolhendo o ambiente grafico
### gnome:
```bash
pacman -S gnome
```
### xfce:
```bash
pacman -S xfce4
```
### minimalista (bspwm):
```bash
pacman -S bspwm sxhkd polybar picom dunst nitrogen dmenu
```

---

pronto! a partir daqui voce pode reiniciar o pc.
