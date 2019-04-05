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

```sh
mkfs.vfat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
```

```sh
mount /dev/sda2 /mnt
mkdir /mnt/{home,boot}
mount /dev/sda2 /mnt
mount /dev/sda2 /mnt
```

```sh
pacstrap /mnt base base-devel
```
