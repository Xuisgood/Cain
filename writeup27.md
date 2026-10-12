# [CTF BugKu] 变量1

题目信息
 
题目名称：变量1

题型：Web代码审计

考点：PHP可变变量$、PHP超全局变量GLOBALS

题目描述：访问页面直接展示PHP源码，提示flag In the variable !，flag存放在全局变量中。
 
页面源码
 
flag In the variable ! <?php  

error_reporting(0);

include "flag1.php";

highlight_file(__file__);

if(isset($_GET['args'])){

    $args = $_GET['args'];
    
    if(!preg_match("/^\w+$/",$args)){
    
        die("args error!");
        
    }
    
    eval("var_dump($$args);");
    
}

?>

原理分析
 
1.  preg_match("/^\w+$/",$args) ：正则限制args只能由字母、数字、下划线组成，不能传入括号、引号等特殊字符。

2.  $$args  是PHP可变变量。args的值作为新变量名。如果传入args=GLOBALS，则args等价于GLOBALS。

3. GLOBALS是PHP超全局数组，保存当前页面所有全局变量。flag1.php里定义的flag全局变量会保存在GLOBALS数组内。

4. eval执行 var_dump($$args) ，打印$GLOBALS数组，就能看到flag变量。
 
手动解题步骤
 
1. 打开靶场网页，观察页面给出的PHP源代码。

2.在浏览器URL末尾拼接GET参数：？args=GLOBALS

完整URL格式：靶场地址/?args=GLOBALS

3. 访问修改后的链接，页面会输出var_dump打印的全局变量数组。

4. 在输出的数组内容中找到flag变量，复制flag字符串。
 
Flag
 
flag{6df491b3ca68fc5d2afef2dfd0aea228}
 
踩坑记录
 
1. 参数名称必须是args，不能写错。参数值GLOBALS大小写不能写错。

2. 正则不允许特殊符号，不能尝试传入带括号、引号的payload，会直接输出args error。

3. 不要混淆可变变量 $$ ，不是 $args 。

4. flag在$GLOBALS数组里面，需要在一大段数组输出内容中找到flag对应的字符串。
 
总结
 
本题考察PHP可变变量和超全局变量GLOBALS。利用GET传入args=GLOBALS，通过可变变量args读取GLOBALS，打印全部全局变量，取出数组内的flag。
