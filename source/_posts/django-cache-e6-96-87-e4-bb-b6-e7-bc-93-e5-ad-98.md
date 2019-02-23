---
title: Django Cache 文件缓存
tags:
  - Django
  - python
id: 952
categories:
  - Django
date: 2015-11-25 13:46:24
---

setting 部分代码：
```
    CACHES = {
        'default': {
            'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
            'LOCATION': os.path.join(BASE_DIR, 'cache'),
            'TIMEOUT': 60,
            'OPTIONS': {
                'MAX_ENTRIES': 1000
            }
        }
    }
    

 
```

    ### 缓存视图

    view部分代码

```
    @cache_page(60 * 15, key_prefix="site1")
    def index(request):
    

 ```

    ### 缓存模板

    html部分代码：

```
    {% load cache %}
    {% cache 500 sidebar %}
        .. sidebar ..
    {% endcache %}
    

 ```

    ### 缓存指定数据

    view 代码

```
    from django.core.cache import cache
    cache.set('key','hello,world!',30)
    value = cache.get('key') # hello,world!
```
    