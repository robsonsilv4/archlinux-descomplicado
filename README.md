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

- reflector...
- particionamento...
- formatação...
- instalação da base...
