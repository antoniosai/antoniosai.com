+++
author = "Antonio Saiful Islam"
title = "asdf, Tool yang berguna untuk para Programmer"
date = "2023-11-19"
description = "All-in-One SDK Manager atau Tool Version Manager"
tags = ["software developer", "software development", "linux", "coding", "sdk"]
image = "https://miro.medium.com/v2/resize:fit:1400/format:webp/1*UXpywcS-a6hJEJ_CE6kYWA.png"
+++

**ASDF**  merupakan  **Tool Version Manager**  yang berarti kita dapat  **melakukan management Tool / SDK**  yang kita butuhkan, lalu memilih versi nya sesuai kebutuhan.

Sebagai contoh. Di laptop / perangkat terdapat beberapa project aplikasi yang dimana membutuhkan SDK dan Versi masing-masing yang berbeda, misal:

-   [**_Project-A_**:](https://www.tokopedia.com/levida/tecno-pova-5-case-flip-cover-kulit-leather-case-sarung-buku-hitam-f58b9?extParam=ivf%3Dfalse%26src%3Dsearch&refined=true)  menggunakan  **_NodeJS v18.12.0_**
-   **_Project-B_**: menggunakan  **_NodeJS v16.18.0_**
-   **_Project-C_**: menggunakan  **_Java 17.0.6_**
-   **_Project-D_**: menggunakan  **_Java 21.0.1_**

Coba bayangkan apabila kita akan melakukan switch versi SDK dari  **_Project-A_**  ke  **_Project-B_**  (atau sebalik nya) ataupun  **_Project C_**  ke  **_Project-D_**  (atau sebalik nya)**_._**  Langkah normal nya adalah kita meng-uninstall Versi yang kita gunakan saat ini dan meng-install versi yang dibutuhkan. Apabila kita sering menggonta-ganti versi maka hal ini sangat menyebalkan. Ah, u know lah.

# Instalasi ASDF

Sebenar nya sudah dibahas pada situs resmi nya pada  [https://asdf-vm.com/guide/getting-started.html](https://asdf-vm.com/guide/getting-started.html). Namun kali ini saya akan tetep membahas nya. Saya menggunakan OS Debian Bookworm 12.

# Dependencies

Di dokumentasi nya dijelaskan terdapat 2 dependencies yaitu  **_curl_**  dan  **_git_**. Maka kita perlu menginstall nya dengan command

{{<  highlight bash >}}
sudo apt install curl git
{{<  / highlight >}}

# Download ASDF

Pada saat artikel ini dibuat, Versi  **_ASDF_**  nya yaitu 0.13.1. Untuk menginstall jalankan perintah

{{<  highlight bash >}}
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.13.1
{{<  /highlight >}}

# Install ASDF

Install ASDF di Linux dapat dilakukan dengan mengupdate file  **_~/.bashrc, ~/.bash_profile ataupun ~/.zshrc_**  (tergantung bash apa yang digunakan pada OS masing-masing), tapi cara nya sama saja kok. Saya menggunakan  **_~/.zshrc_**

{{<  highlight bash >}}
echo  '. "$HOME/.asdf/asdf.sh"' >> ~/.zshrc && source ~/.zshrc
{{<  /highlight >}}

# Cek Instalasi

Apabila langkah diatas sudah dilakukan. Lakukan perintah

{{<  highlight bash >}}
adsf --version  
v0.13.1-0586b37
{{<  /highlight >}}
# Menggunakan ASDF

Pada kali ini kita akan menggunakan studi kasus, yaitu saya akan menginstall NodeJS dengan berbagai versi, yaitu 18.12.0 & 16.18.0. Langkah sistematis nya yaitu kita perlu menambahkan  **_plugin_**, istilah ini plugin ini bisa kita artikan sebagai SDK yang akan kita gunakan.

# Mencari Plugin

{{<  highlight bash >}}
asdf plugin list all  // output nya menampilkan semua plugin / SDK yang tersedia di ASDF
{{<  /highlight >}}

atau apabila ingin mencari plugin dengan keyword tertentu, misal:  **_nodejs_**

{{<  highlight bash >}}
asdf plugin list all | grep nodejs  // akan muncul output dibawah  
nodejs                       *https://github.com/asdf-vm/asdf-nodejs.git
{{<  /highlight >}}

# Menginstall Plugin

Untuk menginstall plugin. Command nya adalah

{{<  highlight bash >}}
asdf plugin add [plugin-name] [plugin-url]
{{<  /highlight >}}

Kita akan menginstall plugin NodeJS.

asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git

# Melihat & Install Versi pada SDK

asdf list all nodejs  
// untuk melihat versi yang tersedia pada nodejs. nodejs bisa diganti sesuai dengan plugin yang tersedia dari asdf

Install NodeJS versi 18.16.0 dan juga 16.18.0

{{<  highlight bash >}}
asdf install nodejs 18.16.0

asdf install nodejs 16.18.
{{<  /highlight >}}

# Menggunkan Versi secara Global dan Local

{{<  highlight bash >}}
asdf local nodejs 18.16.0 // nodejs 18.16.0 akan digunakan locally pada folder tersebut.

asdf global nodejs 16.18.0
{{<  /highlight >}}

# Penutup

Terimakasih secara pribadi kepada Tim Pengembang dari ASDF  [https://asdf-vm.com](https://asdf-vm.com/)  . Karena telah mengembangkan sebuah Tool Version Manager / SDK Manager yang sangat bermanfaat pada pribadi.

Harapan nya adalah teman-teman dapat mengeksplore lebih dari apa yang ditulis dari artikel ini. Karena kita sebagai Software Engineer salah satu kekuatan kita adalah literasi, so jangan malas membaca dokumentasi resmi nya. Jadikan dokumentasi sebagai panutan utama nya hehehe…

Dah ah, salam!