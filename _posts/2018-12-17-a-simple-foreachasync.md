---
title: A Simple ForeachAsync
date: 2018-12-17
author: nhvu1988
category: Coding
tags: [parallel,async,dotnet]
---

It's just the codes from [Implementing a simple ForEachAsync, part 2](https://blogs.msdn.microsoft.com/pfxteam/2012/03/05/implementing-a-simple-foreachasync-part-2/) but I will write it as simple as it should be

## Code

```csharp
public static class ParallelHelper
{
    public static Task ForEachAsync<T>(this IEnumerable<T> source, int maxConcurrent, Func<T, Task> body)
    {
        return Task.WhenAll(
            from partition in Partitioner.Create(source).GetPartitions(maxConcurrent)
            select Task.Run(async delegate {
                using (partition)
                    while (partition.MoveNext())
                        await body(partition.Current);
            }));
    }
}
```

## How to use

```csharp
string[] urls = new []
{
    "https://google.com",
    "https://bing.com",
    "https://yahoo.com",
};

await ParallelHelper.ForEachAsync(urls, 5, async (url) =>
{
    WebClient client = new WebClient();
    await client.DownloadStringTaskAsync(url);
});
```