Bug bounty writeup


<ul>
  {% for post in site.posts %}
    <li>
      <a href="/Blog{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
