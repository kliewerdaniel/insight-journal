--- 
layout: home 
--- 
# Welcome to Insight Journal

<ul> {% for post in site.posts %} <li>
<h2><a href="{{ post.url }}">{{ post.title }}</a></h2> 
<p>{{ post.date | date: "%B %d, %Y" }}</p>
 </li> {% endfor %} </ul>