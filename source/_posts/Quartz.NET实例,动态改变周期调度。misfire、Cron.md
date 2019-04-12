---
title: Quartz.NET实例,动态改变周期调度。misfire、Cron
date: 2018/11/22 18:46
tags: Quartz
---
 # Quartz.NET实例,动态改变周期调度。misfire、Cron

Quartz：Java编写的开源的任务调度作业框架 类似Timer之类定时执行的功能，但是更强大

Quartz.NET：是把Quartz转成C#  NuGet中可以直接下载对应类库



官网：<https://www.quartz-scheduler.net/>



主要对象:



Job ：工作，要执行的具体内容继承IJob。此接口中只有一个方法：execute(IJobExecutionContext context) 

JobDetail：具体的可执行的调度程序，Job 是这个可执行程调度程序所要执行的内容

Trigger：调度参数的配置，什么时候去调 执行间隔。 

Scheduler：调度容器，一个调度容器中可以注册多个 JobDetail 和 Trigger。当 Trigger 与 JobDetail 组合，就可以被 Scheduler 容器调度了。 

大体介绍：

```
static void Main(string[] args)
        {
            Task<IScheduler> task_scheduler = StdSchedulerFactory.GetDefaultScheduler();
            IScheduler scheduler = task_scheduler.Result;
 
            //ISchedulerFactory _SchedulerFactory = new StdSchedulerFactory();
            //IScheduler scheduler = _SchedulerFactory.GetScheduler().Result;
 
            /*  
             *   过程：
             *      一.通过StdSchedulerFactory计划工厂对象创建调度计划Scheduler
             *      二.创建Job对象，需要继承IJob 实现执行方法
             *      三.触发对象，2种：间隔执行和定时执行
             *          1.ITrigger:间隔执行
             *          2.ICronTrigger:定时执行
             *      四.将job对象和触发对象传入调度计划Scheduler 开始执行
             *  
             * 
             */
            //job创建
            IJobDetail job = JobBuilder.Create<SayHello>().WithIdentity("定时确认完成订单").Build();
 
            ////创建简单计时器
            //ISimpleTrigger _SimpleTrigger = TriggerBuilder.Create()
            //    .WithSimpleSchedule(t => t //声明定时
            //    .WithIntervalInSeconds(2) //5秒一次
            //    .RepeatForever()) //永远执行
            //    .StartNow() //立即开始
            //    .Build() //创建
            //    as ISimpleTrigger;
            ////关联job与计时器
            //scheduler.ScheduleJob(job, _SimpleTrigger);
            /*
             * WithCronSchedule参数说明：
             *  秒 分 时 某一天 月 周 年(可选参数,一般不用)
             *  秒分的合法值为0-59，小时:0-23，日期(天):0-31[要注意不同的月份中的天数不同] 月:0-11[或JAN,FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC] 星期:1-7(1=星期日)或[SUN,MON,TUE,WED,THU,FRI,SAT]    (程序员数数法。。 万事0开始)（ 涉及到星期最好用英文）
             *      通配符{[, - * /]都可以使用、[L #]只能星期课额外使用、[L W]天数可额外使用}
             *          ',' 子条件
             *          '*' 代表任意值的所有 
             *          '-' 代表一个时段 
             *          '/' 代表值的增量 
             *                  比如:分钟域中放入'0/15' 表示【从0开始 每隔15分钟】 '3/20'表示【从第3分钟开始 每隔20分钟】 等同【3,23,43】
             *          '?' 只能用在天数或者星期中 表示【没有指定值,满足所有】 
             *          'L' 只能用在天数或者星期中 表示【这个时段的最后一天】 
             *                  比如：月份中 1月的31号 2月的28或者29号 反正最后一天，或者 4L:该月的最后一个周四 6L:该月的最后一个周四。  
             *                  注意！星期中表示【周六 SAT（手动加粗）】 
             *          'W' 代表离给定日期最近的那个工作日
             *                  比如：天数设置为15W 表示【离本月15号最近的一个工作日】？？
             *          'LW'代表这个月最后一周的工作日 可以在日期使用 ？待测
             *          '#' 代表本月的第几个工作日
             *                  比如：星期中设置6#3 当月的第三个周五（6是周五！）
             * 
             *  过失触发：
             *    withMisfireHandlingInstructionDoNothing--->misfireInstruction = 2
             *     ——不触发立即执行
             *     ——等待下次Cron触发频率到达时刻开始按照Cron频率依次执行
             *
             *    withMisfireHandlingInstructionFireAndProceed--->misfireInstruction = 1
             *     ——以当前时间为触发频率立刻触发一次执行
             *     ——然后按照Cron频率依次执行
             *
             *     withMisfireHandlingInstructionIgnoreMisfires--->misfireInstruction = -1
             *     ——以错过的第一个频率时间立刻开始执行
             *     ——重做错过的所有频率周期后
             *     ——当下一次触发频率发生时间大于当前时间后，再按照正常的Cron频率依次执行
             * 
             * 
             * 
             * 实例：
             *      "0 0/5 * * * ?" 每5分钟
             *      "10 0/5 * * * ?" 这分钟后 10 秒起, 每五分钟
             *      "0 30 10-13 ? * WED,FRI" 每周三、周五的10点到13点的30分时 【每周三, 周五 10:30, 11:30, 12:30 和 13:30】
             *      "0 0/30 8-9 5,20 * ?" 每个月的5号、20号的8点9点之间 每半小时触发一次 因为0开始 所以10点的时候不触发【8:00, 8:30, 9:00  9:30 】
             *      "0 0-5 14 * * ?"  每天的14:00到14:05分每分钟一次
             *      "0 10,44 14 ? 3 WED" 每年三月的周三14:10和14:44
             *      "0 15 10 ? * MON-FRI" 每周三到周五的10::15
             *      "0 15 10 ? * 6#3" 每个月第三个周五的10:15
             * 
             *   生成表达式的网站：http://cron.qqe2.com/?tdsourcetag=s_pctim_aiomsg 小时模块的增量有bug注意下 但大体不影响使用
            */
            ICronTrigger _CronTrigger = TriggerBuilder.Create()
                .WithIdentity("定时确认")
                .WithCronSchedule("0/2 * * * * ?") //秒 分 时 某一天 月 周 年(可选参数)
                .Build()
                as ICronTrigger;
            scheduler.ScheduleJob(job, _CronTrigger);
 
 
            scheduler.Start();
            Console.ReadLine();
        }
    }
 
    public class SayHello : IJob
    {
        public async Task Execute(IJobExecutionContext context)
        {
            Console.WriteLine("Hello Wrold!");
        }
    }
```



 

 



实例：



**1.创建job**

```
public class SayHello : IJob
    {
        public static int ii = 0;
        static string str = "服务1=Cron 5秒一次。Hello World==SayHello  ";
        public async Task Execute(IJobExecutionContext context)
        {
             
            if (ii == 1)
            {
                QuartzManage.ModifyJob(context.JobDetail, context.Trigger as ICronTrigger, "0/2 * * * * ?");
                ii = 0;
                str += ",周期已改变！！变成2秒一次 ";
            }
            Common.WriteLog(str + context.Scheduler.GetHashCode());
        }
    }
 
    public class SayCZ : IJob
    {
        public async Task Execute(IJobExecutionContext context)
        {
            Common.WriteLog("服务2=Simple 3秒一次。Hello CZ==SayCZ  " + context.Scheduler.GetHashCode());
        }
    }
```







**2.Quartz管理类**



```
public class QuartzManage
    {
        static Task<IScheduler> task_scheduler = StdSchedulerFactory.GetDefaultScheduler();
        static IScheduler scheduler;
        private static readonly object objlock = new object();
        static QuartzManage()
        {
            if (scheduler == null)
            {
                lock (objlock)
                {
                    if (scheduler == null)
                        scheduler = task_scheduler.Result;
                }
            }
        }
 
        /// <summary>
        /// 以Simple开始一个工作
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="name"></param>
        /// <param name="simpleInterval"></param>
        public static void StartJobWithSimple<T>(string name, Action<SimpleScheduleBuilder> simpleInterval) where T : IJob
        {
            IJobDetail job = JobBuilder.Create<T>().WithIdentity(name + "_job", name + "_group").Build();
            ITrigger Simple = TriggerBuilder.Create().StartNow()
                               .WithSimpleSchedule(simpleInterval)
                               .Build() as ISimpleTrigger;
 
            scheduler.ScheduleJob(job, Simple);
            scheduler.Start();
        }
 
        /// <summary>
        /// 以Cron开始一个工作
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="name"></param>
        /// <param name="CronExpression"></param>
        public static void StartJobWithCron<T>(string name, string CronExpression) where T : IJob
        {
 
            IJobDetail job = JobBuilder.Create<T>().WithIdentity(name + "_job", name + "_group").Build();
 
            ITrigger CronTrigger = TriggerBuilder.Create().StartNow().WithIdentity(name + "_trigger", name + "_group")
                                    .WithCronSchedule(CronExpression, w => w.WithMisfireHandlingInstructionDoNothing())
                                    .Build() as ICronTrigger;
 
            scheduler.ScheduleJob(job, CronTrigger);
            scheduler.Start();
        }
 
        /// <summary>
        /// Cron修改频率
        /// </summary>
        /// <param name="job"></param>
        /// <param name="trigger"></param>
        /// <param name="CronExpression"></param>
        public static void ModifyJob(IJobDetail job, ICronTrigger trigger, string CronExpression)
        {
            ICronTrigger _ICronTrigger = trigger;
            _ICronTrigger.CronExpressionString = CronExpression;
 
            scheduler.RescheduleJob(trigger.Key, _ICronTrigger);
        }
 
        /// <summary>
        /// Simple修改频率
        /// </summary>
        /// <param name="job"></param>
        /// <param name="trigger"></param>
        /// <param name="SimpleTime"></param>
        public static void ModifyJob(IJobDetail job, ISimpleTrigger trigger, TimeSpan SimpleTime)
        {
            ISimpleTrigger _ISimpleTrigger = trigger;
            _ISimpleTrigger.RepeatInterval = SimpleTime;
 
            scheduler.RescheduleJob(trigger.Key, _ISimpleTrigger);
        }
 
    }
```







**3.执行调度，并改变周期**

```
public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
 
 
        /// <summary>
        /// Cron
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button1_Click(object sender, EventArgs e)
        {
            QuartzManage.StartJobWithCron<SayHello>("first", "0/5 * * * * ?");
        }
 
        /// <summary>
        /// Simple运行
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button2_Click(object sender, EventArgs e)
        {
            QuartzManage.StartJobWithSimple<SayCZ>("second", x => x.WithIntervalInSeconds(3).RepeatForever()); //每三秒 一直执行
        }
 
        private void button3_Click(object sender, EventArgs e)
        {
            SayHello.ii = 1;
        }
    }
```





执行结果：

![img](https://img-blog.csdn.net/20180913134400241?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvbW1hbmRCYWJ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)





改变周期后 会有一个过时触发的问题 根据设置不同的模式 执行的效果不同 **misfire**的概念，第一个代码块中有详细注释说明



 



代码实例: https://download.csdn.net/download/commandbaby/10664266