# -*- coding: utf-8 -*-
import scrapy
import time
import requests
import re
from twopoint.items import TwopointItem

class TwopSpider(scrapy.Spider):
    name = 'twop'
    #allowed_domains = ['http://www.twopoint.de']
    start_urls = ['http://www.twopoint.de/pr.jsp?_pp=0_548_27_-1']
    now_data = time.strftime("%Y-%m-%d")
    path=f'{now_data}日爬取的数据.txt'


    def parse(self, response):
    	for i in range(27,103):
    		url = f'http://www.twopoint.de/pr.jsp?_pp=0_548_{i}_-1'
    		yield response.follow(url=url,callback=self.parse_down)


    def parse_down(self, response):
    	
    	name = response.xpath('//table[@class="propDiv productName      "]').xpath('.//a/text()').extract()
    	price = response.xpath('//table[@class="propDiv productProp11    "]').xpath('.//td/span[2]/text()').extract()
    	weight = response.xpath('//table[@class="propDiv productProp16    "]').xpath('.//td/span[2]/text()').extract()
    	product_id = response.xpath('//div[@class="productListForms"]/div/@productid').extract()
    	#信息写入过度文件
    	with open(self.path,"a",encoding='utf-8') as f:
    		for i in range(len(name)):
    			f.write(name[i]+","+price[i].strip('￥')+","+weight[i]+","+product_id[i]+","+'D://twopoint_img/'+product_id[i]+'.jpg'+"\n")

    	#转向商品页面，获取品牌和规格
    	p_url=response.xpath('//table[@class="propDiv productName      "]').xpath('.//a/@href').extract()
    	for each in p_url:
    		pn_url='http://www.twopoint.de/'+each
    		yield response.follow(url=pn_url,callback=self.production_down)

    	#保存图片
    	image = response.xpath('//div[@class="imgDiv   "]').xpath('.//img/@src').extract()
    	for i in range(len(image)):
    		img_path=f'D:/PY/twopoint/img/{product_id[i]}.jpg'
    		with open (img_path,'wb')as f: 
    			req=requests.get(image[i])
    			f.write(req.content)

    	#下一页
    	try:
    		count = response.xpath('//div[@class="pageNext"]/a/@href').extract_first()
    		if count:
    			next_url='http://www.twopoint.de'+count
    			yield response.follow(url=next_url,callback=self.parse_down)
    	except:
    		pass

    def production_down(self, response):
    	#有的商品没有品牌和规格。
    	product_id2 = re.findall(r'id=(\d+)',response.url)[0]
    	if response.xpath('//td[@title="规格"]').xpath('../td[2]/span/text()').extract_first():
    		specification = response.xpath('//td[@title="规格"]').xpath('../td[2]/span/text()').extract_first()
    	else :
    		specification='0'
    	if response.xpath('//td[@title="品牌"]').xpath('../td[2]/span/text()').extract_first():
    		brand = response.xpath('//td[@title="品牌"]').xpath('../td[2]/span/text()').extract_first()
    	else:
    		brand = '0'
    	try:
    		#存入过度文件
    		with open('表二'+self.path,"a",encoding='utf-8') as f:
    			f.write(product_id2+","+specification+","+brand+"\n")
    	except:
    		pass


