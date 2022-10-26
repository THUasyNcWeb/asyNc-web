# Tables of Database Web

> 采用 PostgreSQL

1. news
   
   > store the news from web

   | Name | Type | Attribution |
   | :--- | :--- | :---------- |
   | id | AutoField | primary_key=true & db_index=True |
   | news_url | URLField | unique=True |
   | media | CharField | max_length=20 |
   | category | ArrayField | base_field=models.CharField(mex_length=30) & size=5 |
   | tags | ArrayField | base_field=models.CharField(mex_length=30) & size=8 | 
   | title | CharField | max_length=200 |
   | description | TextField | |
   | content | TextField | |
   | first_img_url | URLField | |
   | pub_time | DateTimeField | |

2. user_basic_info
   
   > store basic user information
   
   | Name | Type | Attribution |
   | :--- | :--- | :---------- |
   | id | AutoField | primary_key=true |
   | user_name | CharField | max_length=12 & unique=True |
   | password | CharField | max_length=40 |
   | signature | CharField | max_length=200 |
   | tags | DictField | allow_empty=True & blank=True |

3. search_history
   
   > store the user's search history

   | Name | Type | Attribution |
   | :--- | :--- | :---------- |
   | id | AutoField | primary_key=true |
   | user | ForeignKey | on_delete=CASCADE |
   | content | CharField | max_length=38 |

4. user_preference
   
   > store the user's preferences

   | Name | Type | Attribution |
   | :--- | :--- | :---------- |
   | id | AutoField | primary_key=true |
   | user | ForeignKey | on_delete=CASCADE |
   | word | CharField | max_length=10 |
   | num | IntegerField | default=0 |