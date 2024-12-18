---
title: learn basic web3 from solana - part1
date: '2024-12-18'
draft: false
tags:
  - solana
  - web3
comments: {}
---
jadi pada post sebelumnya, saya belajar di video youtube [https://youtu.be/amAq-WHAFs8](https://youtu.be/amAq-WHAFs8)  namun setelah menonton video tersebut saya stuck di part 2. saya merasa kemampuan saya masih kurang untuk lanjut ke part berikutnya dan metode belajar saya sepertinya juga salah, saya seperti copy paste kode yang ditulis tanpa mengerti kenapa harus menuliskan kode tersebut.

<!--more-->

jadi saya memulai pendekatan baru, dengan membaca panduan di [https://solana.com/developers](https://solana.com/developers) 

baca mulai dari sini
> [https://solana.com/developers/courses/intro-to-solana/getting-started](https://solana.com/developers/courses/intro-to-solana/getting-started)

pada dokumen ini kita akan mempelajari sedikit teori tentang web3 dan solana, dan juga beberapa code yang bisa kita coba.

disini saya menggunakan deno sebagai runtimenya alasan utama karena saat ini saya memang fokus dengan deno dan juga deno sudah support ts out of the book, sehingga tidak perlu install typescript lagi.

anda tidak wajib untuk menggunakan deno, tapi bagi yang menggunakan deno bisa menggunakan command dibawah, untuk init project di folder yg sudah disiapkan
```
deno init
deno task dev
```

oke kita mulai saja pertama kita akan melakukan

### #1 pembuatan keypair (public key, secret key)
install dependecies
```bash
deno add 'npm:@solana/web3.js'
```

code untuk membuat pair key
```ts
import {Keypair} from "@solana/web3.js"

const keypair = Keypair.generate()

console.log(`the public key is: ${keypair.publicKey.toBase58()}`)
console.log(`the secret key is: ${keypair.secretKey}`)
```

setelah anda mendapatkan publickey dan secretkey simpan datanya di .env
seperti berikut
```
PUBLIC_KEY=<string>
SECRET_KEY=[<number>,..]
```
data ini akan kita gunakan di kode selanjutnya

### #2 import secret key from env

install dependecies
```bash
deno add "npm:dotenv/config"
deno add "npm:@solana-developers/helpers"
```

code
```ts
import "dotenv/config"
import {getKeypairFromEnvironment} from "@solana-developers/helpers"

const keypair = getKeypairFromEnvironment("SECRET_KEY")

console.log(`the public key is: ${keypair.publicKey.toBase58()}`)
console.log(`the secret key is: ${keypair.secretKey}`)
```

### #3 connect to solana network

code
```ts
import "dotenv/config"
import {Connection, LAMPORTS_PER_SOL, PublicKey} from "@solana/web3.js"
import process from "node:process";

const connection = new Connection("https://api.devnet.solana.com", "confirmed")
const publicKey = new PublicKey(process.env["PUBLIC_KEY"] ?? "")
const balanceInLamports = await connection.getBalance(publicKey)
const balanceInSOL = balanceInLamports / LAMPORTS_PER_SOL

console.log(`finished! balance for address: ${process.env["PUBLIC_KEY"]} is ${balanceInSOL}!`)
```

saat anda petama kali menjalankan anda akan mendapatkan solana anda 0, sekarang anda isi balance anda melalui [https://faucet.solana.com
](https://faucet.solana.com)
silahkan dijalankan dan cek nilai balance anda akan bertambah.

> sebagai catatan ketika anda menambahkan balance faucet, solana yang ada dipublik tidak terpengaruh, faucet ini digunakan untuk melakukan uji coba

> disini meskipun saya menggunakan deno, saya membuat kode ini agar tetap bisa dijalankan di nodejs dikarenakan tidak menggunakan kode' khusus untuk deno dan menggunakan kompabiliti seperti node:process agar bisa berjalan pada nodejs dan deno

> sepertinya saya juga tidak perlu menjelaskan kode nya lagi dikarenakan nama variable yang mencerminkan fungsinya dan catatan anda juga sudah memiliki basic tentang typescript
