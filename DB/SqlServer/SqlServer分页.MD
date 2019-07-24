## SqlServer 三种分页方式

1. 使用top分页
```
DECLARE @PageIndex INT=2, @PageSize INT=10;
SELECT 
TOP(@PageSize)a 
FROM t1
WHERE a NOT IN(SELECT TOP(@PageSize *(@PageIndex-1))a FROM t1 ORDER BY a) 
ORDER BY a;
```

2. 使用ROW_NUMBER()分页
```
DECLARE @PageIndex INT=2, @PageSize INT=10;
SELECT 
a 
FROM (SELECT ROW_NUMBER() OVER (ORDER BY a) AS rn, a FROM t1) temp 
WHERE rn BETWEEN (((@PageIndex-1)* @PageSize)+1) AND (@PageIndex * @PageSize);
```

3. 使用ORDER BY分页
```
DECLARE @PageIndex INT=2, @PageSize INT=10;
SELECT 
a 
FROM t1 
ORDER BY a OFFSET (@PageIndex-1)*@PageSize ROWS FETCH NEXT @PageSize ROWS ONLY;
```