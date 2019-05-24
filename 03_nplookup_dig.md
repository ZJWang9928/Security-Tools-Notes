# 被动信息收集

"开源智能" (open source) OSINT
* 都是公开渠道可获得的信息
* 与目标系统不产生直接交互
* 尽量避免留下一切痕迹

Passive reconnaissance(no direct interaction) -> Normal interation -> Active reconnaissance  

**信息收集内容:**
* IP地址段  
* 域名信息  
* 邮件地址  
* 文档图片数据  
* 公司地址  
* 公司组织架构  
* 联系电话/传真号码  
* 人员姓名/职务  
* 目标系统使用的技术架构  
* 公开的商业信息  

**信息用途:**
* 用信息描述目标  
* 发现  
* 社会工程学攻击  
* 物理缺口探测与渗透  

**信息收集——DNS**    
**域名解析成IP地址**    
域名: sina.com     
完全限定域名(FQDN): www.sina.com(sina.com域名下的一条主机记录)      
	  
域名记录:   
* A 主机记录，把域名解析到IP地址  
* C name 别名记录，把域名解析到另一个域名  
* NS 域名服务器地址记录  
* MX 邮件交换记录，邮件地址，SMTP地址  
* ptr 反向解析记录，通过IP地址反向解析域名  
* spf 反向解析，用于反垃圾邮件，对收到的邮件的来源IP地址做spf查询，若查询到的域名和IP地址不匹配，则判断为伪造域名的虚假邮件，拒收  
			  
www.sina.com.  
缓存DNS服务器，本身不保存任何域名解析记录，接收到请求后发给根域服务器(.)，返回.com.域的域名服务器地址，再向.com服务器发送请求，返回sina.com.的域名服务器地址，再发起访问，返回www.sina.com.对应的IP地址，缓存DNS服务器拿到后保存一份，保存一个TTL的时间，返回给客户端，客户端再用IP地址进行访问   
      
DNS客户端与DNS服务器之间——递归查询  
DNS服务器与各个域名服务器之间——迭代查询  

*manjaro(archlinux)下，ifconfig、route在net-tools中，nslookup、dig在dnsutils中，ftp、telnet等在inetutils中，ip命令在iproute2中*  

### 1.nslookup  
	nslookup www.sina.com  

	> server 202.106.0.20 设置使用指定的域名服务器  
	智能DNS：终端用户所处网络不同，DNS返回的查询结果可能是不一样的,让访问流量尽量发生在本地网络  

	> set type=mx 设置查对应的mx记录  
	或  
	> set q=mx/a/ns/any  

	一个域名可以解析成多个IP记录、cname记录  

	nslookup -q=any 163.com [114.114.114.114(指定server)]  
### 2.dig —— DNS信息收集  
	dig @8.8.8.8 sina.com mx  
	dig sina.com any @8.8.8.8  
	dig sina.com any  
	真实使用时可使用多个server多查询几次  
	注：加了any也只查sina.com，不查www.sina.com等  

	输出信息筛选：  
	dig +noall sina.com any 什么都不显示  
	dig +noall +answer sina.com any 只看answer  
	dig +noall +answer sina.com any | awk '{print$5}'  

	反向解析：  
	dig +notall +answer -x 220.181.14.157  

	查一个DNS服务器所使用的bind版本信息：  
	dig +notall +answer txt chaos VERSION.BIND @ns3.dnsv4.com  
	bind对应chaos类  
	若BIND版本较老或存在漏洞，就有机会把DNS服务器中所有记录获取出来  

	DNS追踪：  
	dig +trace example.com  
