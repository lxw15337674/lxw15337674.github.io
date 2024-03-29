---
title: "解析器"
date: 2022-08-02T18:06:56+08:00
draft: false
tags: [""]
categories: ["笔记"]
typora-root-url: ..\..\static
---

## 总结

一个解析器分为四步：

1. 词法分析，将 SQL 字符串拆分成包含关键词识别的字符段（Tokens）。
2. 语法分析，利用自顶向下或自底向上的算法，将 Tokens 解析为 AST，可以手动，也可以自动。
3. 错误检测、恢复、提示推断，都需要利用语法分析产生的 AST。
4. 语义分析，做完这一步就可以执行 SQL 语句了，不过对前端而言，不需要深入到这一步，可以跳过。

## 词法分析

词法分析阶段是编译过程的第一个阶段。

这个阶段的任务是从左到右一个字符一个字符地读入源程序，然后根据构词规则识别单词(也就是token)。比如把“我学习编程”这个句子拆解成“我”“学习”“编程”，这个过程叫做“分词”。

通常用现成工具Lex/Yacc/JavaCC/Antlr生成词法分析器（lexical analyzer、lexer 或者 scanner）。

antlr举例：

```javascript
lexer grammar Hello;  //lexer关键字意味着这是一个词法规则文件，名称是Hello，要与文件名相同
//关键字
If :               'if';
Int :              'int';
//字面量
IntLiteral:        [0-9]+;
StringLiteral:      '"' .*? '"' ;  //字符串字面量
//操作符
AssignmentOP:       '=' ;    
RelationalOP:       '>'|'>='|'<' |'<=' ;    
LeftParen:          '(';
RightParen:         ')';
//标识符
Id :                [a-zA-Z_] ([a-zA-Z_] | [0-9])*;
```



## 语法分析

在词法分析的基础上判断单词组合方式识别出**程序的语法结构**。这个结构是一个树状结构，这棵树叫做抽象语法树（Abstract Syntax Tree，AST）。树的每个节点（子树）是一个语法单元（也就是就是词法分析阶段生成的 Token），这个单元的构成规则就叫“语法”。



## 语义分析——标注AST的属性

语义分析是要让计算机理解我们的真实意图。**语义分析的结果保存在AST 节点的属性上**，比如在 标识符节点和 字面量节点上标识它的数据类型是 int 型的。在AST上还可以标记很多属性。





以balbel流程为例：

- input => [tokenizer](https://github.com/caiyongmin/awesome-coding-javascript/tree/master/src/bundler/babel/lib/tokenizer.js) => tokens，先对输入代码进行**分词**，根据最小有效语法单元，对字符串进行切割。
- tokens => [parser](https://github.com/caiyongmin/awesome-coding-javascript/tree/master/src/bundler/babel/lib/parser.js) => AST，然后进行**语法分析**，会涉及到读取、暂存、回溯、暂存点销毁等操作。
- AST => [transformer](https://github.com/caiyongmin/awesome-coding-javascript/tree/master/src/bundler/babel/lib/transformer.js) => newAST，然后**转换**生成新的 AST。
- newAST => [codeGenerator](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/src/bundler/babel/lib/codeGenerator.js) => output，最后根据新生成的 AST **输出**目标代码。



> 扩展：
>
> [**手写 SQL 编译器 - 词法分析**](https://github.com/ascoders/weekly/blob/master/%E7%BC%96%E8%AF%91%E5%8E%9F%E7%90%86/64.%E7%B2%BE%E8%AF%BB%E3%80%8A%E6%89%8B%E5%86%99%20SQL%20%E7%BC%96%E8%AF%91%E5%99%A8%20-%20%E8%AF%8D%E6%B3%95%E5%88%86%E6%9E%90%E3%80%8B.md)
>
> https://qiankunli.github.io/2020/02/08/fundamentals_of_compiling_frontend.html
>
> https://yearn.xyz/posts/techs/%E8%AF%8D%E6%B3%95%E8%AF%AD%E6%B3%95%E5%88%86%E6%9E%90%E5%9F%BA%E7%A1%80/#hello-world
