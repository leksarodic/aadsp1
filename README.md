```text
Univerzitet u Novom Sadu
Fakultet tehničkih nauka
Računarska tehnika i računarske komunikacije
```

## Arhitekture i algoritmi DSP 1

### Dan01, vezba 1

Ako radimo `mv *.txt` bitno je proveriti skrivene fajlove sa istom ekstenzijom

`more +/"hope" myfile.txt`

Display the contents of file myfile.txt, beginning at the first line containing the string `hope`.

`more` za razliku od `less` oboji pronadjeni pattern/red

```bash
diff
%<  # lines from FILE1
%>  # lines from FILE2
%=  # lines common to FILE1 and FILE2
```

```bash
head -n 10 file
# Display the first 10 lines of a file
```

```bash
tail -n 10 file
# Display the last 10 lines of a file
```

```bash
chmod u+r file
# u za user
# r za read
```

```bash
mkdir -p folder/child
```

https://www.gnu.org/software/bash/manual/html_node/Directory-Stack-Builtins.html#Directory-Stack-Builtins 

```bash
grep -R "macka"
# U svim fajlovima trazimo "macka". To podrazumeva i binarne fajlove.
```

```bash
find . -name "*.txt" | xargs grep "macka"
# "macka" trazimo samo u .txt fajlovima
```

### Dan02, vezba 2

```bash
ps
# report a snapshot of the current processes.

kill PID
```

```bash
sed -e  `s/abc/def/` testdat
# menja svaku sekvencu karaktera abc sa def u datoteci testdat
```

```bash
sed -i.bak -e 's/LOG/ALOG/' sed1.txt
# U fajlu sed1.txt menjamo LOG sa ALOG sa pocetka fajla, pravimo bakcup starog sed1.txt.bak i menjamo sed1.txt
```

```bash
tar jcvf archive.tar.bz2 dir
# Kreiramo arhivu
```

```bash
tar jxvf vezba1.tar.bz2 -C ~/studenti/ra218-2015/vezbe/Dan02/extract_tmp/ vezba2/sed1.txt vezba2/sed2.txt
# Ekstaktujemo vezba2/sed1.txt i vezba2/sed2.txt zajedno sa folderima u extract_tmp
```

```bash
sudo adduser rtrk-user2
# Dodajemo novog korisnika koji se zove rtrk-user2. Prilikom kreiranja pita nas da postavimo lozinku za nalog.
```

```bash
ssh-keygen
# Kreiramo kljuc
```

```bash
ssh-copy-id username@remote_host
# Saljemo kljuc ka serveru, pitace nas za password od naloga.
```

```bash
ssh username@remote_host -p port
# Povezujemo se na server, unosimo lozinku za kljuc
```

Ctrl + Alt + F1-5 menjamo virtuelnu konzolu

#### Vim

Vraćanje sa `ESC <Normal mode>`

```bash
ESC
:q!
# Quit and don't save
```

```text
gore "k"
dole "j"
levo "h"
desno "l"
```

brisanje obelezenog karaktera `x`

Pozicionirati se na mesto iza gde treba dodati tekst, umetanje teksta `i`

dodavanje teksta na kraj reda `A` (veliko A)

### Dan03, vezba 3
Makefile

### Dan04, vezba 4

```bash
picocom -b 115200 /dev/ttyUSB0
```

```bash
setenv ipaddr <IP adresa rpi>
setenv serverip <IP adresa pc>
```

```bash
printenv ethaddr
setenv -f ethaddr <MAC adresa rpi>
```

```bash
tftp 0x01000000 textfile.txt
# Iz UBoota mozemo da dobavimo datoteku preko komande iznad i ona mora da se nalazi na pc na /var/lib/tftpboot
```

```bash
md 0x01000000
Iz Uboota vrednost adrese citamo sa
```

```bash
setenv bootargs "root=/dev/nfs rw ip=<rpi ip> console=ttyAMA0,115200 nfsroot=<pc ip>:/home/rtrk/linux-kernel-labs/modules/nfsroot"
```

```bash
editenv bootargs
# Za promenu env promenjive
```

```bash
bootz 0x01000000 - 0x02000000
Сада можете покренути језгро
```

Ако немате идеју, погледајте и NFS server поруке у `/var/log/syslog`.

### Dan05, vezba 4

```bash
# Pozicioniramo se na trazeno mesto gde je potrebno primeniti patch
patch -p1 < folder/ime/patcha
```

```bash
# Mozemo dodati export u skriptu. Pokrecemo sa source ime_skripte.sh
export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabihf-
```

```bash
make distclean
```

### Dan06, vezba 4

Kada unutar kernel configurationa biramo opcije potrebno je videti, kako bi upalili neku od opcija, da li postoje neke zavisnosti.

Na primer za Ext4 i vfat je potrebno cekirati BLOCK flag.

Rasiriti meni, search config.

Postupak prevodjenja i podesavanja kernela:

1. Preuzmemo kod kernela, na primer pomocu git clone i kloniramo sa maksimalnom dubinom “istorije” komitova 1 ako bi zauzeli sto manje mesta, ako nam istorija i povratak/vracanje kroz kod nisu potrebni
```bash
git clone -b rpi-4.4.y --single-branch https://github.com/raspberrypi/linux --depth=1
```

2. Ukoliko je potrebno primenimo zakrpe/patcheve, pozicioniramo se u folder gde je potrebno da “izvrsimo” patchovanje pa 

```bash
patch -p1 < putanja/do/patcha
```

3. Podesimo cross-compiler

```bash
export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabihf-
```

Ili napravimo skriptu gde nabrojimo export-e i pozovemo je sa source skripta.sh

4. Dobavimo konfiguracioni fajl za kernel. Default configuracioni fajl se nalazi na putanji `linux/arch/PLATFORMA/config` i smestimo ga u linux/src pod imenom `.config`

5. Pokrenemo `make xconfig` ukoliko hocemo jos neki od parametara da podesimo, kada zavrsimo sacuvamo

6. Nakon toga pokrecemo prevodjenje kernela sa `make -j4` i ukoliko smo sve dobro uradili, nas kernel bi trebao da se nalazi na putanji `linux/arch/PLATFORMA/boot` pod imenom `zImage`

7. Zatim je potrebno pozvati komandu kako bi dobili module u fajlsistemu

```bash
make modules_install INSTALL_MOD_PATH=<putanja do nfsroot direktorijuma>
```

### Dan 06, vezba 5

Kada buildujemo kernel, konfiguracioni dokument koji se ucitava pozivom make xconfig se nalazi na `linux-kernel-labs/src/linux` i zove se `.config`

Ako imamo butovan "prazan" kernel, a potrebno na je da pristupimo sd kartici, potrebno je prvo da napravimo folder gde cemo mountovati karticu, na primer `/mnt/sdcard`, zatim moutujemo particiju mount `/dev/mmcblk0p2` /mnt/sdcard i na lokaciji `/mnt/sdcard` kopiramo `zImage`.

```bash
setenv bootcmd run mmc_boot
```

Kada hocemo da budovanje sistem bude izvrseno sa SD kartice, koristimo

```bash
setenv bootcmd "tftp 0x01000000 zImage; tftp 0x02000000 bcm2709-rpi-2-b.dtb; bootz 0x01000000 - 0x02000000" 
# za podizanje sa servera
```

```bash
sudo /etc/init.d/nfs-kernel-server restart
# Nakon izmene /etc/exports fajla gde smestamo informaciju na pcu gde se nalazi putanja do nfs root fajl sistema, potrebno je restartovati servis i izmeniti bootargs u Ubootu.
```

```bash
mknod dev/ttyAMA0 c 204 64
mknod dev/console c 5 1
# Karakterni uredjaj, major 204, minor 64
```

Pri primeni IP adrese PCa potrebno je izmeniti `serverip` i `bootargs` u Ubootu. Sa `saveenv` cuvamo izmene za naredne rebootove.

Boot argumente podesavamo kroz Uboot sa `bootargs`

```bash
cat /proc/cmdline
# Citamo bootargs
```

```bash
mknod dev/tty0 c 4 0
mknod dev/tty1 c 4 1
mknod dev/tty2 c 4 2
# Kako bi napravili framebuffer da radi.
```

### Dan07, vezba 6

Kada kopajliramo busybox potrebno je da koristimo tekstualno okruzenje za podesavanja. Potrebno je instalirati `libncurses-dev` i koristiti `make menuconfig`.

1. `make menuconfig`

2. Busybox Settings > Build Options > Build Busybox as a static binary (no shared libs)  -  Enable this option by pressing "Y"
ako nam je potrebno

3. Busybox Settings > Installation Options > Folder

4. `make -j4`

5. `make install`

Nakon preuzimanja busybox-a, potrebno je dodati foldere:

* etc/init.d
* proc
* sys

Unutar `/etc/init.d/` napraviti fajl `rcS` sa:

```bash
mount -t proc proc proc/
mount -t sysfs sys sys/
```

Unutar `/etc/` napraviti fajl `inittab` sa:

```bash
::sysinit:/etc/init.d/rcS
ttyAMA0::askfirst:/bin/sh
```

Prevodjenje programa na PCu za RPi:

1. Kreirati `Makefile`

```make
CC=arm-linux-gnueabihf-gcc
```

2. Prevesti program sa `make`

Lokacije biblioteka: `/usr/arm-linux-gnueabihf/lib/`

Kako bi otkrili koje biblioteke program poziva, mozemo koristiti 

```bash
readelf -d ime_programa | grep "NEEDED"
```

Potrebne biblioteke da bi sistem pokrenuo C program:

* `ld-2.19.so`
* `libc-2.19.so`
* `ld-linux-armhf.so.3` veza na `ld-2.19.so`
* `libc.so.6` veza na `libc-2.19.so`

Kako bi pokrenuli sistem sa dinamickim povezivanjem za busybox, potrebno je dodati i:

* `libm.so.6`
* `libm-2.19.so`

Pravljenje initramfs-a:

1. Promeniti ime `linuxrc` u `init`

2. Izbrisati fajl `.ash_history` iz FSa

3. src/linux > make xconfig 

4. INITRAMFS_SOURCE podesiti putanju do `/home/rtrk/linux-kernel-labs/src/busybox-1.21.1/_install/`

5. `make -j4`

### Dan08, vezba 7

Ucitavanje modula `insmod ime_fajla.ko`
Brisanje modula iz sistema `rmmod ime_fajla`
Ispis ucitanih modula `cat /proc/modules`

Za ispis kernel loga koristiti `dmesg`

Za ispis verzije kernela u modulu koristiti `#include <linux/utsname.h>` i funkciju `utsname()->version`.

https://stackoverflow.com/a/27339673

Provera major broja `cat /proc/devices`.

Za upotrebu `kmalloc`-a includovati `#include <linux/slab.h>`.

### Dan09, vezba 10

Povezivanje nunchuka sa RPijem:

* GND пин на J8 пин 9 (GND)
* 3V3 пин на J8 пин 1 (3V3)
* SCL пин на J8 пин 5 (GPIO3 / SCL1 I2C)
* SDA пин на J8 пин 3 (GPIO2 / SDA1 I2C)

Join i2c bus with address `0x52`

Generisanje DTB fajla:

1. Na `arch/arm/boot/dts/` dodamo fajl `bcm2709-rpi-2-b-custom-IME.dts`.

2. Izmenimo `arch/arm/boot/dts/Makefile` i pronadjemo liniju `dtb-$(CONFIG_ARCH_BCM2709) += bcm2709-rpi-2-b.dtb` i promenimo je u `dtb-$(CONFIG_ARCH_BCM2709) += bcm2709-rpi-2-b-custom-IME.dtb`

3. Uradimo `make dtbs`. Dobijeni `dts` fajl dobijamo u `arch/arm/boot/dts/`

4. Prenesemo fajl `bcm2709-rpi-2-b-custom-IME.dts` na `/var/lib/tftpboot`

5. Izmenimo `bootcmd` env kroz Uboot.

6. Proverimo da li imamo zakacen nunchuk 

```bash
find /proc/device-tree/ -name "*nunchuk*"
```

Kroz `dtc -I fs /proc/device-tree/` mozemo da vidimo celu Device Tree strukturu.

https://elinux.org/Device_Tree_Usage 

Implementacija osnovnog I2C rukovaoca:

```bash
sudo chown -R rtrk.rtrk ~/linux-kernel-labs/modules/nfsroot/lib/

make modules_install INSTALL_MOD_PATH=~/linux-kernel-labs/modules/nfsroot

sudo chown -R root.root ~/linux-kernel-labs/modules/nfsroot/lib/
```

https://elinux.org/Main_Page

### Dan10, vezba 11

Primer `bcm2709-rpi-2-b-custom.dts`

```
/dts-v1/;

// arch/arm/boot/dts/

#include "bcm2709.dtsi"

/ {
    compatible = "brcm,bcm2709";
    model = "Raspberry Pi 2 Model B";
};

&uart0 {
    status = "okay";
};

&gpio {
    i2c1_pins: i2c1 {
   	 brcm,pins = <2 3>; /* GPIO2 i GPIO3 */
   	 brcm,function = <4>; /* alt0 */
    };
};

&i2c1 {
    status = "okay";
    clock_frequency = <100000>;
    pinctrl-names = "default";
    pinctrl-0 = <&i2c1_pins>;

    nunchuk: nunchuk@52 {
	 mode = <0x1>;
   	 reg = <0x52>;
   	 compatible = "nintendo, nunchuk";
    };
};

/ {
    __overrides__ {
   	 uart0 = <&uart0>,"status";
   	 uart0_clkrate = <&clk_uart0>,"clock-frequency:0";
   	 i2c1 = <&i2c1>,"status";
   	 i2c1_baudrate = <&i2c1>,"clock-frequency:0";
   	 core_freq = <&clk_core>,"clock-frequency:0";
    };
};
```

Testiranje I2C:

```bash
modprobe i2c_dev
i2cdetect -l
```

Уверите се да све ради како је очекивано, покрените `i2cdetect 1`

Ово би требало да скенира све уређје на `i2c1` магистрали. Требало би да видите уређај на адреси `0x52`

То је ваш nunchuk

Testiranje pritisnutosti tastera preko `evtest /dev/input/event0`

Putanja do struct device_node, `nunchuk->i2c_client->dev.of_node`
