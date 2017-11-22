---
layout: post
title: RTSP Embedded interleaved binary data format
---

只能在 RTSP over TCP 使用，在 SETUP 時加上 interleaved 的參數，後面的數字分別對應 RTP/RTCP 的 channel ID。
client request 指定的 channel number **只是參考**，server 不一定會採用，具體還是要看 server 回覆的訊息。
如下例，分別指定 RTP/RTCP 是 0/1，server 回覆是 5/6，所以在收碼流的時候要用 5/6（RTC/RTCP）來判斷。

```
C->S: SETUP rtsp://example.com/bar.file RTSP/2.0
      CSeq: 2
      Transport: RTP/AVP/TCP;unicast;interleaved=0-1
      Accept-Ranges: npt, smpte, clock
      User-Agent: PhonyClient/1.2

S->C: RTSP/2.0 200 OK
      CSeq: 2
      Date: Thu, 05 Jun 1997 18:57:18 GMT
      Transport: RTP/AVP/TCP;unicast;interleaved=5-6
      Session: OccldOFFq23KwjYpAnBbUr
      Accept-Ranges: npt
      Media-Properties: Random-Access=0.2, Immutable, Unlimited
```

RTSP method 問答結束後就進入收碼流的階段，每一包 RTP packet 的前 4 個 byte 分別是

- [0]，`$` (0x24)，用來鑑別開頭
- [1]，channel identifier (RTC/RTCP)
- [2 - 3]，是指後面 RTP packet 的長度，two-octet unsigned integer in network octet order (Big-Endian)

```
S->C: $005{2 octet length}{"length" octets data, w/RTP header}
S->C: $005{2 octet length}{"length" octets data, w/RTP header}
S->C: $006{2 octet length}{"length" octets  RTCP packet}
```

![rtsp-stream-embedded-interleaved-binary-data-format](/public/images/rtsp-stream-embedded-interleaved-binary-data-format.png)

## reference

- https://tools.ietf.org/html/rfc7826#page-101Embedded (Interleaved) Binary Data method
- https://tools.ietf.org/html/rfc7826#section-18.54 Transport
