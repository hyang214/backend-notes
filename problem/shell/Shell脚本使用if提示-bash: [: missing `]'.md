## Shell脚本使用if提示-bash: [: missing `]'

+ 时间：2020.05.20

+ 场景：
	+ 修改启动脚本加入APM监控，但是对于生产环境，不希望启用，所以根据环境变量做了判断
		```
		APM_OPTS=""
    if [ $ENV != "PRO"]; then
       APM_OPTS="-javaagent:/data/apmAgent/skywalking-agent.jar -Dskywalking.agent.service_name=$APP_ID"
    fi
    echo -e "apm_ops $APM_OPTS \c"
		```
	+ 使用的if语句报错，返回如下错误
		```
		[: missing `]'
		```

+ 原因：
	
+ 经过排查，发现是语法问题，在linux的判断符号[]中，必须注意中括号的两端要有空格符来分隔
	
+ 解决：
	+ 在 "PRO" 与 ] 之间补上一个空格
	```
		APM_OPTS=""
    if [ $ENV != "PRO" ]; then
       APM_OPTS="-javaagent:/data/apmAgent/skywalking-agent.jar -Dskywalking.agent.service_name=$APP_ID"
    fi
    echo -e "apm_ops $APM_OPTS \c"
	```
	
+ Reference: [Linux使用[ ]判断的时候报错-bash: [: missing `]'](https://blog.csdn.net/chenmoshashou2/article/details/80648739)
