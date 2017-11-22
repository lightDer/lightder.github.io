---
layout: post
title: Can XML content contain Ampersand ?
---

答案是不行，需要 escape 成 `&amp;`。
根據 [XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-chardata) 的規範

> The ampersand character (&) and the left angle bracket (<) `must not` appear in their literal form, except when used as markup delimiters, or within a comment, a processing instruction, or a CDATA section. If they are needed elsewhere, they must be escaped using either numeric character references or the strings "&amp;" and "&lt;" respectively

對於懂 XML 的這是個基本知識，但有些事總是遇到了，過後才明瞭。

起因是在 paring 某家 IPCAM 的 ONVIF stream URI 時。它的 tag 是

```
<tt:Uri>rtsp://192.168.1.100:554/cam/realmonitor?channel=1&amp;subtype=0&amp;unicast=true&amp;proto=Onvif</tt:Uri>
```
程式把 URI 拿出來就直接用了，沒有把 `&amp;` decode 成 `&`，
拿著這串夾帶 `&amp;` 的 URI 去向 IPCAM 做請求，回覆 `404 Not Found`，連線失敗，修正成 `&` 後就 OK 了。
