---
layout: default
title: Code Highlighting Test
---
<h2>{{ page.title }}</h2>

##Raw Code

```
def MyClass:
    def __init__(self):
        print "good day"
      
print "hello, world !"
```

---

##highlight

{% highlight python %}
def MyClass:
    def __init__(self):
        print "good day"
      
print "hello, world !"
{% endhighlight %}

---

##```

```python
def MyClass:
    def __init__(self):
        print "good day"
      
print "hello, world !"
```

---

##~~~

~~~ python
def MyClass:
    def __init__(self):
        print "good day"
      
print "hello, world !"
~~~

##whitespace

    def MyClass:
        def __init__(self):
            print "good day"
      
    print "hello, world !"
    
---

### Table code

Directory | Description
----------|------------
`/bin`    | blah
`/boot`   | blah blah
`/dev`    | blee blo



  