编译原理课程设计-教学要求

1	课程设计内容及要求
1.1	课程设计的内容
	课程设计题目
一个PASCAL语言子集（PL/0）编译器的设计与实现
	PL/0语言的BNF描述（扩充的巴克斯范式表示法）
<prog> → program <id>；<block>
<block> → [<condecl>][<vardecl>][<proc>]<body>
<condecl> → const <const>{,<const>};
<const> → <id>:=<integer>
<vardecl> → var <id>{,<id>};
<proc> → procedure <id>（[<id>{,<id>}]）;<block>{;<proc>}
<body> → begin <statement>{;<statement>}end
<statement> → <id> := <exp>               
|if <lexp> then <statement>[else <statement>]
               |while <lexp> do <statement>
               |call <id>（[<exp>{,<exp>}]）
               |<body>
               |read (<id>{，<id>})
               |write (<exp>{,<exp>})
<lexp> → <exp> <lop> <exp>|odd <exp>
<exp> → [+|-]<term>{<aop><term>}
<term> → <factor>{<mop><factor>}
<factor>→<id>|<integer>|(<exp>)
<lop> → =|<>|<|<=|>|>=
<aop> → +|-
<mop> → *|/
<id> → l{l|d}   （注：l表示字母）
<integer> → d{d}
注释：
<prog>：程序 ；<block>：块、程序体 ；<condecl>：常量说明 ；<const>：常量；<vardecl>：变量说明 ；<proc>：分程序 ； <body>：复合语句 ；<statement>：语句；<exp>：表达式 ；<lexp>：条件 ；<term>：项 ； <factor>：因子 ；<aop>：加法运算符；<mop>：乘法运算符； <lop>：关系运算符。
	假想目标机的代码
LIT 0 ，a 取常量a放入数据栈栈顶
OPR 0 ，a 执行运算，a表示执行某种运算
LOD L ，a 取变量（相对地址为a，层差为L）放到数据栈的栈顶
STO L ，a 将数据栈栈顶的内容存入变量（相对地址为a，层次差为L）
CAL L ，a 调用过程（转子指令）（入口地址为a，层次差为L）
INT 0 ，a 数据栈栈顶指针增加a
JMP 0 ，a无条件转移到地址为a的指令
JPC 0 ，a 条件转移指令，转移到地址为a的指令
RED L ，a 读数据并存入变量（相对地址为a，层次差为L）
WRT 0 ，0 将栈顶内容输出
代码的具体形式：
 
其中：F段代表伪操作码
	  L段代表调用层与说明层的层差值
      A段代表位移量（相对地址）
进一步说明：
INT：为被调用的过程（包括主过程）在运行栈S中开辟数据区，这时A段为所需数据单元个数（包括三个连接数据）；L段恒为0。
CAL：调用过程，这时A段为被调用过程的过程体（过程体之前一条指令）在目标程序区的入口地址。
LIT：将常量送到运行栈S的栈顶，这时A段为常量值。
LOD：将变量送到运行栈S的栈顶，这时A段为变量所在说明层中的相对位置。
STO：将运行栈S的栈顶内容送入某个变量单元中，A段为变量所在说明层中的相对位置。
JMP：无条件转移，这时A段为转向地址（目标程序）。
JPC：条件转移，当运行栈S的栈顶的布尔值为假（0）时，则转向A段所指目标程序地址；否则顺序执行。
OPR：关系或算术运算，A段指明具体运算，例如A=2代表算术运算“＋”；A＝12代表关系运算“>”；A＝16代表“读入”操作等等。运算对象取自运行栈S的栈顶及次栈顶。
	假想机的结构
两个存储器：存储器CODE，用来存放P的代码
            数据存储器STACK（栈）用来动态分配数据空间
四个寄存器：
一个指令寄存器I:存放当前要执行的代码
一个栈顶指示器寄存器T：指向数据栈STACK的栈顶
一个基地址寄存器B：存放当前运行过程的数据区在STACK中的起始地址
一个程序地址寄存器P：存放下一条要执行的指令地址
该假想机没有供运算用的寄存器。所有运算都要在数据栈STACK的栈顶两个单元之间进行，并用运算结果取代原来的两个运算对象而保留在栈顶。
	活动记录：
 
RA：返回地址
DL：调用者的活动记录首地址
SL：保存该过程直接外层的活动记录首地址
过程返回可以看成是执行一个特殊的OPR运算
注意：层次差为调用层次与定义层次的差值
1.2	课程设计的要求
	程序实现要求
PL/0语言可以看成PASCAL语言的子集，它的编译程序是一个编译解释执行系统。PL/0的目标程序为假想栈式计算机的汇编语言，与具体计算机无关。
PL/0的编译程序和目标程序的解释执行程序可以采用C、C++、Java等高级语言书写。
其编译过程采用一趟扫描方式，以语法分析程序为核心，词法分析和代码生成程序都作为一个独立的过程，当语法分析需要读单词时就调用词法分析程序，而当语法分析正确需要生成相应的目标代码时，则调用代码生成程序。
 
用表格管理程序建立变量、常量和过程标识符的说明与引用之间的信息联系。
用出错处理程序对词法和语法分析遇到的错误给出在源程序中出错的位置和错误性质。
当源程序编译正确时，PL/0编译程序自动调用解释执行程序，对目标代码进行解释执行，并按用户程序的要求输入数据和输出运行结果。
	组织形式要求
每个学生单独完成。
	时间要求
按照规定，编译课程设计时间为一周。
课设周-18周周五验收。
2	课程设计成绩评定
课程设计完成后，根据学生课设期间的表现情况、最终程序的实现质量以及课程设计报告的书写情况进行成绩评定。可以采取学生演示、老师抽问等形式进行课程设计程序实现质量的检查。一般情况下，满足下列条件的学生给予高分：
	课程设计期间主动认真
	能够较好地将理论知识应用与课程设计中解决实际问题
	要求的功能全部完成，能够正确回答老师的提问
	课程设计报告符合规范，内容翔实

3	课程设计上交内容
课程设计完成后，每位学生需要上交的资料包括：
	课程设计报告书（不得少于15页-五号字体，不得包含源程序）
	程序源码
	测试程序若干
提交形式：
压缩程一个压缩文件（例：161610101_姓名.rar）


