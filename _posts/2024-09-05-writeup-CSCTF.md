---
layout: post
title:  "Writeup CSCTF 2024"
tags: [writeup, misc]
authors: [1]
image: /assets/vietthao/CSCTF-2024/thumbnail.png
---

## 1. Share The Flag

>You are an investigator hired by a cybersecurity company to investigate recent flag leaks. You managed to get a hold of incriminatory messages traded by one of the company employees and a possible flag re-seller. You proceeded to temporarily arrest that same employee but he is going to be released soon unless you can prove that what he shared was the real deal. If you want to prevent this, you need to find the flag as soon as possible.
>
><img src="https://i.imgur.com/sCKoY2o.png">
>
>Note: This is a Challenge from our sponsor, and all premium features are out-of-scope. Please do not try to use any kind of automated tools or spam requests to solve it!
>
>Hint: There are 2 important pieces of information that one can get from the discord screenshot. One is the fact that the mention of a VPN, hints to the IP History page.
>
>Hint 2: Think about how discord knows to display such a good looking embed and the technology used in the site.
>
>Author: Cybersharing

Từ bức ảnh trong đề bài, ta thấy có đường link dẫn đến [https://cybersharing.net/s/f5a56b9b91398211](https://cybersharing.net/s/f5a56b9b91398211)

<img src="https://img001.prntscr.com/file/img001/3rDr7c3xQtuTVK50ikXkYA.png">

Tải file video.mp4 này về mở lên thì y như rằng thấy mặt của người anh Rick thân quen :D

<img src="https://img001.prntscr.com/file/img001/ZK8qqyrgRPiJFb9VzfLclA.png">

Thử vào upload 1 file, thấy có option "Save to IP history" và "View IP history", có liên quan tới thông tin mà bức ảnh kia nhắc tới, về "home vpn" với "cybersharing vpn" gì đó.

<img src="https://img001.prntscr.com/file/img001/aT5MSoUATUObEbSzbRT5sQ.png">

Sau khi upload file, vào trang "View IP history", ta được thông tin như sau:

<img src="https://img001.prntscr.com/file/img001/LOPkf1jdTN20TQ995BlQTA.png">

Có vẻ mấy hint trong đoạn chat mà ban tổ chức đưa ra hơi lạc đề một xíu, nên trong suốt nửa đầu cuộc thi không có đội nào giải được bài này, tới khi BTC đưa ra hint thứ 2 về việc Discord nhúng thông tin trong chat thì mới có đội bắt đầu giải được.

Thì ra cái mà BTC muốn hướng đến trong bài này đó là khai thác cơ chế hiển thị thông tin nhúng (embed) trong tin nhắn. Điều đó thể hiện ở chỗ khi chúng ta gửi một đường link bất kì trong Discord thì nó sẽ đọc thông tin từ đường dẫn đó và hiển thị thông tin (title, ảnh, metadata,...) giống như trong bức ảnh ban đầu.

Sau khi biết được thông tin này thì mình đã đi tìm xem Discord hiển thị thông tin nhúng bằng cách nào, và mình tìm được trang [https://discord.com/developers/embeds](https://discord.com/developers/embeds) cho phép debug xem link của chúng ta khi gửi lên Discord thì trông sẽ ra sao.

Nhập thử cái link file anh Rick ban đầu vào bấm debug, ta được thông tin như sau:

<img src="https://img001.prntscr.com/file/img001/gFVxjwVuS4q19emtFAFMZg.png">

Tiếp sau nhập vào link [https://cybersharing.net/history](https://cybersharing.net/history) (link trang view history lúc nãy) thì nó hiển thị embeds trống không

<img src="https://img001.prntscr.com/file/img001/Nd1LhCf0QiK6isXQ3HnnGA.png">

Tuy nhiên khi ta bật F12 lên xem network requests thì thấy trong response có trả về phần html body gì đó

<img src="https://img001.prntscr.com/file/img001/8IVC2zqjSOyMIwUb3zxq4w.png">

Đem phần body này save thành file html rồi mở lên thì thấy có đường dẫn tới file flag.txt ở [s/13f17b167f2229809a95fb9d8c725449](https://cybersharing.net/s/13f17b167f2229809a95fb9d8c725449). Tải file này về mở lên ta được flag.

<img src="https://img001.prntscr.com/file/img001/IG41PgHrSYyADncOfB1vQg.png">

Flag: `CSCTF{dd4a22b47251fd92207cc057c37728a2}`