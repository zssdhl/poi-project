# poi-project
## 功能介绍  
* 任务计划  
支持定义多任务，可单独定义每个任务的抓取方式、抓取地图、抓取区域、抓取周期。  
* 失败重试  
由于发现地图返回poi信息为无序的，因此对失败任务保存断点进行恢复可能造成较大的信息丢失。  
因此，在本系统中对失败的任务，会自动在下个时段进行完整的重试，无需人工干预  
* 多模式*多地图  
本系统目前支持（网格划分、行政划分）*（百度、腾讯、高德），且具备良好扩展性  
	* 网格划分：通过经纬度构成的多边形进行划分，缺点是：抓取速度慢，百度地图对某些矩阵返回信息不完整。高德地图对无poi区域返回难以识别。  
	* 行政划分：通过实时获取行政子区域的方式对地区进行划分，缺点是：目前地图支持最小粒度为区，若某个区里的poi信息超过限制，则会造成丢失。百度目前不支持此模式（地区编码不一致）  

## 项目介绍  
基于需求和前期的尝试以后，不再使用简单的脚本形式，我对系统架构进行了重构。    
目前代码具有良好的结构，以及充分的注释。非常有利于项目维护和扩展。     
### 依赖  
项目基于python3进行开发，依赖的第三方包包括：pyshp、Shapely、pypinyin    
定义好task以后，通过python3 Runner.py运行即可  

### 目录结构：   

* Runner.py 系统入口，主要负责任务调度  
* log.txt 日志文件，所有日志将输出到该文件，方便问题排查  
* task.txt 任务文件，可制定多个周期性任务  
* adcode.txt 中国区级及以上地区的编码，在指定行政区域查询时使用  
* res 结果文件夹，每个任务的执行结果将存储在该文件夹下  
* GADM 国际规范的描述行政区域多边形信息文件，若有需要从http://www.gadm.org/下载更新并替换即可  
* zing 系统代码目录  
	* pTask 任务包，目前支持按网格划分的任务和按行政区域划分的任务，若后续有需要可通过继承BaseTask进行快速扩展  
	* MapDi 地图方言包，目前支持百度、腾讯、高德地图，若有序有需要可通过继承BaseMap对地图进行快速扩展  
	* Util 工具包  
	
### task.txt  
使用#进行注释，每行为一个任务，对任务的描述用空格分隔，不要有多余的空行，行末不要有多余的空格  
列1：地区划分方式 0、1 0表示网格划分 1表示行政划分  
列2：地图 百度、腾讯、高德  百度不支持行政划分（编码不一致）、腾讯若区级包含poi>2200，高德>1000不建议使用行政划分   
列3：范围类型 0、1、2 0全国 1省 2市  
列4：范围 与列3对应，若为网格划分，格式为：中国、广东、广东@广州（不要有省和市）；若为行政划分，请查adcode.txt查询，格式为：440000 （支持到区，无需和列3对应）  
列5：抓取关键字 如：村、大学  
列6：抓取周期 单位为天 如：5  
列7：任务开始日期 格式为YYYYMMDD 如：20160618  可缺省，表示今天   

eg：  
0 高德 2 广东@汕头 村 1 20160618  
0 百度 1 广东 大学 5  
1 腾讯 1 440000 村 3  