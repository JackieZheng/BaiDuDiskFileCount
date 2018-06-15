# BaiDuDiskFileCount
```
/********************
*百度文件数量统计 V2
*2018.06.15
*Jackie
********************/


/************
*百度查询接口
http://pan.baidu.com/api/list?
dir=				//查询目录
&num=100000			//分页大小 最大支持99999999999999 默认1000
&page=1				//页码
&order=time			//排序属性
&desc=1				//排序顺序
&clienttype=0
&showempty=0
************/



var root = "";//指定目录，空取当前目录
var maxPageSize=99999999999999; //最多支持这么多，超过此值报错
var dskApi="https://pan.baidu.com/api/list?&num="+maxPageSize+"&page=1&dir=";
var totalCount = 0;
var startTime = new Date();
var clearTimes=0;
var clearMaxTimes=50;//最大显示行数，超过清一屏
function timeSpan(stime, etime) {
	var usedTime = etime - stime;
	var days = Math.floor(usedTime / (24 * 3600 * 1000));
	var leave1 = usedTime % (24 * 3600 * 1000);
	var hours = Math.floor(leave1 / (3600 * 1000));
	var leave2 = leave1 % (3600 * 1000);
	var minutes = Math.floor(leave2 / (60 * 1000));
	var leave3 = leave2 % (60 * 1000);
	var seconds = Math.round(leave3 / 1000);
	var time ="";
	if(days>0)
	{
		time+=days+"天";
	}
	if(hours>0)
	{
		time+=hours+"小时";
	}
	if(minutes>0)
	{
		time+=minutes+"分钟";
	}
	 
	time+=seconds+"秒";
	 
    return time;  
} 

(function($){
    $.getUrlParam = function (name) {
        var search = document.location.hash;
        var pattern = new RegExp("[?&]" + name + "\=([^&]+)", "g");
        var matcher = pattern.exec(search);
        var items = null;
        if (null != matcher) {
            try {
                items = decodeURIComponent(decodeURIComponent(matcher[1]));
            } catch (e) {
                try {
                    items = decodeURIComponent(matcher[1]);
                } catch (e) {
                    items = matcher[1];
                }
            }
        }
		return items;
    }
})(jQuery);

if(root=="")
{
	root=$.getUrlParam("path");
}

function GetFilesCount(fileLists)
{
	var count=0;
	var fl=0;
    if(fileLists!=undefined&&fileLists.length!=undefined)
    {
        fl=fileLists.length||0;
    }

	for (var index=0; index<fl; index++) {  
             (function(index) {  
				var file=fileLists[index];
				if(file.isdir==0)
				{
					count++;
					totalCount++;
				}
				else if(file.isdir==1)
				{
					count=count+GetDirFilsCount(file.path);
				}
				  
				})(index);  
               
     }  
	return count;
}

function GetDirFilsCount(dirName)
{
	var dfCount=0;
	$.ajax({
		url: dskApi+''+dirName,
		type: 'get',
		async: false,//true 异步，false 同步
		success: function(data) {
			var fileLists = data.list;
			dfCount=GetFilesCount(fileLists);	

			clearTimes++;	
			if(clearTimes==clearMaxTimes)
			{
				console.clear();
				clearTimes=0;
			}
			//console.count();
			console.log("→"+decodeURIComponent(dirName)+"："+dfCount);
			console.log("☋统计目录为：“"+decodeURIComponent(root)+"” 当前计算总数："+totalCount+" 用时：" +timeSpan(startTime, new Date()));
	}
	});
	return dfCount;
}


GetDirFilsCount(root);
```
