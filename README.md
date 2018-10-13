# 解决这个问题分为以下步骤：
# 1.通过分析网络请求：发现图集请求服务端时，返回的数据，在json中，需要解析json获取相应数据，找到列表页URL
# 2.通过1，下一步就是通过URL，获取到详情页信息，进而获取到详情页数据--运用re模块
# 3.将获取到的数据，pandas里面

from urllib.parse import urlencode

import requests

import traceback

import json

from bs4 import BeautifulSoup

import re

#获取返回来的json数据

def image_list_url():

    data = {

        'offset':  0,
        'format': 'json',
        'keyword': '风景',
        'autoload': 'true',
        'count': 20,
        'cur_tab': 3,
        'from': 'gallery'

        }

    url="https://www.toutiao.com/search_content/?"

    response=requests.get(url,params=data)

    try:

        if response.status_code == 200:


            return response.text


        else:

            return None
    except:

        traceback.print_exc()

    else:


        print("没有发现异常")


#解析获取到的json数据，取出列表页的URL


def parse_url(html):


     da=json.loads(html)


     for i in da.get("data"):


           yield i.get("article_url")


#爬取详情页url

def get_page_detail(url):


    header={


        'user-agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',

        'cookie':'tt_webid=6588338229404354052; WEATHER_CITY=%E5%8C%97%E4%BA%AC; UM_distinctid=1638205c797119-00878908d7a3e6-33687706-13c680-1638205c7981d2; login_flag=5078c04c00b3da228d7f1a9f4795afe7; sessionid=266094e8ca823b832e5f97791e82e313; uid_tt=376230c30f71ddc2e10438858b2b9d3b; sid_tt=266094e8ca823b832e5f97791e82e313; sid_guard="266094e8ca823b832e5f97791e82e313|1526894866|15552000|Sat\054 17-Nov-2018 09:27:46 GMT"; uuid="w:536fb4e6b7794e45b18d0bbdd584e1c1"; csrftoken=35e5c8665163c4b71b37aa591f718eec; _ga=GA1.2.1153723191.1531811582; tt_webid=6588338229404354052; tt_webid=6588338229404354052; CNZZDATA1259612802=52382956-1526891427-https%253A%252F%252Fwww.baidu.com%252F%7C1538967042; __tasessionId=176o0ktor1538981438911'

    }




    response = requests.get (url,headers=header)


    try:

        if response.status_code == 200:

            return response.text


        else:

            return None
    except:

        traceback.print_exc ()

    else:

        print ("没有发现异常")

#通过re提取出里面的图片地址


def re_image(dehtml):

#初始化一个Beautifulsoul对象

    bs4=BeautifulSoup(dehtml,"html.parser")

    title=bs4.select("title")[0],string

    print(title)

    pattern=re.compile("BASE_DATA.galleryInfo ={.}",re.S)

    resule=re.search(pattern,dehtml)

    print(resule.group(1))






#调用各种业务函数

def main():

    html = image_list_url()


    parsehtml=parse_url(html)

    for i in parsehtml:


        dehtml=get_page_detail(i)

        re_image(dehtml)

        print(dehtml)



#启动main函数的调用

if __name__ == '__main__':

    main()



