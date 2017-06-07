### https://docs.djangoproject.com

## QuerySets : Executing queries

#### 2017. 06. 07

#### 실습하면서 했더니 정리는 거의 불가능!

* add로 추가가능
```python
>>> from blog.models import Author
>>> joe = Author.objects.create(name="Joe")
>>> entry.authors.add(joe)
```

* 쿼리셋은 게으르다 (QuerySets are lazy)
```python
>>> q = Entry.objects.filter(headline__startswith="What")
>>> q = q.filter(pub_date__lte=datetime.date.today())
>>> q = q.exclude(body_text__icontains="food")
>>> print(q)
```
> 이 예제를 입력하는동안 마지막 줄 print(q) 에서만 데이터베이스에 접속한다.

---
<br>
<br>

### 오류난 예제

* Entry 오브젝트가 비어있어 pk=1을 할당 받을 수 없음.

```python
>>> from blog.models import Entry
>>> entry = Entry.objects.get(pk=1)
>>> cheese_blog = Blog.objects.get(name="Cheddar Talk")
>>> entry.blog = cheese_blog
>>> entry.save()
```

* Entry 오브젝트 생성 예제

```python
>>> b = Blog.objects.get(id=1)
>>> e = Entry(
...     blog=b,
...     headline='Hello',
...     body_text='Hi',
...     pub_date=datetime.date(2005, 1, 1)
... )
>>> e.save(force_insert=True)
```

#### 첫번째 오류

* name 'datetime' is not defined
> import datetime

#### 두번째 오류

* Cannot assign must be a instance.

```python
    blog = models.ForeignKey(
        'Blog',
        related_name='entry_blog',   // related_name 지정해주고
        blank=True,                  // 블랭크, 널 값 True
        null=True,
    )
```

#### 세번째 오류

* NOT NULL constraint failed:

```python
    pub_date = models.DateField(blank=True, null=True, )
    mod_date = models.DateField(blank=True, null=True, )
    authors = models.ManyToManyField('Author')
    n_comments = models.IntegerField(blank=True, null=True, )
    n_pingbacks = models.IntegerField(blank=True,null=True, )
    rating = models.IntegerField(blank=True,null=True, )
```
> 내가 데이터 값을 지정하지 않는 부분에 null값을 가질수 있게 수정

<br>
<br>
<br>
---

### 아무것도 모르면 오류 수정하는데도 몇시간씩 걸린다......