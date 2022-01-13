---
layout: page
title: Everything is connected!
permalink: /about
comments: false
image: assets/images/about.jpg
imageshadow: true
---

Hi! my name is Joep and I'm passionate about home automation and computer technology! I started blogging about home automation because I find a lot of technical information on the Internet without it being clear why people automate it in a certain way. I also enjoy writing about concepts and ideas that might inspire you to make your own home even smarter.

My blog is focused on the [Fibaro System](https://www.fibaro.com/en/), but I regularly use other software such as [Node-RED](https://nodered.org/). The concepts I write about can also be implemented with other controllers like [Home Assistant](https://www.home-assistant.io/), [HomeSeer](https://homeseer.com/) and [openHAB](https://www.openhab.org/).

If you appreciate my work you can buy me a coffee!

<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="joep" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>

<div class="list-authors mt-5">
{% for author in site.authors %}   
    <div id="{{ author[1].name }}" class="authorbox position-relative pb-5 pt-5 mb-4 mt-4 border">   
        <div class="row">
            <div class="wrapavname col-md-3 text-center">
                {% if author[1].gravatar %}
                <img  class="author-thumb" src="https://www.gravatar.com/avatar/{{ author[1].gravatar }}?s=250&d=mm&r=x" alt="{{ author.display_name }}">
                {% else %}
                <img  class="author-thumb" src="{{site.baseurl}}/{{ author[1].avatar }}" alt="{{ author.display_name }}">
                {% endif %}
                <p class="mt-4 mb-0 small text-center">
                    <a target="_blank" class="d-inline-block mx-1 text-dark" href="{{ author[1].linkedin }}"><i class="fab fa-linkedin-in"></i></a> 
                    <a target="_blank" class="d-inline-block mx-1 text-dark" href="{{ author[1].github }}"><i class="fab fa-github"></i></a>
                    <a class="d-inline-block mx-1 text-dark" href="{{site.baseurl}}/contact/"><i class="fa fa-envelope"></i></a>
                </p>
            </div>
            <div class="col-md-9">
                <h3>{{ author[1].display_name }}</h3>
                <p class="mt-3 mb-0">{{ author[1].description }}</p> 
            </div>
        </div> 
    </div>    
{% endfor %}
</div>