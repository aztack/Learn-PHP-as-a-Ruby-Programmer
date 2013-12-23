PHP中的array和map都用`array` language construct来构建。
前者叫数组，后者叫关联数组。


```
static_scalar = common_scalar
	| T_STRING
	| "+" static_scalar
	| "-" static_scalar
	| "array" "(" [static_array_pair_list] ")"
	| static_class_constant ;
	
static_array_pair_list = static_array_pair { "," static_array_pair } [","] ;

static_array_pair = static_scalar ["=>" static_scalar] ;
```

创建
====
```php
$odd = array(1,3,5,7);
$person = array("name"=>"jack","age"=>"28");
```

长度
====

```php
$a[0] = 1;
$a[1] = 3;
$a[2] = 5;
$result = count($a);
// $result == 3

$b[0]  = 7;
$b[5]  = 9;
$b[10] = 11;
$result = count($b);
// $result == 3

$result = count(null);
// $result == 0

$result = count(false); //注意!
// $result == 1

$food = array('fruits' => array('orange', 'banana', 'apple'),
              'veggie' => array('carrot', 'collard', 'pea'));
echo count($food, COUNT_RECURSIVE); // output 8
```

遍历
====

```
foreach (array_expression as $value)
    statement
foreach (array_expression as $key => $value)
    statement

```

```
statement 
  | ...
  | "foreach" "(" (variable|expr_without_variable)
	  "as" foreach_variable ["=>" foreach_variable] ")"
	  foreach_statement
	  
foreach_statement = statement
	| ":" inner_statement_list "endforeach" ";" ;
```

```php
$arr = array(1, 2, 3, 4);
foreach ($arr as &$value) {
    $value = $value * 2;
}

```

映射(map)
====

`array array_map ( callback $callback , array $arr1 [, array $... ] )`

```php
function cube($n)
{
    return($n * $n * $n);
}

$a = array(1, 2, 3, 4, 5);
$b = array_map("cube", $a);
print_r($b);

```

排序
====

PHP中有很多sort函数:
`sort/usort`,`asort/uasort`,`ksort`,`krsort`,`arsort`,`natsort/natcasesort`,`rsort`,`array_multisort`,

`bool sort ( array &$array [, int $sort_flags ] )`

```php
$fruits = array("lemon", "orange", "banana", "apple");
sort($fruits);
foreach ($fruits as $key => $val) {
    echo "fruits[".$key."] = " . $val . "\n";
}

```

`usort — 使用用户自定义的比较函数对数组中的值进行排序`

```php
function cmp($a, $b)
{
    if ($a == $b) {
        return 0;
    }
    return ($a < $b) ? -1 : 1;
}

$a = array(3, 2, 5, 6, 1);

usort($a, "cmp");

foreach ($a as $key => $value) {
    echo "$key: $value\n";
}

```

`asort — 对数组进行排序并保持索引关系`
`uasort — 使用用户自定义的比较函数对数组中的值进行排序并保持索引关联`


```php
$fruits = array("d" => "lemon", "a" => "orange", "b" => "banana", "c" => "apple");
asort($fruits);
foreach ($fruits as $key => $val) {
    echo "$key = $val\n";
}
//c = apple
//b = banana
//d = lemon
//a = orange

```
