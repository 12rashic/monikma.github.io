# sds
asdas
- sadfdsafasdf
- sdaf

<!DOCTYPE html>
<html lang="en" markdown="1">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">
    <title>
        {% if page.title %} {{ page.title }} {% else %} Mona's learning blog {% endif %}
    </title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="/css/bootstrap.min.css">
    <link rel="stylesheet" href="/css/blog.css">

    <!-- jQuery -->
    <script src="/js/jquery.js"></script>
    <!-- Bootstrap Core JavaScript -->
    <script src="/js/bootstrap.min.js"></script>
    <!-- Plugin JavaScript -->
    <script src="/js/jquery.easing.min.js"></script>
    <script src="/js/jquery.fittext.js"></script>
    <script src="/js/wow.min.js"></script>
    <!-- Custom Theme JavaScript -->
    <script src="/js/blog.js"></script>

</head>
<body markdown="1">

{% include menu.html %}

<div class="col-sm-9">

    {% include welcome.html %}
    <hr/>
    <h4>
        <small>RECENT POSTS</small>
    </h4>

    <ul class="list-unstyled">
        {% for post in site.posts %}
        <li>
            <div>
                <a href="{{ post.url }}" style="text-decoration:none;"><h3>{{ post.title }}</h3></a>
                <h5>
                    <span class="glyphicon glyphicon-time"></span> Post by {{ post.author }}, {{ post.date | date_to_long_string }}.
                </h5>
                <h5>{% for tag in post.tags %}<span class="label label-primary">{{ tag }}</span> {% endfor %}</h5>
                <div>{{ post.content |truncatehtml | truncatewords: 60 }}</div>
            </div>
            <hr>
        </li>
        {% endfor %}
    </ul>
</div>
<footer class="container-fluid">

</footer>

</body>
</html>