# python
testing code
import requests
from bs4 import BeautifulSoup
from pyecharts import Bar

ALL_DATA=[]
def parse_page(url):
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.61 Safari/537.36',
    }
    response=requests.get(url,headers=headers)
    text=response.content.decode('utf-8')
    #html5lib   pip install html5lib
    '''html5lib是一个纯Python库，用于分析HTML。它被设计成符合whatwg HTML规范，所有主要的Web浏览器都是这样实现的。
    能让浏览器实现非完整性标签的自动补充
    
    '''
    soup=BeautifulSoup(text,'html5lib')
    conMidtab=soup.find('div',class_='conMidtab')
    tables=conMidtab.find_all('table')
    for table in tables:
        trs=table.find_all('tr')[2:]
        for index,tr in enumerate(trs):
            tds=tr.find_all('td')
            city_td=tds[0]
            if index==0:
                city_td = tds[1]
            city=list(city_td.stripped_strings)[0]
            temp_td=tds[-2]
            min_temp=list(temp_td.stripped_strings)[0]
            ALL_DATA.append({'city':city,'min_temp':int(min_temp)})
            #print({'city':city,'min_temp':min_temp})




def main():
    urls=[
    'http://www.weather.com.cn/textFC/hb.shtml',
    'http://www.weather.com.cn/textFC/db.shtml',
    'http://www.weather.com.cn/textFC/hd.shtml',
    'http://www.weather.com.cn/textFC/hz.shtml',
    'http://www.weather.com.cn/textFC/hn.shtml',
    'http://www.weather.com.cn/textFC/xb.shtml',
    'http://www.weather.com.cn/textFC/xn.shtml',
    'http://www.weather.com.cn/textFC/gat.shtml']
    for url in urls:
        parse_page(url)

    #分析数据
    #根据最低气温排序
    ALL_DATA.sort(key=lambda data:data['min_temp'])

    data=ALL_DATA[0:10]
    cities=list(map(lambda x:x['city'],data))
    temps = list(map(lambda x: x['min_temp'], data))
    #pyecharts  pip install pyecharts
    chart=Bar("中国天气最低气温排行榜")
    chart.add('',cities,temps)
    chart.render('temperature.html')


if __name__ == '__main__':
    main()



