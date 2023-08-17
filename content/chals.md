# My challenges

I've been writing challenges since 2022, which is not a long time, but all of my challenges have more or less been scattered throughout the wind. This is my attempt to consolidate the ones I wrote. 

## MAPLECTF

* [Honksay](https://github.com/ubcctf/maple-ctf-2022-public/tree/main/web/honksay)
    * XSS with object bypass

* [Viene Library](https://jamvie.net/posts/2022/09/maplectf-2022-vies-challenges/#viene-library-11-solves)
    * Prototype Pollution to leverage PUT request-based header overrides in Ruby

* [Art Gallery](https://jamvie.net/posts/2022/09/maplectf-2022-vies-challenges/#art-gallery-3-solves)
    * TLS poison -> FTP -> Redis SSRF chain for deserialization RCE

## PBCTF

* [Makima](posts/2023/02/pbctf-2023-jazzy-x-vie-chals/#makima)
    * FastCGI PHP and nginx path resolution for PHP RCE combined with `X-Accel-Redirect` header SSRF

## GOOGLE CTF

* [Veggie Soda](https://github.com/google/google-ctf/tree/master/2023/web-vegsoda)
    * CSRF bypass to TypeScript type-confusion deserialization, causing pop chain-esque effects to pop XSS

## HTB BUSINESS CTF: THE GREAT ESCAPE

* [Redwave](https://www.hackthebox.com/events/htb-business-ctf-2023)
    * Golang -> Ruby JSON parsing differential + SSRF bypass with header parsing differential + Ruby deserialization RCE