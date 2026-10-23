<img width="1200" height="128" alt="213" src="https://github.com/user-attachments/assets/52709154-6067-4897-9762-a2f5fb9530af" />
<h1> NoShorts! </h1>

This script, built for Tampermonkey®, is designed to save you time and keep you from turning into a "zombie" while mindlessly scrolling through endless short-form videos.
I’ll be honest: the extension was written with the help of Claude-but there’s certainly no shame in using an AI for such a good cause!
The project is completely open-source! I’d be thrilled if you could help me track down and fix any bugs.

```
// ==UserScript==
// @name         Hide Shorts UI
// @match        https://www.youtube.com/*
// @match        https://m.youtube.com/*
// @run-at       document-start
// @grant        none
// @icon         https://i.ibb.co/dJ75rdSw/noshorts.png
// ==/UserScript==

(function () {
  const style = document.createElement("style");
  style.textContent = `
    ytd-guide-entry-renderer:has(a[href*="/shorts"]),
    ytd-guide-entry-renderer:has(a[title="Shorts"]),
    ytd-mini-guide-entry-renderer:has(a[href*="/shorts"]),
    ytd-mini-guide-entry-renderer[aria-label="Shorts"],

    ytd-rich-section-renderer:has(ytd-rich-shelf-renderer[is-shorts]),
    ytd-rich-section-renderer:has(ytm-shorts-lockup-view-model),
    ytd-reel-shelf-renderer,
    ytd-rich-item-renderer:has(ytm-shorts-lockup-view-model),

    grid-shelf-view-model:has(ytm-shorts-lockup-view-model),
    ytd-video-renderer:has(a[href*="/shorts/"]),
    ytd-compact-video-renderer:has(a[href*="/shorts/"]) {
      display: none !important;
    }
  `;
  (document.head || document.documentElement).appendChild(style);

  const hideMenuEntries = () => {
    document
      .querySelectorAll("ytd-guide-entry-renderer, ytd-mini-guide-entry-renderer")
      .forEach((el) => {
        if (el.style.display === "none") return;
        const a = el.querySelector("a");
        const href = (a && a.getAttribute("href")) || "";
        const title = (a && a.getAttribute("title")) || el.getAttribute("aria-label") || "";
        if (href.includes("/shorts") || title.trim().toLowerCase() === "shorts") {
          el.style.display = "none";
        }
      });
  };

  let timer = null;
  const schedule = () => {
    if (timer) return;
    timer = setTimeout(() => {
      timer = null;
      hideMenuEntries();
    }, 300);
  };

  const start = () => {
    new MutationObserver(schedule).observe(document.body, {
      childList: true,
      subtree: true,
    });
    hideMenuEntries();
  };

  if (document.body) start();
  else document.addEventListener("DOMContentLoaded", start);
})();
```
