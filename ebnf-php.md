[EBNF of PHP](http://www.icosaedro.it/articoli/php-syntax-ebnf.txt)
[zend_language_parser.y](https://github.com/php/php-src/blob/master/Zend/zend_language_parser.y)

脚本内嵌数据
============

从下面这段生成式:
```
PHP_SOURCE_TEXT = { inner_statement | halt_compiler_statement };
halt_compiler_statement = "__halt_compiler" "(" ")" ";" ;
```

可以看出 `__halt_compiler()`并不是一个函数，而是一个‘伪装成函数的’语言结构。
它与Ruby中的__END__有类似的[用法](http://stackoverflow.com/questions/5270486/whats-halt-compiler-in-php-for):

```php
<?php
$file = fopen(__FILE__, 'rb');
// Go to the end of the __halt_compiler();
fseek($file, __COMPILER_HALT_OFFSET__);
echo stream_get_contents($file);
__halt_compiler(); §RW$FG$%ZDS$TSG$TSZ%U(); §$"§%"§$!!();
```
上面代码将输出：`§RW$FG$%ZDS$TSG$TSZ%U(); §$"§%"§$!!();`

相比之下ruby的\_\_END\_\_就优雅多了

```ruby
puts DATA.read
__END__
hello world!
```
将输出`hello world!`

echo语言结构
============

从下面的PHP statement的生成式可以找到
`"echo" echo_expr_list ";"` echo也是一个语言结构。不是函数。

`echo_expr_list = expr {"," expr}` 从这个生成式可以看出 `echo`不需要像函数那样调用:
`echo(1,2,3)`。其实不是不需要，而是不可以。单个参数的时候加上括号只是将参数用括号括起来，并不认为是函数调用。
多个参数的时候就干脆报错。

[How are echo and print different in PHP](http://stackoverflow.com/questions/234241/how-are-echo-and-print-different-in-php)

与`print`相比echo不能作为表达式的一部分，也没有返回值。

```
inner_statement = statement
	| function_declaration_statement
	| class_declaration_statement ;

inner_statement_list = { inner_statement } ;

statement = "{" inner_statement_list "}"
	| "if" "(" expr ")" statement {elseif_branch} [else_single]
	| "if" "(" expr ")" ":" inner_statement_list {new_elseif_branch}
	  [new_else_single] "endif" ";"
	| "while" "(" expr ")" while_statement
	| "do" statement "while" "(" expr ")" ";"
	| "for" "(" for_expr ";" for_expr ";" for_expr ")" for_statement
	| "switch" "(" expr ")" switch_case_list
	| "break" [expr] ";"
	| "continue" [expr] ";"
	| "return" [expr_without_variable | variable] ";"
	| "global" global_var {"," global_var} ";"
	| "static" static_var { "," static_var } ";"
	| "echo" echo_expr_list ";"
	| T_INLINE_HTML
	| expr ";"
	| "use" use_filename ";" # FIXME: not implemented
	| "unset" "(" variable {"," variable} ")" ";"
	| "foreach" "(" (variable|expr_without_variable)
	  "as" foreach_variable ["=>" foreach_variable] ")"
	  foreach_statement
	| "declare" "(" declare_list ")" declare_statement
	| ";" # empty statement
	| "try" "{" inner_statement_list "}" catch_branch {catch_branch}
	| "throw" expr ";" ;
```

其他语言结构
============

```
expr_without_variable = "list" "(" assignment_list ")" "=" expr
	...
	| "(int)" expr
	| "(double)" expr
	| "(float)" expr
	| "(real)" expr
	| "(string)" expr
	| "(array)" expr
	| "(object)" expr
	| "(bool)" expr
	| "(boolean)" expr
	| "(unset)" expr # FIXME: not implemented
	| "exit" [exit_expr]
	| "die" [exit_expr]
	...
	| "array" "(" [array_pair_list] ")"
	| "print" expr ;
```

`list`可以实现`并行赋值`

```php
list($drink, $color, $power) = array('coffee', 'brown', 'caffeine');
```
`array`构造一个数组。
这两个结构都需要加上括号（伪装成函数调用）。
这个地方就是PHP设计上不一致的地方。`echo`就不能加括号，`list`就需要。

Internal Function
=================

```
internal_functions = "isset" "(" variable {"," variable} ")"
	| "empty" "(" variable ")"
	| "include" expr
	| "include_once" expr
	| "eval" "(" expr ")"
	| "require" expr
	| "require_once" expr ;
```


变量
====
```
T_STRING = LABEL;

LABEL = (letter | "_") {letter | digit | "_"} ;

T_VARIABLE = "$" T_STRING ;
```

函数
====
```
function_declaration_statement = "function" ["&"] T_STRING
	"(" parameter_list ")" "{" inner_statement_list "}" ;
```
