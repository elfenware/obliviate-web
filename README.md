<p align="center">
   <img src="public/favicon.svg" alt="Icon" />
</p>

<h1 align="center">Obliviate</h1>
<p align="center">A password manager that forgets your passwords</p>

## Description

Obliviate does not store your passwords, but gives them to you when you need them. How?

It asks you for two things:

- the site you want to log in to
- a cipher key, which is any passphrase <a href="https://xkcd.com/936/" target="_blank">you can remember</a>

Using these, it will derive a password, which you can set as your new password for that site.

The next time you need it, enter the same site and same cipher key. Obliviate will derive the same password as before.

It’s not magic, but it’s quite close.

## Developing and building

Once you have Node.js installed, clone this repository, then run

```shell
npm install
```

Every time you want to run the development server, run

```shell
npm run dev
```
