## 常用的语法

* 删除临时表
```
IF(Object_id('tempdb..#TempResult') IS NOT NULL)
    BEGIN
        DROP TABLE #TempResult  -- 删除临时表
    END
```

* 年月日 变为 日期
> DATEFROMPARTS ( 2019, 7, 1 )   

* 开窗函数 行数
> ROW_NUMBER() OVER (PARTITION BY customerID  ORDER BY totalPrice) AS rows   

* 空字符串和空格字符串判断

1. 
``` 
SELECT DATALENGTH(' ')
```
2. 
```
IF CAST('' AS VARBINARY)=CAST(' ' AS VARBINARY)
    PRINT 'true'
ELSE
    PRINT 'false'
```

* 根据另外一张表的条件 插入或更新, 例 :
```
 MERGE INTO dbo.EvaluateDetail ED
 USING #TempResult TED
 ON ED.EmpNo=TED.EmployeeNo AND ED.Year=TED.TxYear AND ED.Month=TED.TxMonth 
 AND ED.ExecType=@ExecType AND ED.ExecYear=@ExecYear AND ED.ExecMonth=@ExecMonth AND ED.DeleteFlag=0
 WHEN MATCHED THEN UPDATE SET 
 ED.EmpName=TED.EmpName,
 ED.TxFirstDate=TED.TxFirstDate,
 ED.FirstHandPerformance=TED.FirstHandPerformance,
 ED.SecondHandPerformance=TED.SecondHandPerformance,
 ED.SalePerformance=TED.SalePerformance,
 ED.RentPerformance=TED.RentPerformance,
 ED.TotalPerformance=TED.TotalPerformance,
 ED.FirstHandPoint=TED.FirstHandPoint,
 ED.SecondHandPoint=TED.SecondHandPoint,
 ED.SalePoint=TED.SalePoint,
 ED.RentPoint=TED.RentPoint,
 ED.TotalPoint=TED.TotalPoint,
 ED.ExecYear=@ExecYear,
 ED.ExecMonth=@ExecMonth,
 ED.ExecType=@ExecType,
 ED.ExecDate=@ExecDate,
 ED.UpdateDate=GETDATE(),
 ED.UpdateUser='SYSTEM_NEW'
 WHEN NOT MATCHED BY TARGET THEN INSERT 
 (EmpNo,EmpName,Year,Month,TxFirstDate,
 FirstHandPerformance,SecondHandPerformance,SalePerformance,RentPerformance,TotalPerformance,
 FirstHandPoint,SecondHandPoint,SalePoint ,RentPoint,TotalPoint,
 ExecYear,ExecMonth,ExecType,ExecDate,DeleteFlag,CreateDate,CreateUser,UpdateDate,UpdateUser) 
 VALUES 
 (TED.EmployeeNo,TED.EmpName,TED.TxYear,TED.TxMonth,TED.TxFirstDate,
 TED.FirstHandPerformance,TED.SecondHandPerformance,TED.SalePerformance,TED.RentPerformance,TED.TotalPerformance,
 TED.FirstHandPoint,TED.SecondHandPoint,TED.SalePoint,TED.RentPoint,TED.TotalPoint,
 @ExecYear,@ExecMonth,@ExecType,@ExecDate,0,GETDATE(),'SYSTEM',GETDATE(),'SYSTEM'
 );
```
