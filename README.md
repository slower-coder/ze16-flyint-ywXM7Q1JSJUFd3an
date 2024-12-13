

> 背景: 我曾经在一个自动化测试平台中集成定时任务，基于 APScheduler 库花了好长时间解决重复执行的问题。定时任务集成在服务中也让服务变得复杂。最后，我们选择了公司其他团队go语言开发的一个定时任务服务。于是萌生了想法，和不用 Python实现一个通用的定时任务服务。于是，schedule\-server应运而生。


GitHub地址: [https://github.com/SeldomQA/schedule\-server](https://github.com)


## schedule\-server 特点


* 运行与部署简单。
* 基于HTTP触发请求。
* 支持三种类型的定时任务：`crontab`、`interval`、`date`
* 支持定时任务的`查询`、`删除`、`添加`、`暂停/恢复`等操作。


## schedule\-server 架构图


![](https://img2024.cnblogs.com/blog/311516/202412/311516-20241212181708096-583515978.png)


* `schedule_server`: 核心功能是定时触发HTTP请求。
* `fontend`: 通过前端UI管理定时任务。
* `you server`: 在你的服务中通过调接口的方式管理定时任务。
* `SQLite`: 用于保存定时任务服务。
* `Redis`: 通过Redis锁解决重复触发的问题。


## 安装与运行


**安装依赖**



```
> cd schedule_server
> pip install -r requirements.txt

```

**开发运行**



```
> uvicorn main:app --reload

INFO:     Will watch for changes in these directories: ['/.../schedule-server/schedule_server']
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [21905] using StatReload
INFO:     Started server process [21907]
INFO:     Waiting for application startup.

```

**查看API**


访问URL: [http://127\.0\.0\.1:8000/docs](https://github.com)


![](https://img2024.cnblogs.com/blog/311516/202412/311516-20241212181727478-2013586365.png)


## 前端服务


使用schedule\_server服务可以不需要前端，但是，你想可视化的方法管理定时任务，我还为此专门开发了个前端服务。


**安装依赖**



```
> npm install

```

**运行服务**



```
> npm run dev

> wiremock-ui@0.0.1 dev
> vite
  VITE v4.3.9  ready in 3426 ms
  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help

```

**访问前端页面**


访问url: [http://localhost:5173/](https://github.com)


![](https://img2024.cnblogs.com/blog/311516/202412/311516-20241212181744577-1887446611.png)


## 定时类型


schedule\-server支持三种定时类型：`crontab`、`interval`、`date`，可以满足不同的需求。


**date类型**


data类型比较简单，适合固定的`日期时间`触发定时任务。


* 调用接口


	+ URL：[http://127\.0\.0\.1:8000/scheduler/date/add\_job](https://github.com):[veee加速器](https://youhaochi.com)
	+ Method: `POST`
	+ Type: `JSON`



```
{
  "job_id": "date_job_111",
  "url": "https://httpbin.org/get?id=111",
  "year": 2022,
  "month": 11,
  "day": 18,
  "hour": 7,
  "minute": 0,
  "second": 0
}

```

* 前端配置


![](https://img2024.cnblogs.com/blog/311516/202412/311516-20241212181804035-1367657783.png)


**interval类型**


interval适合间隔时间`重复执行`的定时任务。


* 调用接口


	+ URL：[http://127\.0\.0\.1:8000/scheduler/interval/add\_job](https://github.com)
	+ Method: `POST`
	+ Type: `JSON`



```
{
  "job_id": "interval_job_222",
  "url": "https://httpbin.org/get?id=222",
  "hours": 0,
  "minutes": 0,
  "seconds": 10
}

```

* 前端配置


![](https://img2024.cnblogs.com/blog/311516/202412/311516-20241212181822090-384598806.png)


**cron类型**


conn使一种复杂的定时任务，能够支持所有的定时任务需求。


* 调用接口


	+ URL：[http://127\.0\.0\.1:8000/scheduler/cron/add\_job](https://github.com)
	+ Method: `POST`
	+ Type: `JSON`



```
{
  "job_id": "cron_job_333",
  "url": "https://httpbin.org/get?id=333",
  "second": "0",
  "minute": "*/3",
  "hour": "*",
  "day": "*",
  "month": "*",
  "day_of_week": "*"
}

```


> 可以参考这个网站学习：[https://tooltt.com/crontab\-parse/](https://github.com)


* 前端配置


![](https://img2024.cnblogs.com/blog/311516/202412/311516-20241212181843531-875316050.png)


