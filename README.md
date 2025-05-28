[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Eu-CByJh)
|    NRP     |      Name      |
| :--------: | :------------: |
| 5025221102 | Marco Marcello Hugo |
| 5025241015 | Farrel Aqilla Novianto |

# Praktikum Modul 3 _(Module 3 Lab Work)_

### Laporan Resmi Praktikum Modul 3 _(Module 3 Lab Work Report)_

Di suatu pagi hari yang cerah, Budiman salah satu mahasiswa Informatika ditugaskan oleh dosennya untuk membuat suatu sistem operasi sederhana. Akan tetapi karena Budiman memiliki keterbatasan, Ia meminta tolong kepadamu untuk membantunya dalam mengerjakan tugasnya. Bantulah Budiman untuk membuat sistem operasi sederhana!

_One sunny morning, Budiman, an Informatics student, was assigned by his lecturer to create a simple operating system. However, due to Budiman's limitations, he asks for your help to assist him in completing his assignment. Help Budiman create a simple operating system!_

### Soal 1

> Sebelum membuat sistem operasi, Budiman diberitahu dosennya bahwa Ia harus melakukan beberapa tahap terlebih dahulu. Tahap-tahapan yang dimaksud adalah untuk **mempersiapkan seluruh prasyarat** dan **melakukan instalasi-instalasi** sebelum membuat sistem operasi. Lakukan seluruh tahapan prasyarat hingga [perintah ini](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul3/README-ID.md#:~:text=sudo%20apt%20install%20%2Dy%20busybox%2Dstatic) pada modul!

> _Before creating the OS, Budiman was informed by his lecturer that he must complete several steps first. The steps include **preparing all prerequisites** and **installing** before creating the OS. Complete all the prerequisite steps up to [this command](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul3/README-ID.md#:~:text=sudo%20apt%20install%20%2Dy%20busybox%2Dstatic) in the module!_

**Answer:**

- **Code:**

```
# Update dan Install Software Pendukung seperti qemu, build-essential, bison, flex, dan lainnya #
sudo apt -y update
sudo apt -y install qemu-system build-essential bison flex libelf-dev libssl-dev bc grub-common grub-pc libncurses-dev libssl-dev mtools grub-pc-bin xorriso tmux

# Siapkan Direktori #
mkdir -p osboot
cd osboot

# Download dan Ekstrak Kernel Linux versi 6.1.1 #
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.1.tar.xz
tar -xvf linux-6.1.1.tar.xz
cd linux-6.1.1

# Konfigurasi Kernel #
make tinyconfig
make menuconfig

# Kompilasi Kernel #
make -j$(nproc)

# Hasilkan File bzImage #
cp arch/x86/boot/bzImage ..

# Install BusyBox #
sudo apt install -y busybox-static
```

- **Explanation:**

Langkah pertama adalah memastikan sistem dalam keadaan terbaru dan semua perangkat lunak yang diperlukan telah terinstal. Perangkat lunak yang dibutuhkan meliputi compiler, pustaka pengembangan, serta emulator QEMU. Ini termasuk `build-essential, qemu, bison, flex, dan pustaka-pustaka tambahan` untuk kompilasi kernel. Selanjutnya, buat direktori khusus sebagai tempat OS dibuat. Semua proses, mulai dari konfigurasi hingga kompilasi kernel, akan dilakukan di dalam direktori ini agar lebih terstruktur dan mudah dikelola. Unduh kode sumber `kernel Linux versi 6.1.1` dari situs resmi kernel.org. Setelah berhasil diunduh, ekstrak file tersebut ke dalam direktori baru yang akan digunakan sebagai tempat konfigurasi dan kompilasi kernel.

Sebelum dikompilasi, kernel harus dikonfigurasi terlebih dahulu. Mulailah dengan konfigurasi minimal menggunakan `tinyconfig` untuk menghasilkan kernel dengan fitur dasar. Kemudian, lanjutkan dengan `menuconfig` untuk mengaktifkan fitur-fitur tambahan yang dibutuhkan seperti dukungan untuk serial console, file system, perangkat virtualisasi, dan driver yang relevan untuk menjalankan sistem di QEMU. Setelah konfigurasi selesai, lakukan proses kompilasi kernel. Proses ini akan menghasilkan file kernel berbentuk `bzImage`, yang nantinya digunakan untuk melakukan boot sistem operasi melalui QEMU. Kompilasi dapat dilakukan menggunakan seluruh inti CPU agar lebih cepat. Setelah proses kompilasi selesai, file kernel yang dihasilkan (bzImage) akan berada di dalam direktori hasil kompilasi kernel. File ini perlu disalin ke direktori proyek utama sebagai persiapan untuk membuat sistem file root (initramfs) dan menjalankan kernel dengan QEMU.

Untuk mendukung berbagai perintah dasar selama proses booting, `BusyBox` digunakan sebagai solusi ringan yang menyediakan banyak utilitas sistem dalam satu paket. BusyBox akan digunakan dalam tahap pembuatan initramfs, sehingga sistem dapat melakukan fungsi dasar seperti mounting file system, login, dan menjalankan shell.

- **Screenshot:**

<img src="https://github.com/user-attachments/assets/a11c60a2-9cd2-46e9-84b1-a16954ca3a5c">

### Soal 2

> Setelah seluruh prasyarat siap, Budiman siap untuk membuat sistem operasinya. Dosen meminta untuk sistem operasi Budiman harus memiliki directory **bin, dev, proc, sys, tmp,** dan **sisop**. Lagi-lagi Budiman meminta bantuanmu. Bantulah Ia dalam membuat directory tersebut!

> _Once all prerequisites are ready, Budiman is ready to create his OS. The lecturer asks that the OS should contain the directories **bin, dev, proc, sys, tmp,** and **sisop**. Help Budiman create these directories!_

**Answer:**

- **Code:**

```
# Masuk ke Direktori osbbot #
cd osboot

# Masuk ke Mode Superuser #
sudo bash

# Buat Direktori untuk Initramfs #
mkdir -p budinux/{bin,dev,proc,sys,tmp,sisop,home/root,home/Budiman,home/guest,home/praktikan1,home/praktikan2}

# Salin File Device ke Direktori dev #
cp -a /dev/null budinux/dev
cp -a /dev/tty* budinux/dev
cp -a /dev/zero budinux/dev
cp -a /dev/console budinux/dev

# Salin BusyBox ke Direktori bin #
cp /usr/bin/busybox budinux/bin
cd budinux/bin
./busybox --install .

# Buat File init #
touch init
nano init
```
Isi file `init`:
``` bash
#!/bin/sh
/bin/mount -t proc none /proc
/bin/mount -t sysfs none /sys

while true
do
    /bin/getty -L tty1 115200 vt100
    sleep 1
done
```
```
# Berikan Izin Eksekusi pada File init #
chmod +x init

# Kompresi dan Buat File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
- **Explanation:**

Proses pembuatan root filesystem dimulai dengan memasuki direktori proyek `osboot`, yang menjadi tempat penyimpanan semua file penting dari sistem. Karena banyak tahapan memerlukan hak administratif, pengguna terlebih dahulu masuk ke mode superuser. Setelah itu, dibuat struktur direktori `budinux` yang menyerupai sistem file Linux standar, meliputi direktori seperti `bin`, `dev`, `proc`, `sys`, `tmp`, serta beberapa direktori `home` untuk pengguna simulasi seperti `root`, `Budiman`, `guest`, dan `praktikan`.

Langkah selanjutnya adalah menyalin file device penting ke dalam direktori `dev` di dalam `budinux`. File seperti `/dev/null`, `/dev/tty`, `/dev/zero`, dan `/dev/console` sangat diperlukan karena mendukung proses booting dan fungsi dasar sistem. Setelah itu, utilitas BusyBox—yang berperan sebagai kumpulan perintah Linux ringan dalam satu file—disalin ke direktori `bin`. BusyBox kemudian di-*install* agar semua perintah dasar seperti `sh`, `ls`, dan `mount` tersedia dalam sistem minimal ini.

Setelah lingkungan dasar siap, dibuat sebuah file bernama `init` yang akan dijalankan pertama kali saat kernel melakukan booting. File `init` ini berisi skrip shell untuk me-*mount* sistem file `proc` dan `sysfs`, yang penting bagi kernel dalam mengakses informasi sistem. Skrip ini juga menjalankan `getty` untuk membuka akses terminal melalui `tty1`, memungkinkan pengguna berinteraksi langsung dengan shell. Setelah skrip selesai, file `init` diberi izin eksekusi agar bisa dijalankan secara otomatis saat boot.

Sebagai langkah terakhir, seluruh isi direktori `budinux` dikemas menjadi file initramfs dalam format `cpio` dan dikompresi menggunakan `gzip`, menghasilkan file `budinux.gz`. File ini nantinya digunakan bersamaan dengan kernel `bzImage` saat melakukan boot sistem melalui QEMU.


- **Screenshot:**

<img src="https://github.com/user-attachments/assets/62267820-5ea2-48a4-94c2-29fb2185001d">
<img src="https://github.com/user-attachments/assets/81ea55e4-3e12-4d5e-abf0-fa4eeafee828">

### Soal 3

> Budiman lupa, Ia harus membuat sistem operasi ini dengan sistem **Multi User** sesuai permintaan Dosennya. Ia meminta kembali kepadamu untuk membantunya membuat beberapa user beserta directory tiap usernya dibawah directory `home`. Buat pula password tiap user-usernya dan aplikasikan dalam sistem operasi tersebut!

> _Budiman forgot that he needs to create a **Multi User** system as requested by the lecturer. He asks your help again to create several users and their corresponding home directories under the `home` directory. Also set each user's password and apply them in the OS!_

**Format:** `user:pass`

```
root:Iniroot
Budiman:PassBudi
guest:guest
praktikan1:praktikan1
praktikan2:praktikan2
```

**Answer:**

- **Code:**

```
# Akses Direktori budinux #
cd osboot/budinux

# Buat Direktori Baru yaitu etc #
mkdir -p etc

# Generate Password Key untuk masing-masing User #
openssl passwd -1 Iniroot
openssl passwd -1 PassBudi
openssl passwd -1 guest
openssl passwd -1 praktikan1
openssl passwd -1 praktikan2

# Buat File passwd #
touch passwd
nano passwd
```
Isi file `passwd` dengan hasil generate password key per user:
```
root:$1$y.8jOPHe$RNiQor7zwxA34sGv8EHAP/:1000:1000:root:/home/root:/bin/sh
Budiman:$1$vUJG6tJn$0/kOCQBVDBmjA0viWanAc/:1001:1001:Budiman:/home/Budiman:/bin/sh
guest:$1$gW.4/OvU$avIc/AeD9Lw567nfVRiiJ.:1002:1002:guest:/home/guest:/bin/sh
praktikan1:$1$Ph1SZoLi$hFIjzjrbLSeQ6wszhPy.b1:1003:1003:praktikan1:/home/praktikan1:/bin/sh
praktikan2:$1$kIRW07Zh$Fs1ai4rkK.PfdsXI8gpKI.:1004:1004:praktikan2:/home/praktikan2:/bin/sh
```
```
# Buat File group #
touch group
nano group
```
Isi file `group` berisi konfigurasi user:
```
root:x:0:
bin:x:1:
sys:x:2:
tty:x:5:root,Budiman,guest,praktikan1,praktikan2
disk:x:6:
wheel:x:10:root,Budiman,guest,praktikan1,praktikan2
users:x:100:root,Budiman,guest,praktikan1,praktikan2
```
```
# Kompresi dan Buat Ulang File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
Cara menjalankan QEMU di `osboot`:
```
qemu-system-x86_64 \
-smp 2 \
-m 256 \
-display curses \
-vga std \
-kernel bzImage \
-initrd budinux.gz
```


- **Explanation:**

Untuk memungkinkan autentikasi pengguna atau multiuser pada sistem operasi minimal yang dibangun, langkah selanjutnya adalah menambahkan informasi akun pengguna dan grup ke dalam root filesystem `initramfs`. Proses ini dilakukan di dalam direktori `budinux` yang sebelumnya telah dibuat dan diatur sebagai sistem file dasar. Pertama, dibuat direktori `etc` di dalam `budinux`, yang berfungsi sebagai tempat penyimpanan konfigurasi sistem, termasuk file `passwd` dan `group`. Dua file ini bertugas untuk menyimpan informasi login pengguna dan keanggotaan grup dalam sistem.

Setiap pengguna yang akan dibuat memerlukan password terenkripsi. Oleh karena itu, password untuk masing-masing pengguna seperti `root`, `Budiman`, `guest`, `praktikan1`, dan `praktikan2` digenerate menggunakan perintah `openssl passwd -1`, yang menghasilkan password dalam format MD5 hash. Hasil dari proses ini kemudian dimasukkan ke dalam file `passwd`. File `passwd` memuat informasi setiap pengguna dalam format:

`username:password_hash:UID:GID:deskripsi:/home/direktori:/shell`

Setelah itu, file `group` dibuat untuk mengatur grup-grup dalam sistem. File ini mencantumkan nama grup, ID grup, dan pengguna-pengguna yang menjadi anggota. Grup penting seperti `tty`, `wheel`, dan `users` diberikan akses kepada semua pengguna yang dibuat, agar mereka memiliki izin menjalankan proses dasar dalam sistem. Dengan konfigurasi ini, sistem operasi minimal yang dijalankan di QEMU dapat mendukung login banyak pengguna, masing-masing dengan direktori home dan shell dasar yang telah ditentukan. Pengguna akan bisa masuk ke sistem dengan kredensial yang sudah disiapkan selama proses booting melalui terminal.

- **Screenshot:**

<img src="https://github.com/user-attachments/assets/b9365032-675e-41eb-86c2-c7e0044519e8">
<img src="https://github.com/user-attachments/assets/31a79bbf-f0a6-45bd-a7b1-f2b6f162c41e">

### Soal 4

> Dosen meminta Budiman membuat sistem operasi ini memilki **superuser** layaknya sistem operasi pada umumnya. User root yang sudah kamu buat sebelumnya akan digunakan sebagai superuser dalam sistem operasi milik Budiman. Superuser yang dimaksud adalah user dengan otoritas penuh yang dapat mengakses seluruhnya. Akan tetapi user lain tidak boleh memiliki otoritas yang sama. Dengan begitu user-user selain root tidak boleh mengakses `./root`. Buatlah sehingga tiap user selain superuser tidak dapat mengakses `./root`!

> _The lecturer requests that the OS must have a **superuser** just like other operating systems. The root user created earlier will serve as the superuser in Budiman's OS. The superuser should have full authority to access everything. However, other users should not have the same authority. Therefore, users other than root should not be able to access `./root`. Implement this so that non-superuser accounts cannot access `./root`!_

**Answer:**

- **Code:**

```
# Akses Direktori budinux #
cd osboot/budinux

# Ubah UID dan GID user root menjadi 0 (Superuser) serta konfigurasi user #
cd etc
nano passwd
```
Isi File `passwd` dengan UID dan GID 0 untuk `root`:
```
root:$1$y.8jOPHe$RNiQor7zwxA34sGv8EHAP/:0:0:root:/home/root:/bin/sh
Budiman:$1$vUJG6tJn$0/kOCQBVDBmjA0viWanAc/:1001:1001:Budiman:/home/Budiman:/bin/sh
guest:$1$gW.4/OvU$avIc/AeD9Lw567nfVRiiJ.:1002:1002:guest:/home/guest:/bin/sh
praktikan1:$1$Ph1SZoLi$hFIjzjrbLSeQ6wszhPy.b1:1003:1003:praktikan1:/home/praktikan1:/bin/sh
praktikan2:$1$kIRW07Zh$Fs1ai4rkK.PfdsXI8gpKI.:1004:1004:praktikan2:/home/praktikan2:/bin/sh
```
```
nano group
```
Isi file `group` dengan user `root` menjadi superuser:
```
root:x:0:
bin:x:1:root
sys:x:2:root
tty:x:5:root,Budiman,guest,praktikan1,praktikan2
disk:x:6:root
wheel:x:10:root,Budiman,guest,praktikan1,praktikan2
users:x:100:root,Budiman,guest,praktikan1,praktikan2
```
```
# Ubah kepemilikan dan izin folder /home/root hanya root yang bisa #
chown 0 /home/root
chmod 700 /home/root

# Kompresi dan Buat Ulang File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
Cara menjalankan QEMU di `osboot`:
```
qemu-system-x86_64 \
-smp 2 \
-m 256 \
-display curses \
-vga std \
-kernel bzImage \
-initrd budinux.gz
```

- **Explanation:**

Pada tahap ini, konfigurasi difokuskan untuk memastikan bahwa user `root` memiliki hak akses penuh sebagai superuser dalam sistem. Hal ini dicapai dengan mengatur UID (User ID) dan GID (Group ID) untuk user `root` menjadi `0` pada file `passwd`. Pengaturan ini diperlukan agar kernel mengenali user tersebut sebagai administrator utama yang memiliki kontrol penuh atas sistem operasi minimal.

Selanjutnya, file `group` dikonfigurasi ulang untuk memasukkan user `root` ke dalam grup-grup penting seperti `bin`, `sys`, `disk`, `tty`, `wheel`, dan `users`. Grup-grup tersebut memberikan akses terhadap berbagai fungsi sistem seperti manajemen perangkat keras, akses terminal, dan eksekusi perintah-perintah penting yang bersifat administratif.

Sebagai langkah keamanan tambahan, direktori `/home/root` disesuaikan agar hanya dapat diakses oleh user `root` saja. Ini dilakukan dengan mengubah kepemilikan direktori tersebut ke UID 0 serta menetapkan hak akses `700` agar hanya pemilik (root) yang dapat membaca, menulis, dan mengeksekusi isi direktori tersebut. Dengan konfigurasi ini, pengguna lain tidak akan dapat mengakses data atau konfigurasi yang bersifat sensitif milik superuser. Konfigurasi ini penting terutama ketika sistem akan digunakan oleh beberapa user dengan hak akses berbeda dalam lingkungan virtual seperti QEMU, guna menjamin isolasi dan keamanan data antar pengguna.


- **Screenshot:**

<img src="https://github.com/user-attachments/assets/d37d891c-b8ef-4bdc-a517-9fa085669cfd">
<img src="https://github.com/user-attachments/assets/df141878-9cfb-461d-8c5f-bbc969b1a03d">

### Soal 5

> Setiap user rencananya akan digunakan oleh satu orang tertentu. **Privasi dan otoritas tiap user** merupakan hal penting. Oleh karena itu, Budiman ingin membuat setiap user hanya bisa mengakses dirinya sendiri dan tidak bisa mengakses user lain. Buatlah sehingga sistem operasi Budiman dalam melakukan hal tersebut!

> _Each user is intended for an individual. **Privacy and authority** for each user are important. Therefore, Budiman wants to ensure that each user can only access their own files and not those of others. Implement this in Budiman's OS!_

**Answer:**

- **Code:**

```
# Akses Direktori budinux #
cd osboot/budinux

# Ubah kepemilikan dan izin folder /home/$USER supaya hanya bisa diakses oleh user itu sendiri (Lihat file passwd untuk melihat UID dan GID) #
chown 1000 /home/Budiman
chmod 700 /home/Budiman

chown 1001 /home/guest
chmod 700 /home/guest

chown 1002 /home/praktikan1
chmod 700 /home/praktikan1

chown 1003 /home/praktikan2
chmod 700 /home/praktikan2

# Kompresi dan Buat Ulang File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
Cara menjalankan QEMU di `osboot`:
```
qemu-system-x86_64 \
-smp 2 \
-m 256 \
-display curses \
-vga std \
-kernel bzImage \
-initrd budinux.gz
```

- **Explanation:**

Setiap direktori home diberikan kepemilikan berdasarkan UID (User ID) yang sesuai dengan entri pada file `passwd`. Misalnya, direktori `/home/Budiman` diatur agar dimiliki oleh user dengan UID 1000, dan begitu juga untuk user `guest`, `praktikan1`, dan `praktikan2` dengan UID berturut-turut 1001, 1002, dan 1003.

Setelah kepemilikan disesuaikan, setiap direktori home diberi hak akses `700`. Artinya, hanya pemilik direktori (user yang bersangkutan) yang memiliki hak untuk membaca, menulis, dan mengeksekusi file di dalam direktori tersebut. Pengguna lain, termasuk user biasa maupun non-root lainnya, tidak akan memiliki akses terhadap isi direktori tersebut. 

Pengaturan ini penting untuk menjaga privasi dan keamanan masing-masing pengguna di dalam sistem operasi minimal yang dibangun, serta untuk meniru perilaku sistem Unix/Linux pada umumnya dalam hal manajemen user dan proteksi data.

- **Screenshot:**

<img src="https://github.com/user-attachments/assets/2230ab86-b6c3-4ca4-9bdd-f8dd2b69992f">
<img src="https://github.com/user-attachments/assets/52f89eab-776b-4ad8-8dde-a6a38799fde5">

### Soal 6

> Dosen Budiman menginginkan sistem operasi yang **stylish**. Budiman memiliki ide untuk membuat sistem operasinya menjadi stylish. Ia meminta kamu untuk menambahkan tampilan sebuah banner yang ditampilkan setelah suatu user login ke dalam sistem operasi Budiman. Banner yang diinginkan Budiman adalah tulisan `"Welcome to OS'25"` dalam bentuk **ASCII Art**. Buatkanlah banner tersebut supaya Budiman senang! (Hint: gunakan text to ASCII Art Generator)

> _Budiman wants a **stylish** operating system. Budiman has an idea to make his OS stylish. He asks you to add a banner that appears after a user logs in. The banner should say `"Welcome to OS'25"` in **ASCII Art**. Use a text to ASCII Art generator to make Budiman happy!_ (Hint: use a text to ASCII Art generator)

**Answer:**

- **Code:**

```
# Akses Direktori budinux/etc #
cd osboot/budinux/etc

# Buat File motd #
touch motd
nano motd
```
Isi file `motd` dengan pesan sambutan:
```
Welcome to OS'25
```
```
# Kompresi dan Buat Ulang File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
Cara menjalankan QEMU di `osboot`:
```
qemu-system-x86_64 \
-smp 2 \
-m 256 \
-display curses \
-vga std \
-kernel bzImage \
-initrd budinux.gz
```

- **Explanation:**

Untuk memberikan pesan sambutan saat pengguna masuk ke sistem, dibuat sebuah file bernama `motd` (Message of the Day) di dalam direktori `etc` pada struktur filesystem `budinux`.

File `motd` ini akan ditampilkan secara otomatis kepada pengguna setiap kali berhasil login ke terminal. Pesan ini bersifat informatif dan sering digunakan untuk menampilkan informasi sistem, pengumuman, atau sekadar pesan sambutan. Dalam konteks soal ini, isi file `motd` disesuaikan dengan instruksi, yaitu menampilkan pesan sambutan **"Welcome to OS'25"**.

Di screenshot kedua terlihat messagenya hancur karena displaynya terlalu kecil.


- **Screenshot:**

<img src="https://github.com/user-attachments/assets/7fcceecc-a499-43fd-9d71-31644a955228">
<img src="https://github.com/user-attachments/assets/29786066-9aca-4a48-bc22-ccd4ce33b0aa">

### Soal 7

> Melihat perkembangan sistem operasi milik Budiman, Dosen kagum dengan adanya banner yang telah kamu buat sebelumnya. Kemudian Dosen juga menginginkan sistem operasi Budiman untuk dapat menampilkan **kata sambutan** dengan menyebut nama user yang login. Sambutan yang dimaksud berupa kalimat `"Helloo %USER"` dengan `%USER` merupakan nama user yang sedang menggunakan sistem operasi. Kalimat sambutan ini ditampilkan setelah user login dan setelah banner. Budiman kembali lagi meminta bantuanmu dalam menambahkan fitur ini.

> _Seeing the progress of Budiman's OS, the lecturer is impressed with the banner you created. The lecturer also wants the OS to display a **greeting message** that includes the name of the user who logs in. The greeting should say `"Helloo %USER"` where `%USER` is the name of the user currently using the OS. This greeting should be displayed after user login and after the banner. Budiman asks for your help again to add this feature._

**Answer:**

- **Code:**

```
# Akses Direktori budinux/etc #
cd osboot/budinux/etc

# Buat File motd #
touch profile
nano profile
```
Isi file `profile` dengan pesan sambutan:
``` bash
echo "Helloo $USER"
echo
```
```
# Kompresi dan Buat Ulang File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
Cara menjalankan QEMU di `osboot`:
```
qemu-system-x86_64 \
-smp 2 \
-m 256 \
-display curses \
-vga std \
-kernel bzImage \
-initrd budinux.gz
```

- **Explanation:**

Untuk menampilkan pesan sambutan secara dinamis saat pengguna masuk ke shell, digunakan file `profile`. File `profile` ini akan dijalankan secara otomatis setiap kali pengguna membuka sesi shell, dan digunakan untuk menampilkan pesan atau menjalankan perintah-perintah tertentu yang bersifat personalisasi.

Dalam implementasinya, file ini berisi perintah untuk mencetak pesan sambutan menggunakan nama pengguna yang sedang login. Pesan yang ditampilkan bersifat dinamis karena menggunakan variabel lingkungan `$USER`, sehingga akan menyesuaikan dengan identitas pengguna yang masuk ke sistem. Penambahan file ini bertujuan untuk memberi tahu user mana yang sedang login.

- **Screenshot:**

<img src="https://github.com/user-attachments/assets/7ba2833f-4f0f-494b-a09c-f821f02bd9cc">
<img src="https://github.com/user-attachments/assets/b6220667-028f-43af-90b0-93a280d25723">

### Soal 8

> Dosen Budiman sudah tua sekali, sehingga beliau memiliki kesulitan untuk melihat tampilan terminal default. Budiman menginisiatif untuk membuat tampilan sistem operasi menjadi seperti terminal milikmu. Modifikasilah sistem operasi Budiman menjadi menggunakan tampilan terminal kalian.

> _Budiman's lecturer is quite old and has difficulty seeing the default terminal display. Budiman takes the initiative to make the OS look like your terminal. Modify Budiman's OS to use your terminal appearance!_

**Answer:**

- **Code:**

```
# Akses Direktori linux-6.1.1 karena kita harus mengubah beberapa config #
make menuconfig
```
Checklist config dibawah ini:
```
Device Drivers -> Character devices -> Serial drivers -> 8250/16550 and compatible serial support
Device Drivers -> Character devices -> Serial drivers -> Console on 8250/16550 and compatible serial support
```
```
# Kompilasi Ulang Kernel #
make -j$(nproc)

# Hasilkan File bzImage #
cp arch/x86/boot/bzImage ..

# Akses Direktori budinux#
cd ../budinux

# Ubah File init #
nano init
```
Isi file `init` yang diperbarui dengan ttyS0:
``` bash
#!/bin/sh
/bin/mount -t proc none /proc
/bin/mount -t sysfs none /sys

while true
do
    /bin/getty -L ttyS0 115200 vt100
    sleep 1
done
```
```
# Kompresi dan Buat Ulang File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
Cara menjalankan QEMU di `osboot`:
```
qemu-system-x86_64 \
  -kernel bzImage \
  -initrd budinux.gz \
  -append "quiet console=ttyS0,115200" \
  -serial mon:stdio \
  -nographic
```

- **Explanation:**

Untuk memastikan budinux bisa dijalankan secara fullscreen di terminal, dilakukan konfigurasi ulang kernel Linux pada direktori `linux-6.1.1` menggunakan perintah `make menuconfig`. Pada konfigurasi ini, diaktifkan dua opsi penting di bawah kategori **Device Drivers → Character devices → Serial drivers**, yaitu:

- **8250/16550 and compatible serial support**
- **Console on 8250/16550 and compatible serial support**

Kedua opsi tersebut memungkinkan kernel menggunakan perangkat serial (seperti `ttyS0`) sebagai media komunikasi utama antara sistem dan pengguna. Setelah pengaturan konfigurasi selesai, kernel dikompilasi ulang menggunakan `make -j$(nproc)`. File hasil kompilasi berupa `bzImage` kemudian disalin ke direktori osboot.

Setelah itu, dilakukan modifikasi pada file `init` dalam direktori `budinux`. Dalam modifikasi ini, terminal yang digunakan diubah dari `tty1` menjadi `ttyS0`, yang merupakan serial port standar. Perubahan ini penting karena `ttyS0` merupakan jalur utama yang digunakan QEMU untuk menampilkan output sistem saat mode headless (`-nographic`). 

Sistem init yang telah diperbarui lalu dikompresi ulang menjadi file initramfs `budinux.gz` menggunakan format `cpio` dan `gzip`.

Untuk menjalankan sistem operasi minimal, digunakan perintah QEMU berikut:

```bash
qemu-system-x86_64 \
  -kernel bzImage \
  -initrd budinux.gz \
  -append "quiet console=ttyS0,115200" \
  -serial mon:stdio \
  -nographic
```

`quiet` berfungsi supaya kernel tidak akan menampilkan banyak pesan log ke console selama proses boot.

- **Screenshot:**

<img src="https://github.com/user-attachments/assets/5f2a3445-6781-4844-ae27-99e5fc30899f">
<img src="https://github.com/user-attachments/assets/b4957dde-e63e-4fd8-a68c-f714637f020a">

### Soal 9

> Ketika mencoba sistem operasi buatanmu, Budiman tidak bisa mengubah text file menggunakan text editor. Budiman pun menyadari bahwa dalam sistem operasi yang kamu buat tidak memiliki text editor. Budimanpun menyuruhmu untuk menambahkan **binary** yang telah disiapkan sebelumnya ke dalam sistem operasinya. Buatlah sehingga sistem operasi Budiman memiliki **binary text editor** yang telah disiapkan!

> _When trying your OS, Budiman cannot edit text files using a text editor. He realizes that the OS you created does not have a text editor. Budiman asks you to add the prepared **binary** into his OS. Make sure Budiman's OS has the prepared **text editor binary**!_

**Answer:**

- **Code:**

```
# Clone repository Budiman Text Editor #
git clone https://github.com/morisab/budiman-text-editor.git

# Akses Direktori budiman-text-editor #
cd budiman-text-editor

# Run File main.cpp untuk menghasilkan budiman #
g++ -static main.cpp -o budiman

# Copy budiman ke budinux/bin #
sudo cp budiman /home/$USER/osboot/budinux/bin

# Ubah File init #
nano init
```
Isi file `init` yang diperbarui export PATH=/bin untuk shell mencari perintah yang kamu jalankan:
``` bash
#!/bin/sh
/bin/mount -t proc none /proc
/bin/mount -t sysfs none /sys

export PATH=/bin

while true
do
    /bin/getty -L ttyS0 115200 vt100
    sleep 1
done
```
```
# Kompresi dan Buat Ulang File initramfs #
find . | cpio -oHnewc | gzip > ../budinux.gz
```
Cara menjalankan QEMU di `osboot`:
```
qemu-system-x86_64 \
  -kernel bzImage \
  -initrd budinux.gz \
  -append "quiet console=ttyS0,115200" \
  -serial mon:stdio \
  -nographic
```

- **Explanation:**

Pertama, kloning repository Budiman Text Editor dari GitHub dengan menggunakan `git clone`, lalu masuk ke direktori hasil kloning tersebut. Langkah ini bertujuan untuk mendapatkan kode sumber editor. Setelah masuk ke direktori proyek, lakukan kompilasi terhadap file `main.cpp` menggunakan `g++` dengan flag `-static`. Penggunaan opsi `-static` penting karena menghasilkan binary yang tidak tergantung pada shared library eksternal, sehingga cocok dijalankan dalam lingkungan initramfs yang sangat terbatas. Hasil kompilasi adalah file eksekusi bernama `budiman`.

Setelah file `budiman` berhasil dibuat, salin file tersebut ke direktori `bin` didalam `budinux`. Direktori `budinux/bin` akan menjadi tempat berbagai perintah dan binary yang dapat dieksekusi oleh sistem saat booting. Langkah berikutnya adalah mengubah file `init` dengan menambahkan perintah `export PATH=/bin`. Perintah ini sangat penting karena shell di initramfs tidak memiliki fitur pencarian path seperti sistem Linux lengkap. Dengan menambahkan `/bin` ke variabel lingkungan `PATH`, maka perintah `budiman` dapat dijalankan tanpa menyebutkan path lengkapnya.

Setelah semua file dan konfigurasi siap, langkah terakhir adalah membuat file initramfs yang berisi seluruh struktur direktori dan file dari `budinux`. Proses ini dilakukan dengan perintah `find`, `cpio`, dan `gzip`, yang menghasilkan file terkompresi bernama `budinux.gz`. File ini kemudian dapat digunakan bersama kernel Linux untuk booting sistem.

- **Screenshot:**

<img src="https://github.com/user-attachments/assets/0af36348-e935-4b02-9783-51b618ffba9b">
<img src="https://github.com/user-attachments/assets/454705e5-b663-4425-845a-4a8857f44d91">

### Soal 10

> Setelah seluruh fitur yang diminta Dosen dipenuhi dalam sistem operasi Budiman, sudah waktunya Budiman mengumpulkan tugasnya ini ke Dosen. Akan tetapi, Dosen Budiman tidak mau menerima pengumpulan selain dalam bentuk **.iso**. Untuk terakhir kalinya, Budiman meminta tolong kepadamu untuk mengubah seluruh konfigurasi sistem operasi yang telah kamu buat menjadi sebuah **file .iso**.

> After all the features requested by the lecturer have been implemented in Budiman's OS, it's time for Budiman to submit his assignment. However, Budiman's lecturer only accepts submissions in the form of **.iso** files. For the last time, Budiman asks for your help to convert the entire configuration of the OS you created into a **.iso file**.

**Answer:**

- **Code:**

```
# Akses direktori osboot #
cd osboot

# Buat direktori ISO #
mkdir -p budinuxiso/boot/grub

# Salin file kernel dan root filesystem #
cp bzImage budinuxiso/boot
cp budinux.gz budinuxiso/boot

# Buat file grub.cfg di budinux/boot/grub #
```
Isi file grub.cfg:
``` bash
set timeout=5
set default=0

menuentry "Budinux" {
  linux /boot/bzImage quiet console=ttyS0,115200
  initrd /boot/budinux.gz
}
```
```
# Buat file ISO bootable di direktori osboot #
grub-mkrescue -o budinux.iso budinuxiso
```
Cara menjalankan ISO di `osboot`:
```
qemu-system-x86_64 \
  -cdrom budinux.iso \
  -serial mon:stdio \
  -nographic
```

- **Explanation:**

Masuk ke direktori `osboot`. Setelah itu, dibuatlah struktur direktori ISO yang diperlukan oleh GRUB, yaitu `budinuxiso/boot/grub`. Folder ini akan berisi file kernel Linux `bzImage`, file initramfs `budinux.gz`, serta file konfigurasi GRUB `grub.cfg`.

Selanjutnya, file kernel `bzImage` dan initramfs `budinux.gz` disalin ke dalam direktori `budinuxiso/boot`. File `bzImage` adalah kernel Linux yang telah dikompilasi, sedangkan `budinux.gz` merupakan hasil akhir dari kompresi root filesystem yang berisi semua file dan konfigurasi sistem Budinux.

Kemudian dibuat file konfigurasi GRUB bernama `grub.cfg` di dalam direktori `budinuxiso/boot/grub`. Isi dari file ini menetapkan waktu tunggu selama 5 detik sebelum boot otomatis, serta memilih entri default pertama. Menu GRUB akan menampilkan satu entri bernama "Budinux" yang ketika dipilih akan menjalankan kernel dari `/boot/bzImage` dengan mode `quiet` dan output terminal diarahkan ke serial `ttyS0` pada kecepatan 115200 baud. Selain itu, GRUB juga mengatur file initramfs dari `/boot/budinux.gz`.

Terakhir, seluruh struktur direktori `budinuxiso` digunakan untuk membentuk file ISO bootable bernama `budinux.iso` menggunakan perintah `grub-mkrescue`. Perintah ini akan menghasilkan file image ISO yang dapat langsung digunakan untuk boot melalui QEMU. Dengan proses ini, sistem Budinux dibungkus ke dalam format ISO yang portabel dan dapat dijalankan di berbagai sistem


- **Screenshot:**

<img src="https://github.com/user-attachments/assets/0c970bd2-281f-415f-95a8-3271522c7d2c">
<img src="https://github.com/user-attachments/assets/2ac99451-9def-4497-8522-489c5510e610">

---

Pada akhirnya sistem operasi Budiman yang telah kamu buat dengan susah payah dikumpulkan ke Dosen mengatasnamakan Budiman. Kamu tidak diberikan credit apapun. Budiman pun tidak memberikan kata terimakasih kepadamu. Kamupun kecewa tetapi setidaknya kamu telah belajar untuk menjadi pembuat sistem operasi sederhana yang andal. Selamat!

_At last, the OS you painstakingly created was submitted to the lecturer under Budiman's name. You received no credit. Budiman didn't even thank you. You feel disappointed, but at least you've learned to become a reliable creator of simple operating systems. Congratulations!_
