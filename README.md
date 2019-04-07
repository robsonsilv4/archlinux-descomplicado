# Arch Linux Descomplicado

Este repositório tem o intuito de te guiar passo a paso na instalação do Arch Linux, sem que você precise se preocupar com os detalhes.

Também será disponibilizado um artigo detalhando todo o passo a passo da intação, para que possa compreender melhor e o que está acontecendo e aprender um pouco mais sobre Linux. Assim que concluído, o artigo estará disponível no [Medium](todo).

## Download

A ISO do Arch Linux é bem leve, cerca de 600 MB e pode ser baixada através de qualquer um dos espelhos disponíveis, como via torrent, atraves do [site oficial](https://www.archlinux.org/download/).

Para criar um pendrive bootável com a mesma, basta utilizar o seguinte comando:

```sh
sudo dd if=local/archlinux.iso of=/dev/sdx
# onde x é o seu dispositivo usb
```

## Preparação da Base

Ao concluir o boot da live do Arch Linux, mude o layout do teclado para pt-br:

```sh
loadkeys br-abnt2
```

Se tiver utilizando internet cabeada, a conexão será reconhecida automaticamente, mas caso queira utilizar uma conexão wi-fi, utilize o seguinte comando e escolha a sua rede:

```sh
wifi-menu
```

Para testar a conexão, utilize o seguinte comando:

```sh
ping -t 5 google.com
```

A midia de instalação é atualizada todo incicio de mês, mas mesmo que esteja utilizando a atual é bom sincronizar e rankiar os mirrors para tirar todo o proveito da sua conexão.

Primeiro instale o reflector:
```sh
pacman -Sy reflector
```

E então sincronize os mirrors utilizando os seguinte comando:
```sh
reflector --latest 200 --protocol http --protocol https --sort rate --save /etc/pacman.d/mirrorlist
# Adicone --verbose caso queira ver o progresso
```

Para o particionamento iremos utilizar o *cfdisk*, mas antes liste os discos disponíveis:

```
lsblk
```

Este é um exemplo de saída do comandos dos meus discos:
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 223,6G  0 disk 
├─sda1   8:1    0   100M  0 part /boot
├─sda2   8:2    0    50G  0 part /
└─sda3   8:3    0 173,5G  0 part /home
```

Iremos criar as partições no */dev/sda* que geralmente é o HD ou SSD principal:

```
cfdisk /dev/sda
```

Se não tiver nenhuma partição no disco, será pedido para escolher um label type, então escolha gpt.

Após isso, em new, crie as seguintes partições:
- A primeira com 250M do tipo EFI System
- A segunda com 20G, 40G ou 50G do tipo Linux filesystem
- E a última com o resto do espaço disponível, também do tipo Linux filesystem.

Agora basta escrever as mudanças selecionando *Write* e depois digitando *yes*.

Antes de montarmos as partições, precisamos formatá-las, para isso, execute os seguintes comandos:

```sh
# Lembre-se de alterar os números das partições!
mkfs.vfat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
```

Após as partições terem sido formatas, iremos montá-las:

```sh
# Primeiro a partição raíz
mount /dev/sda2 /mnt

# Crie os diretórios de boot e home
mkdir /mnt/{boot,home}

# E então os monte
mount /dev/sda1 /mnt/boot
mount /dev/sda3 /mnt/home
```

Verifique as partições montadas novamente com o *lsblk* e se estiver tudo correto, instale a base do sistema com o comando:

```sh
pacstrap /mnt base base-devel
```

Após concluír a instalação das bases, o seu sistema estará praticmente pronto, mas antes de entrarmos nele para fazer algumas configurações necessárias, precisamos gerar o *fstab* para que o Linux lembre de quais partições precisam ser montadas, para isso, utilize o seguinte comando:

```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

Agora utilize o *arch-chroot* para entrar de fato no ambiento do novo sistema:

```sh
arch-chroot /mnt
```

Agora que estamos no novo sistema, precisamos fazer algumas configurações básicas.

Primeiro, utilize o seguinte comando definir quais idiomas estarão disponíveis no sistema:

```sh
nano /etc/locale.gen
```

Então descomente os idiomas de sua escolha, como por exemplo:
```sh
#...
en_US UTF-8
pt_BR UTF-8
#...
```

```
locale-gen
```

```
echo "LANG=pt_BR.UTF-8" >> /etc/locale.conf
```

```
echo "KEYMAP=br-abnt2" >> /etc/vconsole.conf
```

```
ln -sf /usr/share/zoneinfo/America/Fortaleza /etc/localtime

hwclock --systohc
```

GRUB...

```
pacman -S grub efibootmgr
```

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id="Arch Linux"

grub-mkconfig -o /boot/grub/grub.cfg
```

Pacotes base...

```
pacman -S tlp networkmanager xorg-server xorg-xinit ttf-dejavu ttf-liberation xdg-user-dirs
```

Pacotes do KDE...

```
plasma sddm sddm-kcm packagekit-qt5 plasma-nm konsole yakuake dolphin kdegraphics-thumbnailers ffmpegthumbs ark p7zip unrar kate gwenview spectacle vlc okular kcalc qt5-translations aspell-pt
```

```
partitionmanager filelight
```

```
firefox firefox-i8n-pt-br qbittorrent
```

Descrição dos pacotes...
