---
name: quartz-dotnet
description: Quartz.NET scheduling guidance for .NET applications. Use when working with Quartz.NET jobs, triggers, cron schedules, hosted services, dependency injection, persistent job stores, clustering, misfire handling, scheduler configuration, or Quartz API usage.
---

# Quartz.NET

Use this skill for Quartz.NET scheduling work in .NET applications. Default to the project's installed Quartz package version; if the version is unknown, inspect package references before giving version-specific advice.

## Official Sources

- Main docs: https://www.quartz-scheduler.net/documentation
- Quartz 3.x quick start: https://www.quartz-scheduler.net/documentation/quartz-3.x/quick-start.html
- Quartz 3.x tutorial: https://www.quartz-scheduler.net/documentation/quartz-3.x/tutorial/
- Quartz 3.x configuration reference: https://www.quartz-scheduler.net/documentation/quartz-3.x/configuration/reference.html
- Quartz 3.x API docs: https://docs.quartz-scheduler.net/apidoc/3.0
- GitHub: https://github.com/quartznet/quartznet
- NuGet: https://www.nuget.org/packages/Quartz

When exact API behavior matters, check the installed package version and use the matching official docs or XML documentation from the NuGet package.

## Implementation Defaults

- Use `Quartz.Extensions.Hosting` for ASP.NET Core or worker services.
- Use `Quartz.Extensions.DependencyInjection` integration instead of manually constructing jobs.
- Implement jobs with `IJob` and `Task Execute(IJobExecutionContext context)`.
- Pass `context.CancellationToken` into async work where supported.
- Use stable `JobKey` and `TriggerKey` names for jobs that may be rescheduled, queried, or persisted.
- Configure misfire behavior explicitly for time-sensitive schedules.
- Use persistent job stores when schedules or trigger state must survive restarts.
- Set time zones explicitly for cron schedules that represent business-local time.

## ASP.NET Core Pattern

```csharp
builder.Services.AddQuartz(q =>
{
    var jobKey = new JobKey("send-report");

    q.AddJob<SendReportJob>(opts => opts.WithIdentity(jobKey));

    q.AddTrigger(opts => opts
        .ForJob(jobKey)
        .WithIdentity("send-report-trigger")
        .WithCronSchedule("0 0 8 ? * MON-FRI"));
});

builder.Services.AddQuartzHostedService(options =>
{
    options.WaitForJobsToComplete = true;
});
```

## Design Checks

- Is the job idempotent or protected against duplicate execution?
- Should job data be persisted, or should the job query fresh state at execution time?
- What should happen after a missed fire: run immediately, skip, or preserve cadence?
- Does the schedule depend on local time, UTC, or a user/tenant time zone?
- Should the job be durable, recoverable, clustered, or externally triggered?
- Are retries handled by Quartz configuration, job logic, or surrounding infrastructure?

## Common Pitfalls

- Do not use in-memory job store for production schedules that must survive deployment or restart.
- Do not rely on server local time for business schedules unless that is intentional.
- Do not put large mutable objects in `JobDataMap`; store identifiers and load current data inside the job.
- Do not block async work with `.Result` or `.Wait()` inside jobs.
- Do not assume cron expressions include seconds unless using Quartz cron syntax.
- Do not ignore clustering configuration when multiple app instances can run the same scheduler.
