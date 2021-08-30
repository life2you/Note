#### case-when 基本用法

```mysql

case column 
when param1 then result1
when param2 then result2
else result_default
end

```

#### case-when 判Null用法

```mysql

case 
when column1 is null then result1 
when column1 != null then result2
else result_default
end

```

