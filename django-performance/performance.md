# 🔥 Performance in Django

> Its not needed until it does.

---

## 😴 Lazy execution

In django, sql queries are not evaluated until they are iterated over.

> The act of creating a QuerySet doesn’t involve any database activity. You can stack filters together all day long, and Django won’t actually run the query until the QuerySet is evaluated.

```python
q = Entry.objects.filter(headline__startswith="What")
q = q.filter(pub_date__lte=datetime.date.today())
q = q.exclude(body_text__icontains="food")
print(q) # SQL hits
```

---

## 🤔 Isnt being lazy good for performance?

Being lazy might be thought of as a performance boost as you are only executing query when required. But this may lead
to multiple queries being made to the database unknowingly.

```
~~~graph-easy --as=boxart
[ ModelA ] - one-to-one -> [ ModelB ]
~~~
```

```python
qs = ModelA.objects.all()

# a serializer step that adds ModelB
serializer = ModelASerializer(qs, many=True)
return qs
```

> Here for each Object of ModelA you are hiting the db for ModelB.

---

## 🤦 Whats the problem?

```
~~~graph-easy --as=boxart
[ Network calls ] - slower -> [ Python ] - slower -> [ SQL land]
~~~
```

---

## ✅ Solution

**Try to make a bigger SQL query.**

```python
qs = ModelA.objects.all().select_related('modelB') # includes SQL code for ModelB
serializer = ModelASerializer(qs, many=True) # doesn't make new SQL request for getting ModelB
return qs

```

---

# 🤠 Thankyou

> Off to [arjan](https://github.com/arjansunar) for the stuff done in DMS.

## 🧰 Tools used and references

- [slides](https://maaslalani.com/slides/)
- [graph-easy](https://metacpan.org/pod/Graph::Easy)
- [django queryset](https://docs.djangoproject.com/en/3.2/topics/db/optimization/#understand-querysets)
