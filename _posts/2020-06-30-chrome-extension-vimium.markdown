---
layout: post
title: Chrome extension Vimium
date: 2020-06-30 00:00:00 +0300
description: Chrome extension Vimium
tags: [Chrome, Vimium]
img: vim.png
---

## Vim in Chrome

### Navigating the page
* j, <c-e>		Scroll down    // <c-e> = ctrl + e
* k, <c-y>		Scroll up      // <c-e> = ctrl + y
* gg		Scroll to the top of the page
* G		Scroll to the bottom of the page
* d		Scroll a half page down
* u		Scroll a half page up
* h		Scroll left
* l		Scroll right
* r		Reload the page

### Navigating history
* H		Go back in history
* L		Go forward in history

### Url, history and bookmark
* yy		Copy the current URL to the clipboard
* p		Open the clipboard's URL in the current tab
* P(capital)		Open the clipboard's URL in a new tab
* gi		Focus the first text input on the page
* f		Open a link in the current tab
* F		Open a link in a new tab
* o		Open URL, bookmark or history entry
* O		Open URL, bookmark or history entry in a new tab
* b		Open a bookmark
* B		Open a bookmark in a new tab

* i		Enter insert mode
* v		Enter visual mode
* gf		Select the next frame on the page
* gF		Select the page's main/top frame

### Using the vomnibar
* T		Search through your open tabs

### Using find
* /		Enter find mode
* n		Cycle forward to the next find match
* N		Cycle backward to the previous find match

### Manipulating tabs
* t		Create new tab
* J, gT		Go one tab left
* K, gt		Go one tab right
* ^		Go to previously-visited tab
* g0		Go to the first tab
* g$		Go to the last tab
* yt		Duplicate current tab
* <a-p>		Pin or unpin current tab
* <a-m>		Mute or unmute current tab
* x		Close current tab
* X		Restore closed tab

### Miscellaneous
* ?		Show help

#### 검색 패턴
1. t(Create a new tab)
2. gi(foucus the first text input, , does not work on google main page) -> type a keyword
3. j(down)/k(up)/u(half page up)/d(hal page down)/h(left)/l(right) -> move to interesting articles(links)
4. F(Open a link in a new tab) -> i.e) multiple articles
5. f(Open a link in the current tab) -> i.e) go to next page or keep the tab
6. J(left tab)/K(right tab) / H(go back in history),L(go forward)
7. gg(top of the page)/G(bottome of page)
8. x(close current tab) // X (reopen the tab just closed)