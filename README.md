# 一、爬虫对象-豆瓣电影TOP250
前几天，我分享了一个python爬虫案例，爬取豆瓣读书TOP250数据：[【python爬虫案例】用python爬豆瓣读书TOP250排行榜！ ](https://www.cnblogs.com/mashukui/p/17514196.html)

今天，我再分享一期，python爬取豆瓣电影TOP250数据！

爬虫大体流程和豆瓣读书TOP250类似，细节之处见逻辑。

首先，打开豆瓣电影TOP250的页面：https://movie.douban.com/top250
![豆瓣电影TOP250页面](https://img2023.cnblogs.com/blog/2864563/202306/2864563-20230629164733250-1961712412.png)

开发好python爬虫代码后，爬取成功后的csv数据，如下：
![结果数据](https://img2023.cnblogs.com/blog/2864563/202306/2864563-20230629164750857-1545535178.png)

代码是怎样实现的爬取呢？下面逐一讲解python核心代码。

# 二、python爬虫代码讲解
首先，导入需要用到的库：
```python
import requests  # 发送请求
from bs4 import BeautifulSoup  # 解析网页
import pandas as pd  # 存取csv
from time import sleep  # 等待时间
```
然后，向豆瓣电影网页发送请求：
```python
res = requests.get(url, headers=headers)
```
利用BeautifulSoup库解析响应页面：
```python
soup = BeautifulSoup(res.text, 'html.parser')
```
用BeautifulSoup的select函数，（css解析的方法）编写代码逻辑，部分核心代码：
```python
for movie in soup.select('.item'):
	name = movie.select('.hd a')[0].text.replace('\n', '')  # 电影名称
	movie_name.append(name)
	url = movie.select('.hd a')[0]['href']  # 电影链接
	movie_url.append(url)
	star = movie.select('.rating_num')[0].text  # 电影评分
	movie_star.append(star)
	star_people = movie.select('.bd div span')[3].text  # 评分人数
	star_people = star_people.strip().replace('人评价', '')
	movie_star_people.append(star_people)
```
其中，需要说明的是，《大闹天宫》这部电影和其他电影页面排版不同：
![大闹天宫](https://img2023.cnblogs.com/blog/2864563/202306/2864563-20230629164831097-1527451437.png)

它的上映年份有3个（其他电影只有1个上映年份），并且以"/"分隔，正好和国家、电影类型的分割线冲突，

所以，这里特殊处理一下：
```python
if name == '大闹天宫 / 大闹天宫 上下集  /  The Monkey King':  # 大闹天宫，特殊处理
	year0 = movie_infos.split('\n')[1].split('/')[0].strip()
	year1 = movie_infos.split('\n')[1].split('/')[1].strip()
	year2 = movie_infos.split('\n')[1].split('/')[2].strip()
	year = year0 + '/' + year1 + '/' + year2
	movie_year.append(year)
	country = movie_infos.split('\n')[1].split('/')[3].strip()
	movie_country.append(country)
	type = movie_infos.split('\n')[1].split('/')[4].strip()
	movie_type.append(type)
```
最后，将爬取到的数据保存到csv文件中：
```python
def save_to_csv(csv_name):
	"""
	数据保存到csv
	:return: None
	"""
	df = pd.DataFrame()  # 初始化一个DataFrame对象
	df['电影名称'] = movie_name
	df['电影链接'] = movie_url
	df['电影评分'] = movie_star
	df['评分人数'] = movie_star_people
	df['导演'] = movie_director
	df['主演'] = movie_actor
	df['上映年份'] = movie_year
	df['国家'] = movie_country
	df['类型'] = movie_type
	df.to_csv(csv_name, encoding='utf_8_sig')  # 将数据保存到csv文件
```
其中，把各个list赋值为DataFrame的各个列，就把list数据转换为了DataFrame数据，然后直接to_csv保存。

这样，爬取的数据就持久化保存下来了。

# 三、同步视频
代码讲解视频：[【python爬虫】利用python爬虫爬取豆瓣电影TOP250的数据！](https://www.zhihu.com/zvideo/1465578220191592448)
​
# 四、作者声明

本源码首发公众号“**老男孩的平凡之路**”，后台回复“**豆瓣电影250**”免费即可获取。![二维码-公众号放底部](https://github.com/user-attachments/assets/74c114a1-bc14-4561-8c79-7c7290c960f2)

源码免费开源。如对你有帮助，请给项目点个star✨，或者打赏作者。

用户的支持将是我持续创作的最大动力！<img width="1528" height="918" alt="收款码v2" src="https://github.com/user-attachments/assets/0dab2a08-d7e0-4cc7-af5d-bd197107fb2a" />


____
我是 [@马哥python说](https://github.com/mashukui) ，一名10年程序猿，持续分享python干货中！
