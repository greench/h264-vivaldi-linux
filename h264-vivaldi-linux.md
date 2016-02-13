# Vivaldi'de H.264 desteği nasıl kullanılır (alternatif FFMpeg kütüphanesiyle)
Bu yazı [Ruarí Ødegaard](https://twitter.com/ruari)'ın [How to use H.264 support In Vivaldi, via an alternative FFMpeg library](https://gist.github.com/ruario/bec42d156d30affef655#file-h264-vivaldi-linux-md) yazısının Türkçe tercümesidir. 

## Giriş
Bu hızlı rehber çeşitli Linux dağıtımlarında çalışmaktadır. Eğer Vivaldi'nin yanında Chrome yüklediyseniz, bu değişiklikleri yapınca Netflix'in çalışacağnı not olarak düşelim.

### Ubuntu
Aşağıdaki adımlar Vivaldi beta içindir. Eğer en son Vivaldi önizlemesini kullanıyorsanız, "Diğer dağıtımlar" bölümündeki [Vivaldi snapshot](#Vivaldi-snapshots) kısmında belirtilenleri yapın.

#### chromium-codecs-ffmpeg-extra paketini yükleyin

```
sudo apt-get update && sudo apt-get install chromium-codecs-ffmpeg-extra
```

Vivaldi'yi yeniden başlatmanız gerekecektir. Desteğin sağlandığını [bu sayfa](http://www.quirksmode.org/html5/tests/video.html)da görebilirsiniz.

### Arch
#### AUR deposundan yükleme
En son beta'yı kullanmak istiyorsanız,  [vivaldi-beta](https://aur.archlinux.org/packages/vivaldi-beta/) ve [vivaldi-ffmpeg-codecs](https://aur.archlinux.org/packages/vivaldi-ffmpeg-codecs/) paketlerini yükleyin.

En son beta'yı kullanmak istiyorsanız,  [vivaldi-snapshot](https://aur.archlinux.org/packages/vivaldi-snapshot/) ve [vivaldi-snapshot-ffmpeg-codecs](https://aur.archlinux.org/packages/vivaldi-snapshot-ffmpeg-codecs/) paketlerini yükleyin..

#### Resmi olmayan herecura deposundan kurulum
Resmi olmayan  [[herecura]](http://repo.herecura.eu/) deposundan aşağıdaki paketleri alabilirsiniz.

AUR'dan veya bağlantılı resmi olmayan depodan kurulum sonrası, Vivaldi'yi yeniden başlatmanız gerekmekte. Video desteğini [bu sayfadan](http://www.quirksmode.org/html5/tests/video.html) kontrol edebilirsiniz.

### Diğer dağıtımlar
Kütüphanenin uygun paketini sağlayamayan bir dağıtımınız varsa gerekli kütüphaneleri aşağıdaki çalıştırılabilir paketlerden birinden çıkartabilirsiniz.

#### Vivaldi beta
Aşağıdaki paketlerden birini indirin

##### 64bit Linux

```
wget http://security.ubuntu.com/ubuntu/pool/universe/c/chromium-browser/chromium-codecs-ffmpeg-extra_45.0.2454.101-0ubuntu1.1201_amd64.deb
```

##### 32bit Linux

```
wget http://security.ubuntu.com/ubuntu/pool/universe/c/chromium-browser/chromium-codecs-ffmpeg-extra_45.0.2454.101-0ubuntu1.1201_i386.deb
```

##### Paketten kütüphanenin çıkarılması

```
ar p chromium-codecs-ffmpeg-extra_45.0.2454*.deb data.tar.xz | tar xJf - ./usr/lib/chromium-browser/libs/libffmpeg.so --strip 5
```

**Not:** `ar` GNU binutils paketinde vardır.

#### Vivaldi snapshot
Aşağıdaki paketlerden birini indirin

##### 64bit Linux

```
wget http://repo.herecura.eu/herecura/x86_64/vivaldi-snapshot-ffmpeg-codecs-48.0.2564.88-1-x86_64.pkg.tar.xz
```

##### 32bit Linux

```
wget http://repo.herecura.eu/herecura/i686/vivaldi-snapshot-ffmpeg-codecs-48.0.2564.88-1-i686.pkg.tar.xz
```

##### Paketten kütüphanenin çıkarılması

```
tar xf vivaldi-snapshot-ffmpeg-codecs-48.0.2564*.pkg.tar.xz opt/vivaldi-snapshot/libffmpeg.so --strip 2
```

#### Vivaldi dizininde kurulum
Root olarak aşağıdakini çalıştırın (`sudo` ön kelimesi ile):

##### Vivaldi beta

```
install libffmpeg.so /opt/vivaldi-beta/libffmpeg.so
```

##### Vivaldi snapshot

```
install libffmpeg.so /opt/vivaldi-snapshot/libffmpeg.so
```

Vivaldi'yi yeniden başlatmanız gerekmekte. Video desteğini [bu sayfadan](http://www.quirksmode.org/html5/tests/video.html) kontrol edebilirsiniz.

## Kendi libffmpeg.so dosyanızı derlemek
Ubuntu tarafından sağlanan paketteki dosyayı kullanmak istemiyorsanız, bu dosyayı kendi kendinize derleyebilirsiniz.

### Bağımlılıkları yüklemek
#### Ubuntu, Debian & Mint

```
sudo apt-get update && sudo apt-get install automake build-essential libtool pkg-config yasm zlib1g-dev
```

#### Fedora

```
sudo dnf install autoconf automake gcc gcc-c++ libtool make nasm pkgconfig zlib-devel
```

#### openSUSE

```
zypper in -t pattern devel_basis
```

#### Diğer dağıtımlar
- autoconf
- automake
- gcc
- gcc-c++
- libtool
- make
- nasm (ya da yasm)
- pkg-config
- zlib geliştirici destek dosyaları

### Uygun Chromium kaynak arşini edinmek ve açmak
Vivaldi beta, terminale aşağıdakini girin:

```
CHRVER=45.0.2454.26
```

Vivaldi snapshot için, terminale aşağıdakini girin:

```
CHRVER=48.0.2564.88
```

Şimdi aşağıdaki komutları çalıştırın:

```
wget http://commondatastorage.googleapis.com/chromium-browser-official/chromium-$CHRVER.tar.xz
tar xf chromium-$CHRVER.tar.xz
cd chromium-$CHRVER
```

### Depo Araçlarını edinmek ve ayarlamak

```
git clone --depth 1 https://chromium.googlesource.com/chromium/tools/depot_tools.git
export PATH="$PATH:`pwd`/depot_tools"
```

### libffmpeg.so İnşası

```
touch chrome/test/data/webui/i18n_process_css_test.html
./build/gyp_chromium --depth . -Dcomponent=shared_library -Dffmpeg_branding=ChromeOS -Dclang=0
ninja -C out/Release ffmpeg
```

**Not:** PulseAudio kullanmayan (örn. Slackware) sistemlerde,  `-Duse_pulseaudio=0` tanımlanmalıdır.

### Vivaldi kurulumunuza kütüphaneyi kopyalamak
Duruma göre `sudo` ön komutuyla aşağıdaki komutları çalıştırın:

#### Vivaldi beta

```
install out/Release/lib/libffmpeg.so /opt/vivaldi-beta/libffmpeg.so
```

#### Vivaldi snapshot

```
install out/Release/lib/libffmpeg.so /opt/vivaldi-snapshot/libffmpeg.so
```

### Tarayıcıyı yeniden başlatma
Şimdi Vivaldi'yi yeniden başlatmanız gerekecektir.. Video desteğini [bu sayfadan](http://www.quirksmode.org/html5/tests/video.html) kontrol edebilirsiniz.

## Paket bakıcıları için ipuçları
Eğer Vivaldi tarayıcısını dağıtımınız için tekrar paketliyorsanız, ffmpeg kütüphanesini içeren **vivaldi-beta-ffmpeg-codecs** ya da **vivaldi-snapshot-ffmpeg-codecs** paketlerini göz önünde bulundurmayı düşünebilirsiniz. Bu kütüphaneyi sırasıyla `/opt/vivaldi-beta/` ya da `/opt/vivaldi-snapshot/` dizinlerine yerleştirmeniz önerilmektedir.
