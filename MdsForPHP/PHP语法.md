# PHP语法
遍历当前路径的所有文件和文件夹名字

```
$path = "../";
$fh = opendir($path);
while (($row = readdir($fh)) !== false) {
    var_dump($row);
}
closedir($fh);
?>
```
`readir($fh)`获取到有文件则返回文件名， 否则返回FALSE， 需要注意一点遍历的结束条件是<span style ="color: red;">!==</span> ,避免文件(或者文件夹)名为“0”，0，null等的文件。



```
$test = array(2=>'php',2.5=>'入','2.5'=>'门','2'=>'中', null=>"ha");
print_r($test); //Array ( [2] => 中 [2.5] => 门 [] => ha )
```

数组常用操作

```
// 数组常用操作
$info = array("name" => "zhou", "job" => "coder", "hobby" => "hh");
foreach ($info as $key => $value) {
    echo $key, ":",  $value;
}
echo "<br>";
echo implode(",",$info) ,"<br>";
echo count($info) , "<br>";  // count(str) = 1
echo array_key_exists("name", $info) . "<br>";
echo isset($info["name"]) ,"a" ,"<br>";
echo "<hr>";
array_pop($info);
print_r($info);
echo "<br>";
array_push($info, "aaaa");
print_r($info) . "<br>";
echo "<Br>";
array_shift($info);
print_r($info) . "<br>";
echo "<br>";
array_unshift($info, "aaaaaaaaaa");
print_r($info);
echo "<br>";
```

PHP算法：小羊繁殖问题
>Q: 小羊成年长度5年，每满两年生一只（2岁，4岁），5岁死，20年后年的数量

```
$sheeps = array(1,0,0,0,0);   // key  1-5岁,   value 每一岁对应的羊的数量
for ($i = 1; $i<=20;$i++) {
    $oneAge = $sheeps[1] + $sheeps[3];
    array_unshift($sheeps,$oneAge);
    array_pop($sheeps);
}
echo array_sum($sheeps);
```

PHP超全局变量（8个）**$_GLOBALS**,都是数组的形式，从*php.ini*可以看出`variables_order = "EGPCS"`,顺序从左到右，newer values override older values

* $_GET &nbsp; &nbsp; &nbsp; &nbsp;用于接收get请求的参数
* $_POST &nbsp; &nbsp; &nbsp; &nbsp;用于接受post请求的参数
* $_REQUEST &nbsp; &nbsp; &nbsp; &nbsp;手册介绍 由GET，POST 和 COOKIE 机制提交至脚本的变量，因此该数组并不值得信任。GET,POST,COOKIE重复的key，newer values override older values
* $_SESSION 
* $_COOKIE
* $_FILES
* $_SERVER  &nbsp; &nbsp; &nbsp; &nbsp; web服务器的环境
* $_ENV 

常量的定义方式`define("NAME",value)`,读取不需要`$`,而且一旦`define`之后，不能再次`define`,不能销毁,常量名一般全大写。常量没有作用域问题，都可以使用。
常量不能使数组、对象、资源等具有可变性质的数据。

```
if (!defined('RATE')) {
    define('RATE',1.1);
}
echo RATE;
unset(RATE);  // 此处错误,unset只能用于变量
```

`||`的短路特行

```
defined('RATE') || define('RATE',1.1);  // 前为true，后直接不执行
```

浮点数是不精确的，尽可能使用整数，避免使用浮点数,iOS中的现象是逐次加1.0并输出，若干次之后会出现0.999999的情况

```
if ((0.1 + 0.2) == 0.3) {
    echo 'equal';
} else {
    echo 'not equal';   //  输出not equal
}
```

逻辑与的短路运算：`((true && false))` 已经确定确定整个逻辑为FALSE，`&& $a = 6`会被直接抛弃掉

```
$a = 5;
((true && false)) && $a = 6;
echo $a, "<br>";  // 5
```
逻辑或的短路运算: ①中`defined('PI')`为FALSE，不能确定整个逻辑的true或者FALSE，所以`define('PI', 3.14)`会继续执行；②中`defined('PI')`为true，后面的逻辑已经不能影响整个逻辑的结果，所以
`define('PI', 3.14)`不被执行

```
defined('PI') || define('PI', 3.14);①
defined('PI') || define('PI', 3.14);②
```

同其他语言一样，static变量只被申明一次

```
function test() {
    static $a = 10;
    $a ++;
    echo $a , "<br>";
}
test(); // 11
test(); // 12
test(); //13
```

* 类型检测：`gettype(var)`可以获取变量的类型
* 判断变量是否为某一类型

        method | 见解
----------|--------------:|
is_float()[is_double] |  浮点数
is_int()[is_integer] | 整数
is_string() | 字符串
is_object() | 对象
is_array() | 数组
is_resource | 资源
is_bool | 布尔
is_null | 为空

* 字符串转数字
        
        从左至右截取，一直到不能是数字的一部分，如开头第一个字符就不是数字，该字符串转化的
        数字为0
        $a = '.5a';
        $b = $a + 3; // 3.5
        $a = 'a.5a' -> b = 3
        $a = '1.5a' -> b = 4.5
        
* 数字转字符串 直接`.`拼接
* 数字/字符串/数组等 转布尔
        
        FALSE的情况：'','0',0,0.0,false,NULL,array() 
        
* 对`empty(var)`的介绍：
 
    > A variable is considered empty if it does not existor  if its value
 equals <b>FALSE</b>
    
    即是下面两种情况：  
    1：变量不存在；
    2：变量存在，=FALSE，包括上面的FALSE的所有情况，只声明变量，不赋值是null，也是FALSE的   
    一种情况
    
* 对`isset(var)`的介绍

    > Determine if a variable is set and is not NULL.
    
    1.变量不存在
    2.变量存在不赋值，或者赋值为null

地址引用：&

```
$a = 10; $b = &$a;
$a = 20; echo $b;
```

* `unset(var)`的介绍

    >       Destroys the specified variables
    
    销毁一个引用变量的时候，只要引用的数量不等于0，地址中的数据就一直保留(不能被其他数据重写)，
    
        $a = 10;
        $b = &$a;
        unset($a);
        #isset($a) = false  isset($b) = true
        
* `==` 和 `===`
    
   `==`： 只验证值是否相等
    `===`：验证值和类型是否都相等
    注意一点：对于返回结果为0的逻辑判断，比如查找母字符串中子串的位置，会被认为是FALSE，所以对于此类的插叙使用`===`
    
        $a = "abcdefg";
        $b = "abc";
        if (strpos($a, $b) == false) {
         echo "not exist";  # ==
        } else {
          echo "exist";  # ===
        }

* 一段关于算符优先级的代码

        $a=3;        $b=5;        if($a=5||$b=7){        //因为$a,$b 赋值的时候没有加小括号 而 = 的优先级没有 || 高 //if(($a=5)||($b=7))        //所以 5||$b 返回 true; $a = true; true++ 还是true (echo true;显示1) $a++;//
           var_dump($a);           $b++;        }        echo $a,$b//1,6

* 函数内部访问外部变量

        $money = 200;  // 函数外部,全局变量， $GLOBAL数组中可以找到这个变量
        function save($anotherMoeny) {
          global $money;  #找全局变量money,找不到初始化为0,没有global，则函数不能访问money
          $money = $money + $anotherMoeny;
        }
        save(200);
        echo $money;
* 时间函数

    1. `time()`获取格林威治时间至现在的秒数
    2. `microtime()`获取格林威治时间至现在的秒数+毫秒数
    3. 如果获取的时间时区不对，可通过打印`phpinfo()`，找到`php.ini`文件，修改`date.timezone = PRC` RPC：中华人民共和国缩写
    4. `date($format, $timestamp = null)`格式化时间戳

            $now = time();
            echo date('Y-m-d H:i:s', $now);#如果不传第二个参数，默认time()
            echo date('Y年m月d日 H时i分s秒', $now);#如果不传第二个参数，默认time()
    5. 获取一个日期的Unix时间戳

            //mktime ($hour = null, $minute = null, $second = null, $month = null, $day = null, $year = null, $is_dst = null)
            # 最后一个参数废弃
    6. 检测日期是否合法

            checkdate ($month, $day, $year)
            var_dump(checkdate(3,3,2)); #true

* 字符串相关函数

    1. 大段字符串， 大段标识不能有空格，大段字符串内注释无效
            
            大段单引号字符串
            $str = <<<'hh'
            第一段
            第二段
            第三段
            第四段 
            第五段
            hh;
            
            大段双引号字符串
            $str = <<<hh
            第一段
            第二段
            第三段
            第四段 
            第五段
            hh;
    2. 字符串的转义

            $str1="\"\\\n\t\v\$";
            $str2='\"\\\n\t\v\'\$';            echo $str1,'<br >',$str2;
            #从打印结果可以看出，单引号只能转义\ 和 ',双引号可以转义的比较多
    3. 对变量的解析：双引号可以解析出字符串中的变量，单引号不可以。如果字符串中本来就包含`$`符号，那么使用单引号，或者转义，避免`$`连同后面的字符被理解成字符串`echo "i have a \$logo";`
    4. `strlen ($string)`：获取当前编码下字符串字节长度，utf下汉字3个字节
    5. `mb_strlen ($str, $encoding = null)`获取字符串长度，如果`$encoding`参数为空，获取的依然是字节数量

            echo mb_strlen('aaa好的','utf-8');#5
            echo mb_strlen('aaa好的');#9
    6. `strpos ($haystack, $needle, $offset = 0)`查找从`offset`开始子串的位置，找不到返回FALSE
    7. `str_replace ($search, $replace, $subject, &$count = null)` `count`用来统计被替换的次数
    8. `substr ($string, $start, $length = null)` 如果 start 参数是负数且 length 小于或等于 start，则 length 为 0

        参数   |  描述
        ------|-------
        start | <0: 在从字符串结尾开始的指定位置开始(1.2.3....), >=0:从字符串的指定位置开始(0.1.2.3...)
        length |可选，默认直到母串的结尾，>0:从 start 参数所在的位置返回的长度, <0: 从母串末端，返回到倒数第几个，必须大于start，否则length = 0
        
    9. `str_split ($string, $split_length = 1)` 以`split_length`进行分割字符串，返回一个数组
    10. `explode ($delimiter, $string, $limit = null)` 分割字符串，如果`limit`为null，返回包含所有分割元素的数组，如果为负数，返回不包含`-last limit`的元素数组，如果正数，字符串分割成limit份(如果不能切分，最大的数量)，0作1处理

            $str1 = "ab,cd,ef,ghi,jkl,mn";
            explode(',', $str1) // Array ( [0] => ab [1] => cd [2] => ef [3] => ghi [4] => jkl [5] => mn )
            explode(',', $str1,-2) // Array ( [0] => ab [1] => cd [2] => ef [3] => ghi )
            explode(',', $str1,1000) // Array ( [0] => ab [1] => cd [2] => ef [3] => ghi [4] => jkl [5] => mn )
            explode(',', $str1,1) // Array ( [0] => ab,cd,ef,ghi,jkl,mn )
            
    11. `implode ($glue = "", array $pieces)` 合并数组为字符串
    12. `strcmp ($str1, $str2)` equal返回0，less返回negative，greater返回positive
    13. `strcasecmp ($str1, $str2)` 同上(不区分大小写)


* 数组的相关
    > <span style="font-size:14px">数组分为三种：索引数组，关联数组，多维数组
    索引数组：key 无实际意义，等价于iOS中的数组
    关联数组：key-value键值对的形式存在，key有实际意义，等价于iOS中的字典
    多维数组：value可以是PHP中8种类型的任意一种，包括数组</span>
    
    key可以是数字和字符串，规则如下（一般不会刻意使用极端情况）：
    
    * key为浮点型， 向下取整 -> Int
    * key为整数型的字符串  -> Int
    * key为null，按照空字符串来理解
    * 设置重复key或者以上三条导致key重复，后者覆盖前者，并且value的属性不变

    1. 遍历

            $info = array("zwk",33,"hobby");
            #索引数组可以是for循环遍历，关联数组不可以
            for ($i = 0; $i < count($info); $i ++) {
             echo $info[$i];    
            }
            #遍历key value
            $info = 
            array('name'=>'zwk','age'=>18,'hobby'=>'none');
            foreach ($info as $key => $value) {
             echo $key , ':' , $value , '<br>';
            }
            #遍历只vaule，不能只遍历key
            foreach ($info as $value) {
             echo $value , '<br>';
            }
            
    2. `array_keys(array $input, $search_value = null, $strict = null)` 返回`input`中`value == search_value`的key组成的数组，参数2可为null，为null返回`input`所有的key，参数3是否使用严格比较`===`,默认false，true则value=22 和value = ’22‘ 不同的，

    3. `unset($info['name']);`删除数组单元，`$info['name'] = null;`并不能删除数组单元，即使置为`null`,其key所对应的value等于`null`,但依然在数组中
    4. `count($var)` 数组返回长度，非数组返回1，null返回0
    5. `array_key_exists($key, array $search)`,返回键是否存在，即使`value = null`,依然返回true
    6. `in_array ($needle, array $haystack, $strict = null)`,数组中是否存在某个value，参数3是否开启严格比较模式`===`
    7. `array_push (array &$array, $var1, $var2 , $var3,...)`将若干个单元压入数组的末尾
    8. `array_pop (array &$array)`弹出末尾单元
    9. `array_shift (array &$array)`移除开头的单元
    10. `array_unshift (array &$array, $var1, $var2, $var3..)`在数组开头插入若干个单元

* 常量

    1. `define('PI',3.14);`不能重复定义，定义之前需要检测是否已经定义，使用常量不需要`$`,`echo PI`
    2. `isset($var)`:`Determine if a variable is set and is not NULL`;`defined($var)`:`Checks whether a given named constant exists`
    3. 使用逻辑运算符的短路特性定义常量`defined('PI') || define('PI',3.14);`
    4. 常量的作用域不分全局/局部，任何地方都可以访问

            function test() {
                defined('PI') || define('PI',3.14);
            }
            test();
            echo PI;
            
* 文件引入

    1. `include`、`include_once`引入不存在的文件，会继续走下去

            include ('Test01.html');
            include_once ('Test01.html');
            include ('Test02.php');
            echo '引入结束';
    2. `require`、`require_once`引入不存在的文件，致命错误，后面的代码终止执行

            require ('Test01.html');
            require_once ('Test01.html');
            require ('Test02.php');
            echo "引入结束";

