
## SQL语句执行顺序：
1. 第一步：执行FROM
2. 第二步：WHERE条件过滤
3. 第三步：GROUP BY分组
4. 第四步：执行SELECT投影列
5. 第五步：HAVING条件过滤
6. 第六步：执行ORDER BY 排序

## 更新

### 直接更新
* sql、Orcl update 表名 set 列名=新值 where 条件;

### 由一张表更新另一张表
* sql update t1 set t1.D = t2.D from t2 where t1.A = t2.A
* sql update t1 set t1.D = t2.D from t1,t2 where t1.A = t2.A
* orcl update test2 t2 set t2.value=(select t1.value from test1 t1 where t1.name=t2.name) where t2.value2='A';
* orcl MERGE INTO  a USING  b ON ( a.id = b.id) WHEN MATCHED THEN UPDATE SET a.code = b.code 

## 聚合
1. COUNT：统计行数量
2. SUM：获取单个列的合计值
3. AVG：计算某个列的平均值
4. MAX：计算列的最大值
5. MIN：计算列的最小值
6. group by 分组函数 

## 去重
* distinct 放在前面可以作用多列
* group by 分组函数 

### 根据某些列去重查询结果
* sql,orcl SELECT * FROM (SELECT ROW_NUMBER() OVER (partition by sname,sPec ORDER BY sname  DESC) as Rank,A.*
From table1 A where ISNULL(sname,'')<>''  ) M WHERE Rank=1 Order by sname
* orcl SELECT * from table1 WHERE (id) IN ( SELECT id FROM table1 GROUP BY id HAVING COUNT(id) > 1) 
AND ROWID NOT IN (SELECT MIN(ROWID) FROM table1 GROUP BY id HAVING COUNT(*) > 1) 

## 循环

### SQL 循环
* declare @orderNum varchar(255) --申明变量
* declare @n int,@rows int
* create table #ttableName(id int identity(1,1),Orders varchar(255)) --临时表加前缀#
* insert #ttableName(orders) select iPatientType from Patient where dInHospital>'2020' --插入临时表数据
* select @rows =@@rowcount  --返回上次sql语句所影响的数据行数
* set @n=1 --初始化变量
* while @n<=@rows --循环
* begin
    select @orderNum=iPatientType from Patient where dInHospital>'2020'
    print (@OrderNum)--输出打印
    select @n=@n+1   --赋值
* end
* drop table #ttableName --删除临时表
### SQL 游标
``` sql
declare @orderN varchar(50)　　--临时变量，用来保存游标值
declare y_curr cursor for 　　--申明游标 为orderNum
select orderNum from pe_Orders where orderId<50
open y_curr 　　--打开游标
fetch next from Y_curr into @orderN 　　----开始循环游标变量
while(@@fetch_status=0)　　---返回被 FETCH 语句执行的最后游标的状态，而不是任何当前被连接打开的游标的状态。
begin
  print (@orderN)
  update pe_Orders set Functionary+@orderN where orderNum=@orderN 　　--操作数据库
  fetch next from y_curr into @orderN 　　--开始循环游标变量
end
close y_curr　　--关闭游标
deallocate y_curr 　　--释放游标
```

### orcl 循环
``` sql
DECLARE V_SQL VARCHAR2(1000);
v_count number;
BEGIN
    FOR RW IN (select TABLE_NAME from USER_TAB_COLS where table_name LIKE 'DI_%' AND COLUMN_NAME='DOMAIN_CODE' GROUP BY TABLE_NAME)
    LOOP
         V_SQL:='select count(0)  from '||RW.TABLE_NAME||' WHERE  DOMAIN_CODE=''340824000001'' ';
         EXECUTE IMMEDIATE V_SQL INTO v_count ;
       if v_count<>0 then
            V_SQL:='DELETE DI_INTERFACE.'||RW.TABLE_NAME||' WHERE DOMAIN_CODE=''340824000001''';
           -- dbms_output.put_line(V_SQL);
            EXECUTE IMMEDIATE V_SQL;
            end if;
    END LOOP;
END;
```

## 函数

## 存储过程
