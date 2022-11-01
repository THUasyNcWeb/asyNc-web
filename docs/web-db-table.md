# Tables of Database Web

> 采用 PostgreSQL

1. news
   
   > store the news from web

   | Name | Type | Attribution |
   | :--- | :--- | :---------- |
   | id | AutoField | primary_key=true & db_index=True |
   | news_url | URLField | unique=True |
   | media | CharField | max_length=20 |
   | category | CharField | max_length=20 |
   | tags | ArrayField | base_field=models.CharField(mex_length=30) & size=8 | 
   | title | CharField | max_length=200 |
   | description | TextField | |
   | content | TextField | |
   | first_img_url | URLField | |
   | pub_time | DateTimeField | |
   | comments | Integer | |

   category 对应：

   | Category | Name |
   | :------: | :--: |
   | \ | 要闻 |
   | ent | 娱乐 |
   | sports | 体育 |
   | mil | 军事 |
   | politics | 国际 |
   | tech | 科技 |
   | social | 社会 |
   | finance | 金融 |
   | auto | 汽车 |
   | game | 游戏 |
   | women | 时尚 |
   | health | 健康 |
   | history | 历史 |
   | edu | 教育 |

2. user_basic_info
   
   > store basic user information
   
   | Name | Type | Attribution |
   | :--- | :--- | :---------- |
   | id | AutoField | primary_key=true |
   | user_name | CharField | max_length=12 & unique=True |
   | password | CharField | max_length=40 |
   | signature | CharField | max_length=200 & blank=True |
   | tags | DictField | allow_empty=True |
   | mail | CharField | max_length=100 & blank=True |
   | avatar | TextField | blank=True |

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