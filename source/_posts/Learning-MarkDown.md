---
title: Learning MarkDown
date: 2023-12-18 11:25:17
tags:
    - MarkDown
    - learning
    - coding
---
# This is my first line of markdown
## This is the second line of markdown
### This is the third
#### This is the fourth
##### This is the fifth

Instructions for setting up GraphQL in a Django Project.

---

[Graphene Documentation](https://www.ozak.ca)

**Table *of* Contents:**

~~Table of Contents:~~

1. [Why Graphql?](#why-graphql)
1. [Graphene Installation](#graphene-installation)
1. Creating schema
1. Using Graphql

## Why Graphql?

- Get only the data that you want
- Easier to manage endpoints

## Graphene Installation

Install Graphene: `pip install django_graphene`

## Creating Schemas

```py
# cookbook/schema.py
import graphene
from graphene_django import DjangoObjectType

from cookbook.ingredients.models import Category, Ingredient

class CategoryType(DjangoObjectType):
    class Meta:
        model = Category
        fields = ("id", "name", "ingredients")
        ...
schema = graphene.Schema(query=Query)
```

---

> The secret to doing anything is believing that you can do it. Anything that you believe you can do strong enough, you can do. Anything. As long as you believe.
>
>> No pressure, just relax and do it
>
>-- <cite>Bob Ross</cite>

## Do the algorithm everyday to sharpen my skills but still not get me a job!

[![Check-if-prime-number](images/is-prime.jpeg)](https://ozak.ca)

MarkDown also support html tags<br>see more text

## Roadmap

- [ ] Task #1
- [x] Task #2
- [ ] Task #3

---

| name | current state | next state |
| :------ | :------ | :------ |
| Green | Green | Yellow |
| Yellow | Green | Yellow |

<details>
    <summary>Section Header</summary>
    Section body text here.

    - hello
    - test
    - world!
</details>

## Foot Notes

Happy Trees Are Happy [^1]

## Footer
[^1]: By Bob Ross

## emoji
[破涕为笑]