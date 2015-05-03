---
layout: default
title: Contact Me
---

<div id="contact">
  <h1 class="pageTitle">Contact Me</h1>
  <div class="contactContent">
    <p class="intro">嘛，欢迎来戳~</p>
	<img src="{{ '/assets/img/miao.jpeg' | prepend: site.baseurl }}" alt=""> 
  </div>
  <form action="http://formspree.io/wlj459@gmail.com" method="POST">
    <label for="name">Name</label>
    <input type="text" id="name" name="name" class="full-width"><br>
    <label for="email">Email Address</label>
    <input type="email" id="email" name="_replyto" class="full-width"><br>
    <label for="message">Message</label>
    <textarea name="message" id="message" cols="30" rows="10" class="full-width"></textarea><br>
    <input type="submit" value="Send" class="button">
  </form>
</div>
