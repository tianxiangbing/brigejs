# 接口文档
## 打开webview的协议,app中使用（内部webview）
### 1.直接打开url
	native://openurl?data={url:website,token:1,cookie:1,param:string}
*会直接把开参数url，并把param的参数加在url后，结果变成`website?param=string`,token和cookie默认为空*

1. `url`:需要打开的url地址		[*必填*]
2. `token`:是否传递token信息		[*选填1为传递，默认不传*]
2. `appid`:传递token时必需传appid
3. `cookie`：是否写入cookie		[*选填,1为写入，默认不写*]
4. `param`:添加的参数信息			[*选填的键值对字符串*]

### 2.先打开含webview的应用，唤醒app应用。
	native://openapp?data={appid:1,param:string}
1. `appid`:客户端根据appid从本地获取应用的url，通过唤醒应用的方式打开应用	[*必填*]
2. `param`:添加到url后的参数	[*选填的键值对字符串*]

## 以下为h5中调用app的接口
`native://method?data={}&callback=callbackname`	

**data参数都进行UrlEncode加码成字符串**

### 1.获取当前客户端版本的版本号。
	native://getversion?callback=getversion
1. `callback`回调方法 [*必填*]
	
返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data: {version:"1.0.0"}
	}

*回调getversion(version)的参数就是返回值*
### 2.获取当前客户端的支持的所有协议接口
	native://getallmethod?callback=getallmethod
	
返回的`data`格式应为:

	{
		code: 200,
		msg: "执行成功",
		data: ["methodname1","methodname2","methodname3"...]
	}

*getallmethod([m1,m2..])参数为方法名的集合*
### 3.判断当前客户端是否支持某个协议方法
	native://checkmethod?data={method:m1}&callback=checkmethod

1. `method`参数为方法名称 [*必填*]
2. `callback`返回bool

返回的`data`格式应为:

	{
		code: 200,
		msg: "执行成功",
		data: true
	}

### 4.分享组件
	native://outershare?data={type:1,title:标题,desc:描述,link:分享地址,pic:图片}&callback=checkmethod
1. `type`:1为微信朋友圈分享，2为发送给微信朋友，3为分享到微博，4为发给QQ好友 [必填项1,2,3,4]
2. `title`:标题 [*选填，为空时取当前窗口的title*]
3. `desc`:描述	[*选填*]
4. `link`:链接	[*选填，为空时取当前url*]
5. `pic`:图片地址	[*选填，为空时给默认图片*]
6. `callback`:回调	[*选填*]

返回的data格式应为:

	{
		code: 200,
		msg: "分享成功",
		data: []
	}

### 4.1.短信分享组件(先选人)
	native://msgshare?data={filter:unactived,desc:描述}
1. `filter`:"all".全部,"unactived".未激活
2. `desc`:内容 [*选填，为空时取当前窗口的内容*]

### 5.用户选择组件（待补充）
	native://selectmembers?data={multi:1,hasme:1,orgid:1001,selected:[{id:1001},{id:1002}]}&callback=selectmembers
1. `multi`:是否多选，1是多选，无或非1为单选		[必填]
2. `hasme`:1是包含自己，0是不包含自己	[选填]
3. `selected`:已选
4. `orgid`:企业ID，不传为全部企业
5. `callback`:[*必填*]

返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data: [{
			id:1001,
			name:"李三"
		}]
	}

### 6.获取企业列表
	native://getorglist?callback=getorglist
返回的data格式应为:

	{
		code: 200,
		msg: "分享成功",
		data: [
			...
		]
	}

### 7.获取当前地理位置
	native://getlocation?callback=getlocation
1. `callback`返回经纬度.	[*必填*]

返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data: [
			291.25,
			286.21
		]
	}

### 8.H5打点到友盟 通过客户端完成
	native://pagestat?data={eventId:"checkin", param:"123"}
1. `eventId`  统计事件ID，在友盟中定义.  [*必选*]
1. `param` 在友盟里的一个跟踪参数.		[*选填*]

### 9.获取用户手机类型（移动联动电信）
	native://getoperator

返回的data格式应为:

	{
	    code: 200,
	    msg: "执行成功",
	    data: {
	    service :？,
	    code:"571"
    }
   }

---------- 

## 外部应用调用
### 1.外部页面分享到彩云或麻绳，唤起选聊天对象界面
	ccoa://outershare?data={title:标题,desc:描述,link:分享地址,pic:图片,from:手厅,icon:手厅图标,schemas:schemadefine}
1. 彩云用 ccoa://    麻绳用 masheng://  	以后的再说
2. `title`:标题	（必选）
3. `desc`:描述		（必选）
4. `link`:链接		（必选）
5. `pic`:图片地址	不填不展示
6. `from`:消息框左下角的小字，不填的时候不展示
7. `icon`:消息框左下角的小图标，不填的时候不展示
8. `schemas`:外部schema，返回到源客户端时候用

----------

## 返回callback的通用格式

	｛
		data:object,	//{}||[]
		code:200,
		msg:"成功"
	｝

Native调用example:

	window.JSBridge.onSuccess('token_1439804657177',{
		code: 200,
		msg: "执行成功",
		data: [
			{name: "秋知", tel: 15889936061},
			{name: "移动客服", tel: 10086}
		]
	});

1. `data`:返回的值，为json对象或数组
2. `code`：状态码,200为成功,其他待定
3. `msg`：返回信息


----------
----------

### alert提示框 
	native://alert?data={title:"标题",msg:"提示信息"}

1. `title`：标题	*选填* 
2. `msg`：提示信息	*必填*

### confirm确认框
	native://confirm?data={title:"标题",msg:"提示信息"}&callback=confirm
1. `title`：标题	*必填*
2. `msg`：提示信息	*必填*
3. `callback`：点击确认后的回调	*必填*
	
返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:"ok"
	}

### prompt 输入提示框
	native://prompt?data={title:"标题",msg:"提示信息",placeholder:"请输入文字"}&callback=prompt
1. `title`：标题	*必填*
2. `msg`：提示信息	*选填*
3. `value`:默认文本	*选填*
4. `placeholder`:文本框提示文本	*选填*
3. `callback`：点击确认后的回调	*必填*

	
返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:"输入的内容"
	}

### toast 小提示弹窗

	native://toast?data={time:3,msg:"提示信息"}

1. `time`：展示时间 *选填，默认2s*
2. `msg`:提示消息 ,支持多行显示 \n *必填*

### actionsheet 操作多选

	native://actionsheet?data={title:"请选择",list:[{text:选项一,value:"001"}]}&callback=actionsheet
1. `title`:标题 *选填*
1. `list`:选项数组 ,键值对 *必填*
2. `callback`:回调,返回选中项

	
返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:{text:选项一,value:"001"}
	}

### modal 弹窗

	native://modal?data={title:"标题",msg:"内容",img:"url",link:"url"}

1. `title`:标题	
2. `msg`：内容
3. `img`:图片地址
4. `link`:点击了解更多跳转地址

### 分享share
	native://share?data={title:标题,desc:描述,link:分享地址,pic:图片}&callback=share
2. `title`:标题	（必选）
3. `desc`:描述		（必选）
4. `link`:链接		（必选）
5. `pic`:图片地址	不填不展示
6. `callback`:分享点击确定或取消后的回调
	
返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:"ok"//ok|cancel 点了确定或取消
	}

### 图片选择selectpic
	native://selectpic?data={max:5}&callback=selectpic
1. `max`:当前可以选择的最大数量，重复选择不作累计 *必填*
1. `callback` ：选择图片后的回调,返回图片的base64

返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:{localIds:[base64,base64]}
	}

### 日期选择selectdate
	native://selectdate?data={format:"yyyy-MM-dd"}&callback=selectdate
1. `format`:返回格式,`yyyy-MM-dd` 或 `yyyy-MM-dd HH:mm`
1. `callback`:选择日期后的回调

返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:'2016-01-06'
	}

### 选取组件 picker

	native://picker?data={title:"投标类型",list:[{text:选项一,value:"001"}]}&callback=picker
1. `title`:标题  *必填*
1. `list`:选项数组 ,键值对  *必填*
2. `callback`:回调,返回选中项
	
返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:{text:选项一,value:"001"}
	}


### 会话
	native://chat?data={type:1,id:"1",name:"xxx"}
1. `type`:会话类型(1)单人，(2)群会话
2. `id`:id
3. `name`:显示的名称

### 选择企业
	native://selectcorp?callback=selectcorp
1. `callback`：选择企业后的回调

返回的data格式应为:

	{
		code: 200,
		msg: "执行成功",
		data:{orgId:1002,orgName:"讯盟"}
	}

### 拍照,直接拍照，不能从相册获取也不能将拍照后的照片保存

	native://photo?&callback=photo
1. `callback`:返回base64

### findfriend 查找好友

	native://findfriend?data={multi:false,selected:[{id:1001},{id:1002}],max:1}&callback=findfriend

1. `multi`:是否多选,默认单选
2. `max`:最多选择,单选时忽略
3. `selected`:已选

callback:

	{
	    code: 200,
	    msg: "执行成功",
	    data: [{
	        id:1001,
	        name:"李三"
	    }]
	}

