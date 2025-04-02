# Linq
- https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.aggregate?view=net-9.0

## Aggregate 累计方法
`Aggregate<TSource>(IEnumerable<TSource>, Func<TSource,TSource,TSource>)`
```cs
int[] nums = new int[] { 1, 2, 3, 4, 5 };
int sum = nums.Aggregate<int>((i, j) => { // 一开始i=1, j=2
  return i+j;
});
Console.WriteLine(sum); // 15
```

`Aggregate<TSource,TAccumulate>(IEnumerable<TSource>, TAccumulate, Func<TAccumulate,TSource,TAccumulate>)`
```cs
int[] nums = new int[] { 1, 2, 3, 4, 5 };
int sum = nums.Aggregate<int, int>(5, (i, j) => { // 一开始i=5, j=1
  return i+j;
});
Console.WriteLine(sum); // 20
```

`Aggregate<TSource,TAccumulate,TResult>(IEnumerable<TSource>, TAccumulate, Func<TAccumulate,TSource,TAccumulate>, Func<TAccumulate,TResult>)`
```cs
int[] nums = new int[] { 1, 2, 3, 4, 5 };
var result = nums.Aggregate<int, int, bool>(
5,  // 初始累计值
(i, j) => i+j, // 累计函数
i=>i==20 ? true :false); // 结果选择器

Console.WriteLine(result); // true
```