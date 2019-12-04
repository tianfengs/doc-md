---
typora-root-url: pics
typora-copy-images-to: pics
---

> 任务调度第三方框架
>
>  [Quartz.NET](https://www.quartz-scheduler.net/) - Version Migration Guide
>
> https://www.quartz-scheduler.net/documentation/quartz-3.x/migration-guide.html 

本笔记摘自官网教程：

 https://www.quartz-scheduler.net/documentation/index.html 

# Quartz.NET- Documentation

### Lesson 1: Using Quartz

一个例子

```c#
using System;
using System.Threading.Tasks;

namespace QuartzSampleApp
{
    using Quartz;
    using Quartz.Impl;
    using Quartz.Logging;
    using System.Collections.Specialized;

    class QuartzSampleApp
    {
        static void Main(string[] args)
        {
            // 
            LogProvider.SetCurrentLogProvider(new ConsoleLogProvider());

            // trigger async evaluation
            RunProgram().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to close the application");
            Console.ReadKey();
        }

        private static async Task RunProgram()
        {
            try
            {
                // Grab the Scheduler instance from the Factory 从工厂获得调度实例
                NameValueCollection props = new NameValueCollection
                {
                    { "quartz.serializer.type", "binary" }
                };
                StdSchedulerFactory factory = new StdSchedulerFactory(props);
                IScheduler scheduler = await factory.GetScheduler();

                // and start it off 开始调度
                await scheduler.Start();

                // define the job and tie it to our HelloJob class
                // 定义一个任务，并绑定到HelloJob类上
                IJobDetail job = JobBuilder.Create<HelloJob>()
                    .WithIdentity("job1", "group1")
                    .Build();

                // Trigger the job to run now, and then repeat every 10 seconds
                // 创建一个触发器，每10秒钟调度一次任务
                ITrigger trigger = TriggerBuilder.Create()
                    .WithIdentity("trigger1", "group1")
                    .StartNow()
                    .WithSimpleSchedule(x => x
                        .WithIntervalInSeconds(10)
                        .RepeatForever())
                    .Build();

                // Tell quartz to schedule the job using our trigger
                // 告诉Quartz使用触发器调度任务实例
                await scheduler.ScheduleJob(job, trigger);

                // some sleep to show what's happening Sleep一下以显示
                await Task.Delay(TimeSpan.FromSeconds(60));

                // and last shut down the scheduler when you are ready to close your program 关闭调度
                await scheduler.Shutdown();
            }
            catch (SchedulerException se)
            {
                await Console.Error.WriteLineAsync(se.ToString());
            }
        }
        
        ///////////////////////////////////////////////////////
        // simple log provider to get something to the console
        private class ConsoleLogProvider : ILogProvider
        {
            public Logger GetLogger(string name)
            {
                return (level, func, exception, parameters) =>
                {
                    if (level >= LogLevel.Info && func != null)
                    {
                        Console.WriteLine("[" + DateTime.Now.ToLongTimeString() + "] [" + level + "] " + func(), parameters);
                    }
                    return true;
                };
            }

            public IDisposable OpenNestedContext(string message)
            {
                throw new NotImplementedException();
            }

            public IDisposable OpenMappedContext(string key, string value)
            {
                throw new NotImplementedException();
            }
        }
    }

    /// <summary>
    /// 创建一个任务，来测试
    /// </summary>
    public class HelloJob : IJob
    {
        public async Task Execute(IJobExecutionContext context)
        {
            await Console.Out.WriteLineAsync("Greetings from HelloJob!");
        }
    }
}

```

### Lesson 2: Jobs And Triggers 任务和触发器

#### API 接口

最重要的API接口和类是：

- **IScheduler** - the main API for interacting with the scheduler. 

  和调度程序交互的主API。

- **IJob** - an interface to be implemented by components that you wish to have executed by the scheduler.

  实现调度任务实际运行的代码，一定要实现`public async Task Execute(IJobExecutionContext context)`方法。

- **IJobDetail** - used to define instances of Jobs.

  定义任务实例，并用JobBuilder的静态方法实现。

- **ITrigger** - a component that defines the schedule upon which a given Job will be executed.

  定义调度组件触发规则实例，用TriggerBuilder的静态方法实现，在其上运行指定的任务。

- **JobBuilder** - used to define/build JobDetail instances, which define instances of Jobs.

  定义或建立JobDetail实例，即任务实例。

- **TriggerBuilder** - used to define/build Trigger instances.

  定义或建立Trigger实例。

Scheduler调度的生命期包括创建（通过ScheduleFactory）、

