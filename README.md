# Multiple Model View

[Django Rest Framework](https://github.com/tomchristie/django-rest-framework) provides some incredible tools for serializing data, but sometimes you need to combine many serializers and/or models into a single API call.  **drf-multiple-model** is an app designed to do just that.

* Full Documentation: [https://django-rest-multiple-models.readthedocs.org/en/latest/](https://django-rest-multiple-models.readthedocs.io/en/latest/)
* Source Code: [https://github.com/Axiologue/DjangoRestMultipleModels](https://github.com/Axiologue/DjangoRestMultipleModels)
* PyPI: [https://pypi.python.org/pypi/django-rest-multiple-models](https://pypi.python.org/pypi/django-rest-multiple-models)
* License: MIT

# Installation

Install the package from pip:

```
pip install django-rest-multiple-models
```

Make sure to add 'drf_multiple_model' to your INSTALLED_APPS:

```
INSTALLED_APPS = (
    ....
    'drf_multiple_model',
)
```

Then simply import the view into any views.py in which you'd want to use it:

```
from drf_multiple_model.views import MultipleModelAPIView
```

**Note:** This package is built on top of Django Rest Framework's generic views and serializers, so it presupposes that Django Rest Framework is installed and added to your project as well.

# Basic Usage

**drf-multiple-model** comes with the `MultipleModelAPIView` generic class-based-view for serializing multiple models.  `MultipleModelAPIView` requires a `queryList` attribute, which is a list or tutple of queryset/serializer pairs (in that order).  For example, let's say you have the following models and serializers:

```
# Models
class Play(models.Model):
    genre = models.CharField(max_length=100)
    title = models.CharField(max_length=200)
    pages = models.IntegerField()

class Poem(models.Model):
    title = models.CharField(max_length=200)
    style = models.CharField(max_length=100)
    lines = models.IntegerField()
    stanzas = models.IntegerField()

# Serializers
class PlaySerializer(serializers.ModelSerializer):
    class Meta:
        model = Play
        fields = ('genre','title','pages')

class PoemSerializer(serializers.ModelSerializer):
    class Meta:
        model = Poem
        fields = ('title','stanzas')
```

Then you might use the `MultipleModelAPIView` as follows:


```
from drf_multiple_model.views import MultipleModelAPIView

class TextAPIView(MultipleModelAPIView):
    queryList = [
        (Play.objects.all(),PlaySerializer),
        (Poem.objects.filter(style='Sonnet'),PoemSerializer),
        ....
    ]
```

which would return:

```
[
    {
        'play' : [
                {'genre': 'Comedy', 'title': "A Midsummer Night's Dream", 'pages': 350},
                {'genre': 'Tragedy', 'title': "Romeo and Juliet", 'pages': 300},
                ....
            ],
    },
    {
        'poem' : [
                {'title': 'Shall I compare thee to a summer's day?', 'stanzas': 1},
                {'title': 'As a decrepit father takes delight', 'stanzas': 1},
                ....
            ],
    }
]
```

For configuration options, filtering tools, and more, see [the documentation](https://django-rest-multiple-models.readthedocs.org/en/latest/).
