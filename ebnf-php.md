[EBNF of PHP](http://www.icosaedro.it/articoli/php-syntax-ebnf.txt)

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

相比之下ruby的__END__就优雅多了

```ruby
puts DATA.read
__END__
hello world!
```
将输出`hello world!`
