---
layout: single
title: "PWA of QR code to WhatsApp"
output: html_document
permalink: PWA-QR-WhatsApp 
tags: [qr, PWA, WhatsApp]
categories:
  - qr
---

Earlier this year I was in China, and in order to communicate with local delegates I installed WeChat. WeChat has a very handy feature that replaces exchanging telephone numbers manually, and then waiting until WhatsApp picks up the new contract from the contracts app, by a QR code. One person brings up his QR code from within the app and the other person scans this code, and that's it! you are connected.

WhatsApp does not offer this. However, it does offer a simple API to open a message to a phonenumber (even if not a contact). It is trivial to turn the URL that uses this API into a QR code.

The QR code is already useful itself, however I wanted to put this QR code on the homescreen of my phone, and the easiest way to do this is using a Progressive Web App (PWA).

I created a simple PWA that includes some basic instructions (open the camera app on your phone and scan the below code) and the QR code itself (as SVG text). 
You can find the code for the app here: https://github.com/bquast/qr

When I visited https://qua.st/qr it poped up a promt if I wanted to install the Web App to my phone, which I didn.

I now have an app on my homescreen that I can open whenever I want to exchange contact information. Anyone scan scan this code and it will open their whatsapp in a conversation with me. 

