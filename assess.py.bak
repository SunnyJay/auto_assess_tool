#! -*-coding:utf-8 -*-
"""
Created on 2018年1月18日
自动评价
@author:SUN
"""
import urllib2
import urllib
import cookielib
import re
import datetime
import random
import time
import logging
from collections import Counter

tel_no_list = []

def get_cookie(url):
    """
    获取cookie信息
    :param url: 二维码请求地址
    :return: cookie1 cookie2 手机号码
    """
    send_headers = {
        'Host':'2i.wo-xa.com',
        'User-Agent':'Mozilla/5.0 (Linux; U; Android 8.0.0; zh-cn; ALP-AL00 Build/HUAWEIALP-AL00) AppleWebKit/537.36 (KHTML, like Gecko)Version/4.0 Chrome/57.0.2987.132 MQQBrowser/8.1 Mobile Safari/537.36',
        'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,image/sharpp,image/apng,*/*;q=0.8',
        'Connection':'keep-alive',
        'Cache-Control':'max-age=0',
        'Accept-Encoding': 'gzip, deflate',
        'Accept-Language': 'zh-CN,zh-CN;q=0.8,en-US;q=0.6'
    }

    cookie = cookielib.LWPCookieJar()
    opener=urllib2.build_opener(urllib2.HTTPCookieProcessor(cookie))
    request = urllib2.Request(url, headers=send_headers)
    opener.open(request).read()

    cookie_1 = None
    cookie_2 = None
    tel_no = None
    for item in cookie:
        if item.name == 'JSESSIONID':
            cookie_1 = item.value
        elif re.match('^amt_cookie', item.name):
            cookie_2 = item.value
            tel_no = re.split('_', item.name)[2]
            tel_no_list.append(tel_no)
            log('被评价手机号:' + str(tel_no))

    return cookie_1, cookie_2, tel_no


def assess(cookie_1, cookie_2, tel_no, emp_id):
    """
    自动评价
    :param cookie_1: JSESSIONID
    :param cookie_2: amt_cookie
    :param tel_no: 手机号码
    """
    send_headers = {
        'Host': '2i.wo-xa.com',
        'User-Agent': 'Mozilla/5.0 (Linux; U; Android 8.0.0; zh-cn; ALP-AL00 Build/HUAWEIALP-AL00) AppleWebKit/537.36 (KHTML, like Gecko)Version/4.0 Chrome/57.0.2987.132 MQQBrowser/8.1 Mobile Safari/537.36',
        'Accept': '*/*',
        'Origin': 'http://2i.wo-xa.com',
        'Connection': 'keep-alive',
        'X-Requested-With': 'XMLHttpRequest',
        'Referer': 'http://2i.wo-xa.com/qtoa/share/amt/assessPaper_index.shtml?emp_id=' + emp_id,
        'Cache-Control': 'max-age=0',
        'Accept-Encoding': 'gzip, deflate',
        'Accept-Language': 'zh-CN,zh-CN;q=0.8,en-US;q=0.6',
        'Cookie': 'JSESSIONID=' + cookie_1 + ';amt_cookie_' + tel_no + '=' + cookie_2,
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
    }

    data = {
        "score": "5",
        "score_1": "5",
        "words_leave": "",
        "assessPaper_id": "1",
        "emp_id": "" + emp_id,
        "address": "%E9%99%95%E8%A5%BF%E7%9C%81%2C+%E8%A5%BF%E5%AE%89%E5%B8%82%2C+%E9%9B%81%E5%A1%94%E5%8C%BA%2C+%E5%B0%8F%E5%AF%A8%E4%B8%9C%E8%B7%AF%2C+159%E5%8F%B7",
        "location_x": "108.95739209462",
        "location_y": "34.229197749773"
    }

    data = urllib.urlencode(data)
    url = "http://2i.wo-xa.com/qtoa/share/amt/add_assessPaper.shtml"
    request = urllib2.Request(url=url, data=data, headers=send_headers)
    response = urllib2.urlopen(request)
    result = response.read()
    status = re.search("\d", result).group(0)
    if str(status) == "0":
        log("结果: 评价成功")
    else:
        log("结果: 评价失败 status: " + str(status))


def time2seconds(t):
    h,m,s = t.strip().split(":")
    return int(h) * 3600 + int(m) * 60 + int(s)

def seconds2time(sec):
    m,s = divmod(sec,60)
    h,m = divmod(m,60)
    return "%02d:%02d" % (h,m)

def get_time_list():
    st = "08:00:00"
    et = "19:15:33"
    sts = time2seconds(st) #sts==27000
    ets = time2seconds(et) #ets==34233
    #print sts,ets
    rt = random.sample(range(sts, ets), random.randint(80, 120))
    rt.sort()
    # for r in rt:
    #   print(seconds2time(r))
    return rt

def run_task():
    qrcode_url_list = [
"http://2i.wo-xa.com/qtoa/share/amt/assessPaper_index.shtml?emp_id=02d48a385f22ea93015f8ed5536a1c36",
"http://2i.wo-xa.com/qtoa/share/amt/assessPaper_index.shtml?emp_id=02d48a385f22ea93015f8ed685df1c38",
"http://2i.wo-xa.com/qtoa/share/amt/assessPaper_index.shtml?emp_id=02d48a385f22ea93015f8ed0dcb71c30",
"http://2i.wo-xa.com/qtoa/share/amt/assessPaper_index.shtml?emp_id=02d48a385f22ea93015f5777db1e0833",
"http://2i.wo-xa.com/qtoa/share/amt/assessPaper_index.shtml?emp_id=02d48a385f22ea93015f8ed5c8aa1c37",
"http://2i.wo-xa.com/qtoa/share/amt/assessPaper_index.shtml?emp_id=02d48a385f22ea93015f8ed9b4341c3f"
    ]

    # 随机挑选一个进行评价
    for url in random.sample(qrcode_url_list, 1):
        cookie1, cookie2, tel_no = get_cookie(url)
        emp_id = re.split("=", url)[1]
        assess(cookie1, cookie2, tel_no, emp_id)

def init_logging():
    logging.basicConfig(level=logging.DEBUG,
                        format='%(message)s',
                        filename='mylog.log')
    console = logging.StreamHandler()
    console.setLevel(logging.INFO)
    formatter = logging.Formatter('%(message)s')
    console.setFormatter(formatter)
    logging.getLogger('').addHandler(console)

def log(message):
    #print(message)
    logging.info(message)

def start():
    while True:
        log('******************************************************')
        log("日期:" + datetime.datetime.now().strftime('%Y-%m-%d'))
        time_list = get_time_list()
        log('预期评价次数:' + str(len(time_list)))
        log('预期评价时间:')
        time_str = ""
        for item in time_list:
            time_str += seconds2time(item) + " "
        log(time_str)

        assess_num = 0
        while True:
            now = datetime.datetime.now()

            # 超过零点, 则汇总，并开始新的时间序列
            if now.strftime("%H:%M") == "00:00":
                # 汇总
                global tel_no_list
                log("==========================================")
                log("评价次数：")
                for k, v in Counter(tel_no_list).items():
                    log(k + ":" + str(v) + "次")
                tel_no_list = []
                time.sleep(60)
                break

            for item in time_list:
                if now.strftime("%H:%M") == seconds2time(item):
                    assess_num = assess_num + 1
                    log("第" + str(assess_num) + "次执行评价 " + "时间:" + now.strftime("%H:%M"))
                    run_task()
                    break
            time.sleep(60)



if __name__ == "__main__":
    init_logging()
    #run_task()
    start()

