---
date: '2025-01-01T22:29:03+05:30'
draft: false
title: "A Research paper library using Zotero"
summary: "Setting up Zotero and Infinicloud webdav for saving research papers."
---

I've been looking for a solution to keep track of the papers I have read from Arxiv and so far I think [Zotero](https://www.zotero.org/download/) works well for the things I care about viz: cloud storage for papers, cross device syncing, ease of adding papers, free and open source. I had to setup a [Infini-cloud]() account so I can use the free tier 20GB storage rather than zotero's default 300MB (surprisingly easy to use up with research papers!)

## You'll need:
1. A free zotero account, you can set it up from within the app after download.
2. A free [infini-cloud](https://account.teracloud.jp/RegistForm.php/index/) account.

## Steps:

1. Log in to the infini-cloud account and head to the `My Page` section.
2. In the page navigate to, `Apps Connection > Turn on Apps Connection` and enable it.
3. Setup the webdav credentials and make sure to save the connection password (only shown once)
4. Open the zotero app in your devices and head to the settings.
5. Go to `Settings > Sync` and confirm you are logged in and the right account is linked in `Data Syncing`. Under `File Syncing`, just below, enable `Sync attachment files under My Library using` and choose `WebDAV`
6. Copy the WebDAV url from infini-cloud which is similar to `chogo.teracloud.jp/dev` and enter the connection username and password and tap on `Verify Server`
7. If everything went well, you'll see a successfully verified message and your device is setup. Use the same WebDAV credentails to setup sharing across all your zotero installations.

## Adding papers to library
1. One click addition of paper to zotero library. From the [zotero downloads page](https://www.zotero.org/download/) you can install the browser extension which auto-detects Arxiv, DOI and many other sites and enables saving with a single click.
2. On mobile, with the arxiv paper open in the tab, go to the sharing options and choose zotero app to be able to instantly add the paper to your library.