---
title: first program in solana - building favorites with anchor in solana playground
date: '2024-12-02'
draft: false
tags:
  - solana
  - rust
comments: {}
---
ini merupakan project pertama dari video 
[https://youtu.be/amAq-WHAFs8](https://youtu.be/amAq-WHAFs8)

disini saya mempelajari tentang bagaimana solana menyimpan dan mengambil data menggunakan signing untuk mengontrol akses.

<!--more-->

data pada solana disimpan di Program Derived Addresses 
(PDAs)

> Program Derived Addresses (PDAs) adalah alamat akun khusus di blockchain Solana yang memiliki beberapa sifat unik. PDA tidak memiliki private key terkait, sehingga tidak dapat dikontrol oleh pengguna biasa. Sebaliknya, PDA dikendalikan oleh program Solana yang terkait dengannya.

berikut hasil kodingan, mengikuti dari video

```rust
use anchor_lang::prelude::*;

declare_id!("F8XiugcNnr8AbBqmpJNFCmBM1RcRwocs4x8Gn6JMGSBw");

pub const ANCHOR_DISCRIMINATOR_SIZE: usize = 8;

#[program]
pub mod favorites {
    use super::*;

    pub fn set_favorites(
        context: Context<SetFavorites>,
        number: u64,
        color: String,
        hobbies: Vec<String>,
    ) -> Result<()> {
        msg!("Greetings from {}", context.program_id);
        let user_public_key = context.accounts.user.key();

        msg!(
            "User {}'s favorite number is {}, favorite color is {}, and their hobbies are {:?}",
            user_public_key,
            number,
            color,
            hobbies
        );
        context.accounts.favorites.set_inner(Favorites {
            number,
            color,
            hobbies,
        });

        Ok(())
    }
}

#[account]
#[derive(InitSpace)]
pub struct Favorites {
    pub number: u64,

    #[max_len(50)]
    pub color: String,

    #[max_len(5, 50)]
    pub hobbies: Vec<String>,
}

#[derive(Accounts)]
pub struct SetFavorites<'info> {
    #[account(mut)]
    pub user: Signer<'info>,

    #[account(
        init_if_needed,
        payer = user,
        space = ANCHOR_DISCRIMINATOR_SIZE + Favorites::INIT_SPACE,
        seeds = [b"favorites", user.key().as_ref()],
        bump
    )]
    pub favorites: Account<'info, Favorites>,

    pub system_program: Program<'info, System>,
}

```

kode diatas bisa anda uji coba melalui web [https://beta.solpg.io/](https://beta.solpg.io/)
