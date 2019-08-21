# Resourcely

With minimal code, expose your resource folders to the rest of your package as importable namespaces!

In the `images` folder, I store my PNG images. With resourcely, I can add a `resources.yml` file like so:

```yaml
resources:
  type: namespace
  buttons:
    next: next.png
    previous: previous.png
    play: play.png
    pause: pause.png
```

And add an `__init__.py` file to tell Python to treat my resource folder as a package:

```python
import resourcely as rly

# Treat the folder in which the current file is located as the parent folder of the loaded resources
resources = rly.from_located_file('resources.yml', near=__file__)

# Expand namespace in module namespace, 
# so that we can write 'from images import X' to import them where we need them
rly.expand(resources, globals())
```

This enables us to import the defined namespaces from the newfound `images` package:

```python
>>> from images import buttons
>>> buttons.next.url
"/home/waluigi/only_otters/images/next.png"
>>> buttons.next.QUrl
PyQt5.QtCore.QUrl('file:///home/waluigi/only_otters/images/next.png')
```

A `Resource` is a simple structure with two fields: a name and a URL. The class has several utility properties to convert the URL to different formats, such as `str` and `QUrl` since we work with PyQt. It also implements [PEP 519](https://www.python.org/dev/peps/pep-0519/).

You can describe a list of resources in 3 ways:

```yaml
# as a list of (name, url) items
resources:
  - name: A
    url: /b/c.png
  ...
  
# as key-value pairs
resources:
  A: /b/c.png
  
# as a list of strings (`name` is then equal to `url`)
resources:
  - /b/c.png
```

The second syntax won't allow you to nest namespaces, unless you explicitly specify it:
```yaml
resources: # <- Not a key-value pair!
  type: namespace # <- Add this so that the tool knows that it's a namespace
  buttons: # <- Not a key-value pair!
    type: namespace # <- Add this so that the tool knows that it's a namespace
    player:
      A: /b/c.png
```
      
