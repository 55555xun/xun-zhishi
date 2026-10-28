# C 语言学习指南

> 从完全新手到能独立做项目。开头一页速查表，后面是正文：每章拆成 5 个学习等级。

---

# 速查表（一页版）

## 1. 一句话定义

C 是一门贴近硬件的编译型通用编程语言：你直接管理内存和类型，换来接近机器的速度与控制力。

## 2. 核心概念

| 概念 | 要点 |
|---|---|
| 编译流程 | 源码 `.c` → 预处理 → 编译 → 汇编 → 链接 → 可执行文件 |
| 类型 | `int` `char` `float` `double`，大小依平台而定，用 `sizeof` 查看 |
| 指针 | 存放地址的变量，`*` 解引用，`&` 取地址 |
| 数组 | 连续内存；传参时退化为指向首元素的指针 |
| 字符串 | 以 `'\0'` 结尾的 `char` 数组 |
| 内存 | 栈（自动释放）、堆（`malloc`/`free` 手动管理）、静态区 |
| 结构体 | `struct` 把不同类型的数据打包成一个整体 |
| 头文件 | 放声明（函数原型、类型、宏），实现放 `.c` 文件 |

## 3. 真实例子

读取用户输入的整数，求和后输出：

```c
#include <stdio.h>

int main(void) {
    int n, sum = 0;
    printf("请输入个数: ");
    if (scanf("%d", &n) != 1 || n <= 0) {
        fprintf(stderr, "输入无效\n");
        return 1;
    }
    for (int i = 0; i < n; i++) {
        int x;
        if (scanf("%d", &x) != 1) return 1;
        sum += x;
    }
    printf("总和 = %d\n", sum);
    return 0;
}
```

编译运行：

```bash
gcc -Wall -Wextra -g demo.c -o demo
```

## 4. 常见错误

- 数组越界，读写了不属于自己的内存
- 使用未初始化的变量
- `malloc` 后忘记 `free`（内存泄漏），或 `free` 后继续使用（悬空指针）
- 字符串没留 `'\0'` 的位置，`strcpy` 缓冲区溢出
- `scanf` 忘记取地址 `&`，或不检查返回值
- 用 `=` 写成条件判断（应为 `==`）
- 对 `char *` 字符串用 `==` 比较（应使用 `strcmp`）
- 有符号整数溢出、有符号与无符号混合比较
- 函数返回局部变量的地址

## 5. 使用前检查清单

- [ ] 所有变量在使用前都已初始化
- [ ] 每个数组访问都在 `0 ~ 长度-1` 范围内
- [ ] 每次 `malloc` 都检查了是否返回 `NULL`
- [ ] 每个 `malloc` 都有对应的 `free`，且只释放一次
- [ ] 字符串操作使用带长度限制的函数（`snprintf`、`strncpy` 并手动补 `'\0'`）
- [ ] 所有输入函数的返回值都被检查
- [ ] 编译时打开 `-Wall -Wextra`，警告清零
- [ ] 用 `-fsanitize=address,undefined` 或 Valgrind 跑过一遍
- [ ] 头文件有 include guard（`#ifndef` 或 `#pragma once`）

## 6. 5 个自测小问题

1. `int a[5];` 中 `a` 和 `&a[0]` 有什么关系？`sizeof(a)` 与函数形参里的 `sizeof(a)` 为什么不同？
2. `char *s = "abc"; s[0] = 'x';` 为什么会出错？
3. `malloc(n * sizeof(int))` 与 `calloc(n, sizeof(int))` 有何区别？
4. 什么是悬空指针？举一个会产生它的写法。
5. `static` 修饰局部变量和修饰全局函数分别起什么作用？

<details>
<summary>参考答案</summary>

1. 数组名在多数表达式中退化为首元素地址，二者值相同；但 `sizeof(a)` 在定义处得到整个数组大小，作为形参时 `a` 已是指针，只得到指针大小。
2. 字符串字面量存放在只读区，修改属于未定义行为；应使用 `char s[] = "abc";`。
3. `calloc` 会把内存清零，且内部检查乘法溢出；`malloc` 不初始化。
4. 指向已释放或已失效内存的指针，例如 `free(p);` 后继续 `*p`，或返回局部变量地址。
5. 局部变量加 `static`：生命周期延长到整个程序，只初始化一次。全局函数加 `static`：只在当前文件可见（内部链接）。

</details>

---

# 正文

学习路线按 6 个章节展开。每章统一拆成 5 个等级：

- L1 入门：能照着例子跑通
- L2 基础：能独立写小程序
- L3 熟练：能处理边界与错误
- L4 进阶：理解底层原理，能调试复杂问题
- L5 独立：能把这一块用在项目里，设计合理

每个等级都包含：需要掌握什么、常见错误、应该练习什么、进入下一层的标准。

## 第 1 章 基础语法与数据类型

### L1 入门
**一句话定义**：能在本机装好编译器，独立写出、编译并运行一个带变量、运算和格式化输出的完整 C 程序。

**核心概念**：
- 编译链路：源文件 `.c` 经 gcc/clang 编译、链接，生成可执行文件，再运行
- 程序入口：`int main(void)` 是唯一入口，返回 `0` 表示正常结束
- 基本类型：`int`（整数）、`double`（双精度浮点）、`char`（单个字符，本质是小整数）
- 格式化输出：`printf` 的格式符必须与参数类型一一对应
- 运算规则：`+ - * / %`；两个整数相除结果仍是整数，直接丢弃小数部分

**知识点详解**：
- 安装 gcc/clang，会编译运行：编译器把文本源码翻译成机器码。最小流程是 `gcc hello.c -o hello`，再运行 `./hello`（Windows 下是 `hello.exe`）。建议从第一天起加上 `-Wall -Wextra`，让编译器帮你找错。
- `main` 函数结构：`#include` 引入声明，`main` 里写语句，每条语句以分号结束，最后 `return 0;`。写成 `int main(void)` 表示不接收命令行参数。
- `printf`：来自 `<stdio.h>`，按格式串输出。`%d` 对应 `int`，`%f` 对应 `double`，`%c` 对应 `char`，`%s` 对应字符串。
- 变量声明：先声明类型再使用，如 `int a = 5;`。局部变量不初始化时值不确定，读取它是错误。
- `int`/`double`/`char`：`int` 通常 4 字节；`double` 通常 8 字节，约 15 位有效数字；`char` 通常 1 字节，字符字面量 `'A'` 就是整数 65。
- 算术运算符：`/` 在两个操作数都是整数时做整数除法；只要一边是浮点数，就按浮点数计算。`%` 只能用于整数。

**需要掌握**：安装 gcc/clang，会编译运行；`main` 函数结构；`printf`；变量声明；`int`/`double`/`char`；算术运算符

**真实例子**：

```c
#include <stdio.h>

int main(void) {
    double celsius = 36.6;                          /* 摄氏度 */
    double fahrenheit = celsius * 9.0 / 5.0 + 32.0; /* 转华氏度 */
    int a = 5, b = 2;
    char grade = 'A';

    printf("%.1f C = %.1f F\n", celsius, fahrenheit);
    printf("整数除法 5/2 = %d\n", a / b);           /* 结果为 2 */
    printf("浮点除法 5/2.0 = %.2f\n", a / 2.0);     /* 结果为 2.50 */
    printf("字符 %c 的 ASCII 码是 %d\n", grade, grade);
    return 0;
}
```

预期输出：`36.6 C = 97.9 F`，`整数除法 5/2 = 2`，`浮点除法 5/2.0 = 2.50`，`字符 A 的 ASCII 码是 65`（共四行；Windows 控制台若出现中文乱码，执行 `chcp 65001` 即可）。

**常见错误**：漏写分号；`printf` 格式符与类型不匹配（`%d` 打印 `double`）；整数除法以为得到小数（`5/2 == 2`）；使用未初始化的局部变量。

错误写法与正确写法对照：

```c
/* 错误：%d 配 double，行为未定义，输出是乱码；5/2 得到 2 而不是 2.5 */
double avg = 5 / 2;
printf("%d\n", avg);

/* 正确：让其中一个操作数是浮点数，并使用 %f */
double avg2 = 5 / 2.0;
printf("%f\n", avg2);   /* 输出 2.500000 */
```

​	**应该练习**：Hello World；温度转换；计算圆面积；交换两个变量

**使用前检查清单**：
- [ ] 每条语句都以分号结束，花括号成对出现
- [ ] 每个 `printf` 格式符的数量和类型都与参数一致
- [ ] 所有局部变量在使用前都已赋初值
- [ ] 涉及除法的地方，确认是想要整数除法还是浮点除法
- [ ] 编译时加了 `-Wall -Wextra`，并且零警告
- [ ] `main` 末尾返回 `0`

**自测小问题**：

1. `5 / 2` 与 `5 / 2.0` 的结果分别是什么？为什么不同？
2. `printf("%d", 3.14);` 会发生什么？
3. `char c = 'A'; printf("%d", c);` 输出什么？
4. 为什么推荐写 `int main(void)` 而不是 `int main()`？
5. 一个局部变量只声明不赋值就读取，会得到什么？

<details><summary>参考答案</summary>

1. 分别是 `2` 和 `2.5`。两个整数相除是整数除法，丢弃小数；只要有一个操作数是浮点数，另一个会被转换为浮点数后再计算。
2. 未定义行为：`%d` 期望 `int`，传入的却是 `double`，通常输出无意义的数字。应使用 `%f`。
3. 输出 `65`，字符在内存中就是它的 ASCII 码整数值。
4. C 中 `()` 表示“参数未指定”，`(void)` 才明确表示没有参数，编译器能更好地检查调用；这也是 C23 之前的推荐写法。
5. 值不确定（垃圾值），读取它属于错误做法，可能每次运行结果都不同。

</details>

**进入下一层的标准**：不看资料能写出完整程序并编译通过；能解释每个格式符对应的类型

### L2 基础
**一句话定义**：能读取用户输入并做合法性检查，清楚类型之间如何转换，并会用常量、位运算和定宽类型写出无警告的小程序。

**核心概念**：
- 输入：`scanf` 需要变量地址（`&n`），并且必须检查返回值
- 类型转换：隐式转换由编译器按规则自动完成，强制转换 `(type)expr` 由你显式写出
- 常量：`const` 表示只读变量，`#define` 是预处理阶段的文本替换，没有类型
- 大小：`sizeof` 在编译期得到类型或对象的字节数，结果类型是 `size_t`
- 位与定宽：`& | ^ ~ << >>` 直接操作二进制位，`<stdint.h>` 提供 `int32_t`、`uint8_t` 等位宽固定的类型

**知识点详解**：
- `scanf` 输入：`scanf("%d", &n)` 把输入解析后写入 `n` 所在的地址，所以要取地址。它返回成功解析的项数，不等于预期值就说明输入有问题，此时变量内容不可信。
- 类型转换：隐式转换发生在混合类型运算中，比如 `int` 和 `double` 相加会先把 `int` 转成 `double`。强制转换用来明确意图，如 `(double)total / count`，要注意转换发生在除法之前。
- `const`：给变量加上只读约束，修改它会触发编译错误，同时是写给读代码的人看的“承诺”。
- 常量与宏 `#define`：宏只是文本替换，不做类型检查，也没有作用域。简单常量优先用 `const` 或 `enum`，宏参数要多加括号。
- `sizeof`：表示对象占用的字节数，用 `%zu` 打印。写 `sizeof *p` 比写 `sizeof(int)` 更不容易在改类型时漏改。
- 位运算基础：`n >> i & 1` 能取出第 `i` 位，`x & 1` 能判奇偶。移位和位运算作用在整数提升后的值上，对有符号负数右移的结果由实现定义。
- `stdint.h` 定宽类型：`int` 的宽度因平台而异，`int32_t`、`uint8_t` 则宽度确定，适合协议、文件格式和位操作。

**需要掌握**：`scanf` 输入；类型转换（隐式、强制）；`const`；常量与宏 `#define`；`sizeof`；位运算基础；`stdint.h` 定宽类型

**真实例子**：

```c
#include <stdint.h>
#include <stdio.h>

#define MAX_N 100                                   /* 宏常量 */

int main(void) {
    const int base = 2;                             /* 只读变量 */
    int n;

    printf("请输入 0~%d 的整数: ", MAX_N);
    if (scanf("%d", &n) != 1 || n < 0 || n > MAX_N) { /* 检查返回值，注意 &n */
        printf("输入无效\n");
        return 1;
    }
    printf("%d 是%s数\n", n, (n % base == 0) ? "偶" : "奇");

    printf("8 位二进制: ");
    for (int i = 7; i >= 0; i--) {
        putchar(((n >> i) & 1) ? '1' : '0');        /* 逐位取出 */
    }
    putchar('\n');

    int total = 7, count = 2;
    printf("整数平均 %d，强转后 %.2f\n", total / count, (double)total / count);
    printf("sizeof(int)=%zu sizeof(int64_t)=%zu sizeof(uint8_t)=%zu\n",
           sizeof(int), sizeof(int64_t), sizeof(uint8_t));
    return 0;
}
```

输入 `37` 时的预期输出：`37 是奇数`，`8 位二进制: 00100101`，`整数平均 3，强转后 3.50`，`sizeof(int)=4 sizeof(int64_t)=8 sizeof(uint8_t)=1`（`int` 的大小因平台而异）。

**常见错误**：`scanf` 忘记 `&`；`char` 有无符号的平台差异；`int` 与 `unsigned` 混合比较导致结果反常；不检查 `scanf` 的返回值；宏参数不加括号。

错误写法与正确写法对照：

```c
/* 错误：忘记 &，scanf 把 n 的值当地址写入，程序崩溃或数据损坏 */
int n;
scanf("%d", n);

/* 错误：-1 被转成 unsigned 后变成很大的数，条件为假 */
int s = -1;
unsigned u = 1;
if (s < u) { /* 不会进入 */ }

/* 正确：加 &、检查返回值；比较前统一为有符号类型 */
if (scanf("%d", &n) != 1) { /* 处理输入失败 */ }
if (s < (int)u) { /* 会进入 */ }
```

**应该练习**：打印各类型大小与取值范围；简易计算器；判断奇偶和闰年；十进制转二进制

**使用前检查清单**：
- [ ] 每个 `scanf` 参数都带 `&`（字符串数组除外），并检查了返回值
- [ ] 混合有符号和无符号的比较，已经显式转换或避免
- [ ] 需要确定位宽的地方（协议、位操作）使用了 `stdint.h` 类型
- [ ] 强制转换写在除法之前，而不是之后
- [ ] 宏的参数和整体表达式都加了括号，或已改用 `const`/`enum`
- [ ] 加上 `-Wall -Wextra` 编译，零警告

**自测小问题**：
1. `scanf("%d", &n)` 的返回值表示什么？输入 `abc` 时返回多少？
2. `(double)(a / b)` 与 `(double)a / b` 有何区别（`a`、`b` 都是 `int`）？
3. `#define SQUARE(x) x * x`，`SQUARE(1 + 2)` 的结果是多少？
4. `char` 是有符号还是无符号？
5. `uint8_t x = 200; x + 100` 的类型和结果是什么？

<details><summary>参考答案</summary>

1. 返回成功匹配并赋值的项数。输入 `abc` 时一项都没匹配，返回 `0`；遇到输入结束（EOF）则返回 `EOF`（通常是 -1）。
2. 前者先做整数除法再转 `double`，小数已丢失；后者先把 `a` 转成 `double`，再做浮点除法，保留小数。
3. 展开为 `1 + 2 * 1 + 2`，结果是 `5` 而不是 `9`。应写成 `((x) * (x))`，或改用函数。
4. 由实现定义：x86 上的 gcc 通常是有符号，ARM 上常为无符号。需要确定性时用 `signed char`、`unsigned char` 或 `uint8_t`。
5. `x` 先整数提升为 `int`，所以结果是 `int` 类型的 `300`，不会回绕到 44。

</details>

**进入下一层的标准**：能说清整数提升和常规算术转换的规则，写出的程序无编译警告

### L3 熟练
**一句话定义**：能识别并避开整数溢出、浮点误差、非法移位等未定义行为，熟练用位掩码、`enum`、`typedef`、`static` 组织可靠的代码。

**核心概念**：
- 有符号整数溢出是未定义行为，无符号整数溢出则按模回绕
- 浮点数是二进制近似值，比较要用容差而不是 `==`
- 位掩码：用 `|` 置位、`& ~` 清位、`&` 测试位，常配合 `enum` 定义标志
- `typedef` 给类型起别名，`enum` 给整数常量起名字
- `static`：用在函数内表示生命周期贯穿程序，用在文件级表示仅本文件可见

**知识点详解**：
- 整数溢出与未定义行为：`INT_MAX + 1` 在标准里没有规定结果，编译器可以假设它不会发生，并据此删除你的检查。所以必须在运算之前判断，而不是之后。
- 浮点精度：`0.1` 无法用二进制精确表示，`0.1 + 0.2` 并不严格等于 `0.3`。比较时用 `|a - b| < eps`，容差大小取决于数值量级和业务要求。
- 位掩码操作：一个整数的每个位可表示一个开关。`flags |= MASK` 置位，`flags &= ~MASK` 清位，`flags & MASK` 测试。掩码常量用无符号类型，避免符号位带来的麻烦。
- `enum`：让常量有名字且集中管理，比散落的 `#define` 更易调试。枚举常量的类型是 `int`，不要把它当作严格的类型安全机制。
- `typedef`：把 `struct` 或复杂类型简化为一个名字，也方便将来统一修改底层类型。
- `static` 与作用域/生命周期：函数内的 `static` 变量只初始化一次，调用之间保持值；文件级的 `static` 函数和变量限制了可见范围，避免命名冲突。

**需要掌握**：整数溢出与未定义行为；浮点精度（不能直接用 `==` 比较）；位掩码操作；`enum`；`typedef`；`static` 与作用域/生命周期

**真实例子**：

```c
#include <limits.h>
#include <stdbool.h>
#include <stdint.h>
#include <stdio.h>

typedef enum {
    PERM_READ  = 1 << 0,
    PERM_WRITE = 1 << 1,
    PERM_EXEC  = 1 << 2
} Perm;

static int next_id(void) {
    static int id = 0;                              /* 只初始化一次，生命周期贯穿程序 */
    return ++id;
}

static bool add_overflows(int a, int b) {           /* 加法之前检查，避免有符号溢出 */
    return (b > 0 && a > INT_MAX - b) || (b < 0 && a < INT_MIN - b);
}

static int popcount32(uint32_t x) {
    int c = 0;
    while (x) { x &= x - 1; c++; }                  /* 每次清掉最低位的 1 */
    return c;
}

static bool nearly_equal(double a, double b) {
    double d = a > b ? a - b : b - a;
    return d < 1e-9;
}

int main(void) {
    unsigned perms = PERM_READ | PERM_WRITE;
    perms |= PERM_EXEC;                             /* 置位 */
    perms &= ~(unsigned)PERM_WRITE;                 /* 清位 */
    printf("读=%d 写=%d 执行=%d\n", (perms & PERM_READ) != 0,
           (perms & PERM_WRITE) != 0, (perms & PERM_EXEC) != 0);

    printf("INT_MAX + 1 会溢出吗? %s\n", add_overflows(INT_MAX, 1) ? "会" : "不会");

    double x = 0.1 + 0.2;
    printf("x == 0.3 ? %d, nearly_equal ? %d\n", x == 0.3, nearly_equal(x, 0.3));
    printf("popcount(0xF0F0) = %d\n", popcount32(0xF0F0u));
    int id1 = next_id(), id2 = next_id(), id3 = next_id(); /* 分开求值，顺序确定 */
    printf("id: %d %d %d\n", id1, id2, id3);
    return 0;
}
```

预期输出依次为：`读=1 写=0 执行=1`，`INT_MAX + 1 会溢出吗? 会`，`x == 0.3 ? 0, nearly_equal ? 1`，`popcount(0xF0F0) = 8`，`id: 1 2 3`。

**常见错误**：有符号溢出被编译器优化掉检查；`float` 累加误差；移位量超过类型宽度；用 `==` 比较浮点数；对负数做左移。

错误写法与正确写法对照：

```c
/* 错误：有符号溢出是未定义行为，编译器可能把整个判断优化成恒为假 */
if (a + b < a) { /* 想检测 b > 0 时的溢出 */ }

/* 错误：移位量 >= 类型宽度，未定义行为 */
uint32_t v = 1u << 32;

/* 正确：在运算前检查；移位量先限定范围 */
if (b > 0 && a > INT_MAX - b) { /* 会溢出 */ }
uint32_t w = (n < 32) ? (1u << n) : 0;
```

**应该练习**：用位运算实现标志位管理；写安全的加法溢出检测；统计整数中 1 的个数

**使用前检查清单**：
- [ ] 所有有符号整数加减乘运算，已确认不会溢出，或在运算前检查
- [ ] 浮点比较使用了容差，而不是 `==`
- [ ] 移位量大于等于 0 且小于类型宽度，被移位的数是无符号数
- [ ] 位掩码常量使用无符号类型，清位时写成 `& ~MASK`
- [ ] 仅供本文件使用的函数和全局变量都加了 `static`
- [ ] 用 `-fsanitize=undefined` 运行过测试

**自测小问题**：
1. 有符号整数溢出与无符号整数溢出，行为上有什么区别？
2. 为什么 `0.1 + 0.2 == 0.3` 通常为假？
3. `flags &= ~MASK` 的作用是什么？
4. 函数内 `static int n = 0;` 与普通局部变量有什么不同？
5. 怎样用 UBSan 检查未定义行为？

<details><summary>参考答案</summary>

1. 有符号溢出是未定义行为，编译器可以做任何假设；无符号溢出有明确定义，按 2 的 N 次方取模回绕。
2. 0.1、0.2、0.3 都不能用二进制精确表示，相加后有微小误差，所以与 0.3 的最近似值不完全相同。应使用容差比较。
3. 把 `MASK` 中为 1 的位在 `flags` 里清零，其他位保持不变。
4. `static` 局部变量只初始化一次，生存期贯穿整个程序，多次调用之间保持值；普通局部变量每次进入函数都重新创建。
5. 编译时加 `-fsanitize=undefined`（可配合 `-g`），运行时遇到溢出、非法移位等问题会打印诊断信息。

</details>

**进入下一层的标准**：能判断一段代码是否含未定义行为，并用工具（`-fsanitize=undefined`）验证

### L4 进阶
**一句话定义**：理解数据在内存中如何排布，能分辨标准保证与平台细节，并知道编译器优化对代码行为的影响。

**核心概念**：
- 对齐与填充：每个类型有对齐要求，`struct` 成员间会插入填充字节
- 字节序：多字节整数在内存里的排列顺序，分大端和小端
- 别名规则：通过不兼容类型的指针访问同一对象是未定义行为，`memcpy` 是合法的替代
- `volatile` 与 `restrict`：前者禁止优化掉访问，后者承诺指针不重叠
- 行为分类：未定义、未指定、实现定义，三类的责任完全不同

**知识点详解**：
- 内存布局与对齐、`struct` 填充：CPU 访问对齐的数据更快，有些平台甚至要求对齐。编译器因此在成员间补齐字节，`struct` 的总大小也会被补成最大对齐的倍数。成员按从大到小排列可以减少浪费，用 `offsetof` 和 `alignof` 查看实际布局。
- 大小端：小端把低位字节放在低地址（x86、多数 ARM），大端相反。处理网络协议和文件格式时要显式转换，不要直接把结构体内存写出去。
- `volatile`：告诉编译器这个对象可能被程序之外的因素改变（硬件寄存器、信号处理），每次访问都必须真实执行。它不保证线程同步，也不是原子操作。
- `restrict`：程序员向编译器保证，在该指针生命周期内，通过它访问的对象不会被其他指针访问。违反承诺是未定义行为，守住承诺则能换来向量化等优化。
- 编译器优化对行为的影响：优化只保证“合法程序”的可观察行为不变。含未定义行为的代码在 `-O0` 和 `-O2` 下可能表现不同，这正是排查此类问题的线索。
- 读汇编输出（`gcc -S`）：`gcc -O2 -S foo.c` 生成 `foo.s`，可以确认某个检查是否被删除、循环是否被展开，用事实代替猜测。

**需要掌握**：内存布局与对齐、`struct` 填充；大小端；`volatile`；`restrict`；了解编译器优化对行为的影响；读汇编输出（`gcc -S`）

**真实例子**：

```c
#include <stdalign.h>
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <string.h>

struct A { char c; int i; char d; };                /* 中间和末尾有填充 */
struct B { int i; char c; char d; };                /* 按大小重排后更紧凑 */

_Static_assert(sizeof(float) == sizeof(uint32_t), "float 必须是 32 位");

static int is_little_endian(void) {
    uint16_t v = 1;
    unsigned char first;
    memcpy(&first, &v, 1);                          /* 读取最低地址的字节 */
    return first == 1;
}

static uint32_t float_bits(float f) {
    uint32_t u;
    memcpy(&u, &f, sizeof u);                       /* 合法的类型双关 */
    return u;
}

static void add_arrays(int *restrict dst, const int *restrict src, size_t n) {
    for (size_t k = 0; k < n; k++) dst[k] += src[k]; /* restrict：承诺两块内存不重叠 */
}

int main(void) {
    printf("小端? %s\n", is_little_endian() ? "是" : "否");
    printf("sizeof(A)=%zu offsetof(A,i)=%zu sizeof(B)=%zu alignof(double)=%zu\n",
           sizeof(struct A), offsetof(struct A, i), sizeof(struct B), alignof(double));
    printf("1.0f 的位模式 = 0x%08X\n", (unsigned)float_bits(1.0f));

    volatile int reg = 42;                          /* 每次访问都真正读内存 */
    int snapshot = reg;
    int dst[3] = {1, 2, 3}, src[3] = {10, 20, 30};
    add_arrays(dst, src, 3);
    printf("snapshot=%d dst=%d,%d,%d\n", snapshot, dst[0], dst[1], dst[2]);
    return 0;
}
```

典型 x86-64 上的预期输出：`小端? 是`，`sizeof(A)=12 offsetof(A,i)=4 sizeof(B)=8 alignof(double)=8`，`1.0f 的位模式 = 0x3F800000`，`snapshot=42 dst=11,22,33`。

**常见错误**：把依赖具体平台的行为当成标准；强制类型转换破坏严格别名规则；直接把 `struct` 按字节写入文件或网络；以为 `volatile` 能保证多线程安全。

错误写法与正确写法对照：

```c
/* 错误：用 uint32_t* 读 float 对象，违反严格别名规则，-O2 下可能得到错误结果 */
float f = 1.0f;
uint32_t bits = *(uint32_t *)&f;

/* 正确：用 memcpy 拷贝字节，编译器通常会优化成一条指令 */
uint32_t bits2;
memcpy(&bits2, &f, sizeof bits2);
```

**应该练习**：写函数检测大小端；用 `memcpy` 安全地做类型双关；对比 `-O0` 与 `-O2` 的汇编

**使用前检查清单**：
- [ ] 需要依赖的类型大小、对齐，已用 `_Static_assert` 在编译期检查
- [ ] 没有通过不兼容的指针类型读写同一对象
- [ ] 跨平台传输的数据，显式指定了字节序，没有直接写出 `struct`
- [ ] `volatile` 只用于硬件寄存器、信号处理等场景，并没有拿来替代同步
- [ ] 使用 `restrict` 的函数，调用处确认了参数不重叠
- [ ] 怀疑优化问题时，对比过 `-O0` 与 `-O2` 的行为或汇编

**自测小问题**：
1. `struct { char a; int b; char c; }` 在常见平台上为什么是 12 字节？
2. 0x12345678 在小端机器上，最低地址的字节是什么？
3. 为什么推荐用 `memcpy` 做类型双关，而不是指针强转？
4. `volatile` 能保证操作的原子性吗？
5. “未定义”“未指定”“实现定义”三者各举一个例子。

<details><summary>参考答案</summary>

1. `a` 占 1 字节，后补 3 字节使 `b` 对齐到 4；`b` 占 4 字节，`c` 占 1 字节，末尾再补 3 字节使总大小是 4 的倍数，共 1+3+4+1+3=12。
2. `0x78`。小端把低位字节放在低地址。
3. 指针强转违反严格别名规则，是未定义行为；`memcpy` 按字节拷贝，是合法的，现代编译器会将其优化为直接寄存器移动，不会有额外开销。
4. 不能。`volatile` 只保证每次访问都真实发生，不保证原子性和线程间的顺序，多线程应使用 `<stdatomic.h>` 或互斥锁。
5. 未定义：有符号整数溢出。未指定：函数参数的求值顺序（实现无需文档说明）。实现定义：`char` 是否有符号、对负数右移的结果（实现必须文档说明）。

</details>

**进入下一层的标准**：能区分“未定义”“未指定”“实现定义”三类行为并各举例

### L5 独立
**一句话定义**：能为团队制定类型与编码规范，写出跨平台、跨编译器可移植的代码，并用静态分析工具保证质量。

**核心概念**：
- 类型选择：位宽重要用定宽整数，大小与下标用 `size_t`，指针差用 `ptrdiff_t`
- 可移植性：不假设 `int` 宽度、字节序、`char` 符号性，用 `_Static_assert` 把假设写成检查
- 格式化可移植：用 `<inttypes.h>` 的 `PRIu64` 等宏，`%zu`/`%td` 对应 `size_t`/`ptrdiff_t`
- 规范与工具：编码规范落地到编译选项、`clang-tidy`、`cppcheck` 和持续集成
- 谨慎用宏：能用 `const`、`enum`、`static inline` 的地方就不要用宏

**知识点详解**：
- 制定团队编码规范：规范要具体到可检查，例如“对外接口用定宽类型”“所有警告视为错误”“宏必须大写并加括号”。能被工具自动检查的规则才会长期有效，其余写进评审清单。
- 选择合适类型：`size_t` 是对象大小和数组下标的自然类型，无符号；`ptrdiff_t` 是两个指针之差，有符号；协议和文件格式用 `uint32_t` 这类定宽类型。类型选错常常是溢出和截断的来源。
- 跨平台可移植性设计：Windows 的 `long` 是 32 位，Linux 64 位下是 64 位；`char` 符号性、字节序、对齐也不同。把平台假设集中在一处，用 `_Static_assert` 在编译期暴露问题。
- 静态分析工具：`clang-tidy` 和 `cppcheck` 能在不运行程序的情况下找出空指针、越界、未初始化等问题。配合 `-Wall -Wextra -Wconversion` 与 sanitizer，形成多层防线。

**需要掌握**：制定团队编码规范；选择合适类型（定宽整数、`size_t`、`ptrdiff_t`）；跨平台可移植性设计；静态分析工具（`clang-tidy`、`cppcheck`）

**真实例子**：

```c
#include <inttypes.h>
#include <limits.h>
#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

_Static_assert(CHAR_BIT == 8, "本库假定一个字节是 8 位");

typedef struct {
    uint32_t *data;
    size_t len;
} U32Array;

static bool u32array_init(U32Array *a, size_t len) {
    if (len == 0 || len > SIZE_MAX / sizeof *a->data) return false; /* 先防乘法溢出 */
    a->data = calloc(len, sizeof *a->data);
    if (a->data == NULL) return false;
    a->len = len;
    return true;
}

static void u32array_free(U32Array *a) {
    free(a->data);
    a->data = NULL;
    a->len = 0;
}

static uint64_t u32array_sum(const U32Array *a) {
    uint64_t sum = 0;                               /* 用 64 位累加，避免 32 位溢出 */
    for (size_t i = 0; i < a->len; i++) sum += a->data[i];
    return sum;
}

int main(void) {
    U32Array arr;
    if (!u32array_init(&arr, 1000)) {
        fputs("分配失败\n", stderr);
        return EXIT_FAILURE;
    }
    for (size_t i = 0; i < arr.len; i++) arr.data[i] = (uint32_t)(i + 1);

    ptrdiff_t dist = &arr.data[arr.len - 1] - &arr.data[0];
    printf("sum = %" PRIu64 ", 首尾距离 = %td 个元素\n", u32array_sum(&arr), dist);
    printf("sizeof(size_t) = %zu 字节\n", sizeof(size_t));

    U32Array huge;
    printf("超大分配: %s\n", u32array_init(&huge, SIZE_MAX) ? "成功" : "被拒绝");
    u32array_free(&arr);
    return EXIT_SUCCESS;
}
```

预期输出：`sum = 500500, 首尾距离 = 999 个元素`，`sizeof(size_t) = 8 字节`（32 位平台为 4），`超大分配: 被拒绝`。

**常见错误**：过度依赖宏；忽视可移植性，换平台就崩；假定 `long` 或 `int` 的宽度；用 `int` 保存 `size_t`，造成截断；打印 `size_t` 时用 `%d` 或 `%lu`。

错误写法与正确写法对照：

```c
/* 错误：size 是 size_t，在 64 位平台上 %d 与 %lu 都不可移植；乘法也可能溢出 */
printf("%d\n", size);
void *p = malloc(count * sizeof(int));

/* 正确：使用 %zu；分配前检查乘法溢出（或直接使用 calloc） */
printf("%zu\n", size);
if (count <= SIZE_MAX / sizeof(int)) { void *q = malloc(count * sizeof(int)); free(q); }
```

**应该练习**：为一个小库写类型与常量规范文档；在 Windows 与 Linux 上都编译通过同一份代码

**使用前检查清单**：
- [ ] 对外接口的整数宽度明确：协议与文件格式用定宽类型，大小和下标用 `size_t`
- [ ] 所有格式化输出使用了 `%zu`、`%td` 或 `PRIu64` 等可移植格式
- [ ] 平台假设（`CHAR_BIT`、类型大小）都写成了 `_Static_assert`
- [ ] 分配大小计算前检查了乘法溢出，或使用 `calloc`
- [ ] `clang-tidy`、`cppcheck` 已接入构建或持续集成，且结果清零
- [ ] 至少在两个平台、两个编译器上以 `-Wall -Wextra -Wconversion` 编译过

**自测小问题**：
1. 什么场景应该使用 `size_t`，什么场景应该使用 `uint32_t`？
2. 为什么打印 `uint64_t` 要用 `PRIu64`，而不是 `%lu`？
3. `ptrdiff_t` 与 `size_t` 的区别是什么？
4. `_Static_assert` 与运行时 `assert` 的区别？
5. 编码规范如何避免沦为一纸空文？

<details><summary>参考答案</summary>

1. `size_t` 用于对象大小、数组长度与下标，它的宽度匹配平台地址空间；`uint32_t` 用于需要精确 32 位的场景，比如协议字段、文件格式和哈希值。
2. `uint64_t` 在不同平台上对应 `unsigned long` 或 `unsigned long long`，`%lu` 在 Windows 64 位上会出错。`PRIu64` 由实现展开为正确的格式串。
3. `ptrdiff_t` 是有符号类型，表示两个指针之差；`size_t` 是无符号类型，表示对象大小。
4. `_Static_assert` 在编译期检查常量表达式，失败则不能编译，没有运行开销；`assert` 在运行时检查，可被 `NDEBUG` 关闭。
5. 把规则落实到编译选项、静态分析工具与持续集成中，让违反规范的代码无法合入，并在评审清单中补充工具查不到的部分。

</details>

**达标标准**：代码在两个以上平台、两个以上编译器下零警告通过



---

## 第 2 章 控制流与函数

### L1 入门

**一句话定义**：能用分支、循环和简单函数把一个小问题的解法写成可运行的 C 程序。

**核心概念**：
- 分支：`if/else` 与 `switch` 根据条件选择执行路径，条件为 0 即假，非 0 即真。
- 循环：`for`（次数明确）、`while`（先判断）、`do-while`（先执行一次再判断）。
- 跳转：`break` 结束整个循环，`continue` 跳过本轮剩余语句进入下一轮。
- 函数：把一段逻辑起个名字，通过参数接收输入，通过返回值交出结果。
- 拆分思路：每个函数只做一件事，`main` 只负责串联。

**知识点详解**：
- `if/else`：按条件二选一或多选一。C 里没有独立的布尔类型要求，任何非零值都算真，所以条件表达式要写清楚，避免把赋值写成比较。
- `for`、`while`、`do-while`：三者可以互相改写。已知次数用 `for`，条件驱动用 `while`，至少执行一次（如菜单、输入校验）用 `do-while`。
- `break`/`continue`：`break` 只跳出最内层循环（或 `switch`），`continue` 只跳过本轮。它们能减少嵌套，但滥用会让流程难读。
- 定义并调用函数：函数由返回类型、名字、参数列表和函数体组成。拆函数是控制复杂度的第一步，也方便单独测试。
- 返回值与参数：参数是调用者传入的数据，返回值是函数交回的结果。没有返回值写 `void`，无参数写 `(void)`。

**需要掌握**：`if/else`；`for`、`while`、`do-while`；`break`/`continue`；定义并调用函数；返回值与参数

**真实例子**：下面的程序用函数判断素数，在循环中用 `continue` 跳过非素数，并统计个数。

```c
#include <stdio.h>

/* 判断 n 是否为素数：是返回 1，否返回 0 */
int is_prime(int n) {
    if (n < 2) return 0;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) return 0;
    }
    return 1;
}

int main(void) {
    int count = 0;
    for (int n = 1; n <= 30; n++) {
        if (!is_prime(n)) continue;   /* 非素数直接跳过 */
        printf("%d ", n);
        count++;
    }
    printf("\n共 %d 个\n", count);
    return 0;
}
```

预期输出：第一行为 `2 3 5 7 11 13 17 19 23 29 `，第二行为 `共 10 个`。

**常见错误**：
- `if (a = b)` 误用赋值：这会把 `b` 赋给 `a`，再以 `a` 的新值作为条件。
- 循环条件写错导致死循环：例如循环变量没有朝终止方向变化。
- `switch` 漏写 `break`：会继续执行下一个 `case`（贯穿）。
- `for` 后误加分号：分号成为空循环体，真正的代码块只执行一次。

```c
/* 错误：赋值代替比较，且 for 后多了分号 */
if (a = 5) { puts("a 等于 5"); }          /* a 被改成 5，条件恒为真 */
for (int i = 0; i < 3; i++);              /* 空循环体 */
{ printf("%d\n", i); }                    /* 这里 i 已经不在作用域内 */

/* 正确 */
if (a == 5) { puts("a 等于 5"); }         /* 常量写左边 5 == a 也能防手误 */
for (int i = 0; i < 3; i++) {
    printf("%d\n", i);
}
```

**应该练习**：九九乘法表；求 1 到 n 的和；判断素数；打印菱形

**使用前检查清单**：
- [ ] 条件表达式里的比较用的是 `==`，不是 `=`。
- [ ] 每个循环都能说清楚“什么时候结束”，循环变量朝终止方向变化。
- [ ] `if`、`for`、`while` 后面都用了花括号，且没有多余的分号。
- [ ] 每个 `switch` 的 `case` 都有 `break`，或明确注释了有意贯穿。
- [ ] 有返回值的函数每条路径都有 `return`，`main` 以 `return 0` 结束。
- [ ] 函数名能说明它做什么，一个函数不超过一屏。

**自测小问题**：
1. `while` 和 `do-while` 的区别是什么？
2. `break` 放在两层嵌套的 `for` 里，会跳出几层循环？
3. 下面的代码输出什么？`for (int i = 0; i < 3; i++); printf("x");`
4. `int f(void)` 与 `int f()` 在声明参数上有什么区别？
5. 判断素数时，为什么循环条件可以写成 `i * i <= n` 而不必遍历到 `n - 1`？

<details><summary>参考答案</summary>

1. `while` 先判断再执行，可能一次都不执行；`do-while` 先执行一次再判断，至少执行一次。
2. 只跳出最内层的一层循环。
3. 只输出一次 `x`，因为 `for` 后的分号是空循环体，`printf` 在循环结束后执行。
4. `int f(void)` 明确表示不接受参数；`int f()` 在 C 中表示参数未指定（旧式声明），不会做参数个数检查。
5. 如果 `n` 有大于 `sqrt(n)` 的因子，那么必然有一个小于 `sqrt(n)` 的配对因子，所以检查到 `sqrt(n)` 即可。

</details>

**进入下一层的标准**：能独立把一个中等逻辑拆成几个函数完成

### L2 基础

**一句话定义**：理解函数调用时数据如何传递、变量活多久，并能写出正确的递归和带命令行参数的程序。

**核心概念**：
- 函数原型：在使用前告诉编译器函数的名字、参数和返回类型。
- 值传递：调用时实参被复制给形参，函数内改形参不影响调用者。
- 作用域与生存期：局部变量随调用创建和销毁，全局变量与 `static` 局部变量存活到程序结束。
- 递归：函数调用自己，必须有终止条件，并且每次向终止条件靠近。
- `argc/argv`：`argc` 是参数个数（含程序名），`argv` 是字符串数组。

**知识点详解**：
- 函数原型声明：C 编译器按从上到下的顺序读代码，调用在定义之前时需要先声明。原型通常放在头文件里，让多个源文件共享同一份约定。
- 值传递：C 只有值传递。想让函数修改调用者的变量，必须传指针（第 3 章展开），而不是指望形参影响实参。
- 递归：把大问题化成同类的小问题。它写起来简洁，但每层调用都占栈空间，深度过大会栈溢出。
- 局部与全局变量：局部变量放在栈上，进入函数时创建、返回时销毁，未初始化时值不确定。全局变量默认初始化为 0，但会让函数之间产生隐式依赖，应尽量少用。
- `static` 局部变量：只初始化一次，值在多次调用之间保留，但作用域仍限于函数内。适合做计数器或缓存。
- `main` 的 `argc/argv`：通过它接收命令行输入。`argv[0]` 通常是程序名，`argv[argc]` 为 `NULL`，参数都是字符串，需要用 `strtol` 等转换成数字。

**需要掌握**：函数原型声明；值传递；递归；局部与全局变量；`static` 局部变量；`main` 的 `argc/argv`

**真实例子**：下面的程序演示原型声明、递归（阶乘与最大公约数）、`static` 局部变量和命令行参数求和。

```c
#include <stdio.h>
#include <stdlib.h>

long fact(int n);                        /* 函数原型声明 */
int gcd(int a, int b);

static int call_count = 0;               /* 文件内全局变量 */

long fact(int n) {
    call_count++;
    if (n <= 1) return 1;                /* 终止条件 */
    return n * fact(n - 1);
}

int gcd(int a, int b) { return b == 0 ? a : gcd(b, a % b); }

int next_id(void) {
    static int id = 0;                   /* 只初始化一次，调用间保留 */
    return ++id;
}

int main(int argc, char *argv[]) {
    long sum = 0;
    for (int i = 1; i < argc; i++) sum += strtol(argv[i], NULL, 10);
    long f = fact(5);                    /* 先求值，再读 call_count */
    printf("fact(5)=%ld，递归调用 %d 次\n", f, call_count);
    printf("gcd(48,18)=%d\n", gcd(48, 18));
    int a = next_id(), b = next_id(), c = next_id();
    printf("id: %d %d %d\n", a, b, c);
    printf("参数之和=%ld\n", sum);
    return 0;
}
```

预期输出：`fact(5)=120，递归调用 5 次`、`gcd(48,18)=6`、`id: 1 2 3`；不带参数运行时 `参数之和=0`，运行 `./a.out 1 2 3` 时为 `参数之和=6`。

**常见错误**：
- 函数使用前未声明：新标准（C99 起）不再允许隐式声明，编译器会报错或警告。
- 递归缺少终止条件：调用永不返回，最终栈溢出。
- 以为函数能改变实参的值：形参只是副本。

```c
/* 错误：swap 交换的只是副本，main 里的 x、y 不变 */
void swap(int a, int b) { int t = a; a = b; b = t; }

/* 正确：传入地址，通过指针修改调用者的变量 */
void swap_ok(int *a, int *b) { int t = *a; *a = *b; *b = t; }
/* 调用：swap_ok(&x, &y); */
```

**应该练习**：阶乘、斐波那契（递归与迭代对比）；最大公约数；命令行参数求和

**使用前检查清单**：
- [ ] 每个函数在被调用前都有原型或完整定义。
- [ ] 每个递归函数都写出了终止条件，且每次调用都在向它靠近。
- [ ] 清楚哪些参数是值传递，哪些需要传指针才能被修改。
- [ ] 局部变量在使用前都已初始化。
- [ ] 尽量不用全局变量，必须用时限定为 `static` 并集中管理。
- [ ] 使用 `argv` 前检查了 `argc`，转换数字时处理了无效输入。

**自测小问题**：
1. 为什么说 C 语言只有值传递？
2. `static int n = 0;` 写在函数内部和写在函数外部，含义有什么不同？
3. 递归计算 `fib(40)` 为什么会很慢？如何改进？
4. 运行 `./prog a b c` 时，`argc` 和 `argv[0]` 分别是什么？
5. 一个递归函数忘记写终止条件，程序最终会怎样？

<details><summary>参考答案</summary>

1. 调用时实参的值被复制给形参，函数只能操作副本。传指针时复制的是地址值，这也是值传递，只是通过地址间接修改了原对象。
2. 写在函数内：作用域限于该函数，值在多次调用间保留。写在函数外：作用域是整个文件，且 `static` 使其对其他源文件不可见（内部链接）。
3. 朴素递归会重复计算大量子问题，时间近似指数增长。可改为迭代，或用数组缓存已算过的结果（记忆化）。
4. `argc` 为 4，`argv[0]` 通常是程序名（如 `./prog`）。
5. 栈帧不断累积，直到栈空间耗尽，程序因栈溢出崩溃（如段错误）。

</details>

**进入下一层的标准**：能解释调用栈如何工作，并画出一次递归调用的栈帧变化

### L3 熟练

**一句话定义**：能把函数当作数据传递，设计带回调的通用接口，并用统一的约定报告和处理错误。

**核心概念**：
- 函数指针：保存函数地址的变量，可存入数组、作为参数传递，实现分发表与回调。
- 回调：调用者提供一个函数，库在合适的时机调用它，例如 `qsort` 的比较函数。
- 可变参数：`stdarg.h` 的 `va_list`、`va_start`、`va_arg`、`va_end` 让函数接收不定个数的参数。
- 错误返回约定：用返回码表示成败，用 `errno` 或自定义枚举说明原因。
- 断言：`assert` 检查“不应该发生”的程序员错误，与运行时可预期的错误区分开。

**知识点详解**：
- 函数指针与回调：用 `typedef` 给函数指针类型起别名，写起来更清晰。函数指针让“做什么”与“怎么做”解耦，是实现分发表、事件处理和排序策略的基础。
- 可变参数 `stdarg.h`：编译器无法检查可变部分的类型，调用者和被调用者必须约定好个数与类型。常见做法是第一个参数给出个数，或像 `printf` 那样由格式串描述。
- 错误返回约定：C 没有异常，错误只能靠返回值传递。约定要统一，例如“成功返回 0，失败返回 -1 并设置 `errno`”，并在文档里写明。
- `errno`：标准库函数失败时设置的全局错误码（线程各自一份）。只有在函数报告失败后读取才有意义，成功时它的值不确定。
- `assert`：条件为假时打印位置并终止程序，用于抓前置条件被破坏的 bug。定义 `NDEBUG` 后断言被移除，所以断言里不能放有副作用的表达式。
- 尾递归与栈深度：尾递归指递归调用是函数的最后一步。C 标准不保证编译器做尾调用优化，所以深度不可控时应改写为循环。

**需要掌握**：函数指针与回调；可变参数 `stdarg.h`；错误返回约定（返回码、`errno`）；`assert`；尾递归与栈深度

**真实例子**：下面的程序用函数指针做运算分发表，用返回码和 `errno` 报告除零，并演示 `qsort` 回调与可变参数求和。

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <stdarg.h>

typedef int (*binop)(int, int, int *);   /* 成功返回 0，失败返回 -1 */

static int add(int a, int b, int *out) { *out = a + b; return 0; }
static int divide(int a, int b, int *out) {
    if (b == 0) { errno = EDOM; return -1; }
    *out = a / b;
    return 0;
}
static int cmp_int(const void *pa, const void *pb) {
    int a = *(const int *)pa, b = *(const int *)pb;
    return (a > b) - (a < b);            /* 避免相减溢出 */
}
static int sum_ints(int n, ...) {        /* 可变参数：n 为个数 */
    va_list ap; int s = 0;
    va_start(ap, n);
    for (int i = 0; i < n; i++) s += va_arg(ap, int);
    va_end(ap);
    return s;
}

int main(void) {
    struct { char op; binop fn; } table[] = { {'+', add}, {'/', divide} };
    int r;
    for (size_t i = 0; i < sizeof table / sizeof table[0]; i++)
        if (table[i].fn(7, 0, &r) == 0) printf("7 %c 0 = %d\n", table[i].op, r);
        else printf("7 %c 0 失败，errno=%d\n", table[i].op, errno);
    int a[] = {5, 2, 9, 1};
    qsort(a, 4, sizeof a[0], cmp_int);
    printf("%d %d %d %d, sum=%d\n", a[0], a[1], a[2], a[3], sum_ints(3, 1, 2, 3));
    return 0;
}
```

预期输出：`7 + 0 = 7`，`7 / 0 失败，errno=` 后接 `EDOM` 对应的数值（因平台而异，常见为 33），最后一行 `1 2 5 9, sum=6`。

**常见错误**：
- 函数指针类型写错：参数或返回类型不匹配时，通过指针调用属于未定义行为。
- `va_arg` 类型与实际传入不符：例如传入 `double` 却按 `int` 读取，结果不可预料。
- 忽略函数返回的错误码：失败后继续使用无效结果。

```c
/* 错误：比较函数用相减，大数相减会溢出；还忽略了 fopen 的返回值 */
int cmp_bad(const void *a, const void *b) {
    return *(const int *)a - *(const int *)b;     /* INT_MIN 与正数相减溢出 */
}
FILE *fp = fopen("data.txt", "r");
fscanf(fp, "%d", &n);                             /* fp 可能是 NULL */

/* 正确：用比较表达式，并检查返回值 */
int cmp_ok(const void *a, const void *b) {
    int x = *(const int *)a, y = *(const int *)b;
    return (x > y) - (x < y);
}
FILE *fp2 = fopen("data.txt", "r");
if (fp2 == NULL) { perror("fopen"); return 1; }
```

**应该练习**：用函数指针实现简易计算器分发表；自己写一个 `my_printf`；用 `qsort` 配合比较函数

**使用前检查清单**：
- [ ] 函数指针用 `typedef` 命名，调用前确认它不是 `NULL`。
- [ ] 回调函数的签名与接口要求完全一致（包括 `const` 和返回类型）。
- [ ] 可变参数函数写明了个数或格式的约定，且每次 `va_start` 都有对应的 `va_end`。
- [ ] 每个可能失败的调用都检查了返回值，`errno` 只在失败后立即读取。
- [ ] `assert` 只用于检查程序员错误，里面没有带副作用的表达式。
- [ ] 递归深度可能很大时，已改写成循环或确认了栈够用。

**自测小问题**：
1. 写出“指向接受两个 `int`、返回 `int` 的函数的指针”的声明，并用 `typedef` 简化。
2. `qsort` 的比较函数为什么参数是 `const void *`？
3. 为什么 `assert(fgetc(fp) != EOF);` 是危险写法？
4. `va_arg(ap, float)` 有什么问题？
5. 尾递归一定不会栈溢出吗？

<details><summary>参考答案</summary>

1. `int (*fp)(int, int);`，简化为 `typedef int (*binop)(int, int);` 后写 `binop fp;`。
2. 因为 `qsort` 对元素类型一无所知，只能用通用指针；比较函数内部再转换成真实类型。`const` 表示比较函数不应修改元素。
3. 定义 `NDEBUG` 的发布版本中断言会被整个移除，`fgetc` 也不会被调用，程序行为随之改变。
4. 可变参数部分的 `float` 会被提升为 `double`，所以应写 `va_arg(ap, double)`，用 `float` 是未定义行为。
5. 不一定。C 标准不保证尾调用优化，未优化时（如 `-O0`）每次调用仍会占用栈帧，深度大时仍会溢出。

</details>

**进入下一层的标准**：能设计一个带回调的接口，并写清错误处理约定

### L4 进阶

**一句话定义**：看得见函数调用在栈上的真实样子，并能用状态机、集中清理和防御式写法构建可靠的控制流。

**核心概念**：
- 栈帧：每次调用在栈上保存返回地址、参数、局部变量，调用约定规定了参数如何传递、由谁清理。
- 非局部跳转：`setjmp/longjmp` 可以一次跳回外层函数，但不会自动释放资源。
- `inline`：建议编译器把函数体展开到调用处，是宏函数的类型安全替代品。
- 状态机：用“当前状态 + 输入”决定下一步，把复杂的解析逻辑写得清晰可验证。
- 防御式编程：检查参数与边界，对不可能的情况也有明确的处理。

**知识点详解**：
- 调用约定与栈帧细节：调用约定决定参数放寄存器还是栈、返回值放哪里。理解它才能读懂调试器里的回溯和汇编，也能解释栈溢出和缓冲区越界为何会破坏返回地址。
- `setjmp/longjmp`：`setjmp` 保存当前执行位置，`longjmp` 回到该位置，相当于跨函数的 `goto`。它会绕过中间函数的清理代码，使用时要格外小心，并且 `setjmp` 所在函数返回后不能再 `longjmp`。
- 内联函数 `inline`：消除小函数的调用开销，同时保留类型检查和单次求值。是否真正展开由编译器决定，所以 `inline` 只是建议。
- 状态机设计：用枚举表示状态、用 `switch` 处理转移。协议解析、词法分析、按键处理都适合这种写法，状态和转移一目了然，易于测试。
- 防御式编程：不信任输入，检查指针、长度和返回值，失败时走统一的清理路径。常用 `goto cleanup` 模式在 C 里集中释放多个资源，避免遗漏。

**需要掌握**：调用约定与栈帧细节；`setjmp/longjmp`；内联函数 `inline`；状态机设计；防御式编程

**真实例子**：下面的程序用枚举状态机统计字符串中连续数字的段数，辅助判断用 `static inline` 函数实现。

```c
#include <stdio.h>
#include <ctype.h>

typedef enum { S_OUT, S_IN_NUM } state;

static inline int is_digit(char c) { return isdigit((unsigned char)c); }

/* 状态机：统计字符串里有多少段连续数字 */
int count_numbers(const char *s) {
    state st = S_OUT;
    int count = 0;
    for (; *s != '\0'; s++) {
        switch (st) {
        case S_OUT:
            if (is_digit(*s)) { st = S_IN_NUM; count++; }
            break;
        case S_IN_NUM:
            if (!is_digit(*s)) st = S_OUT;
            break;
        }
    }
    return count;
}

int main(void) {
    printf("%d\n", count_numbers("12 abc 7,x99"));   /* 3 */
    printf("%d\n", count_numbers("no digits"));      /* 0 */
    printf("%d\n", count_numbers(""));               /* 0 */
    return 0;
}
```

预期输出三行：`3`、`0`、`0`。

**常见错误**：
- `longjmp` 跳过资源释放：中间函数里已 `malloc` 的内存、已打开的文件都不会被清理。
- 递归过深栈溢出：处理用户可控的大输入时尤其危险。
- 宏函数的副作用（参数被多次求值）：宏只是文本替换。

```c
/* 错误：参数 i++ 被展开两次，行为未定义 */
#define SQUARE(x) ((x) * (x))
int i = 3;
int r1 = SQUARE(i++);              /* 相当于 ((i++) * (i++)) */

/* 正确：用 static inline 函数，参数只求值一次 */
static inline int square(int x) { return x * x; }
int j = 3;
int r2 = square(j++);              /* r2 = 9，之后 j = 4 */
```

**应该练习**：用状态机解析简单协议；用 `goto cleanup` 模式处理多资源释放；把深递归改写为显式栈

**使用前检查清单**：
- [ ] 能在调试器里用 `bt` 或调用栈窗口看到每一层的参数和局部变量。
- [ ] 用到 `longjmp` 时，确认被跳过的函数里没有未释放的资源。
- [ ] 宏函数的参数都加了括号，且调用时参数没有副作用；能用 `static inline` 就不用宏。
- [ ] 状态机的每个状态都处理了所有可能的输入，包括非法输入。
- [ ] 多资源的函数使用统一的 `goto cleanup` 出口，释放顺序与申请顺序相反。
- [ ] 递归深度由输入决定时，设置了深度上限或改成显式栈。

**自测小问题**：
1. 为什么 `longjmp` 可能造成资源泄漏？
2. `static inline` 比宏函数好在哪里？
3. `goto cleanup` 模式的基本写法是什么？为什么在 C 里被认为是合理用法？
4. 状态机用 `enum` + `switch` 实现有哪些好处？
5. 如何确认一次崩溃是栈溢出，而不是别的内存错误？

<details><summary>参考答案</summary>

1. `longjmp` 直接回到 `setjmp` 处，中间函数里后面的清理代码（`free`、`fclose` 等）都不会执行。
2. 有类型检查，参数只求值一次，可以在调试器里单步，也不会因运算优先级产生意外。
3. 函数开头把指针初始化为 `NULL`，出错时 `goto cleanup`，在末尾标签处按相反顺序释放（`free(NULL)` 是安全的）。它避免了每个出错分支重复写清理代码，也不易遗漏。
4. 状态和转移显式可见，容易补全和测试；新增状态时编译器可以对未处理的枚举值给出警告。
5. 在调试器中查看回溯：栈溢出通常表现为很深（成千上万层）的重复调用栈，崩溃地址靠近栈的边界；也可以用 `ulimit -s` 缩小栈来复现。

</details>

**进入下一层的标准**：能在调试器中单步观察栈，并定位栈溢出问题

### L5 独立

**一句话定义**：能独立设计清晰的模块接口，让函数职责单一、错误处理一致、契约写明，别人不看实现也能正确使用。

**核心概念**：
- 模块接口：头文件公开“做什么”，源文件隐藏“怎么做”。
- 单一职责：一个函数只做一件事，长度以一屏为宜。
- 统一错误策略：整个项目用同一种返回码与错误信息风格。
- 契约：用前置条件、后置条件和不变量描述函数行为。
- 文档注释：说明参数、返回值、所有权和失败方式。

**知识点详解**：
- 模块接口设计：接口应小而稳定，只暴露必要的类型和函数，内部细节用 `static` 隐藏。接口一旦被别人使用就很难修改，所以命名、参数顺序和所有权约定要先想清楚。
- 单一职责与函数长度控制：函数过长通常意味着混合了解析、计算、输出等多个职责。拆分后每个函数更易命名、测试和复用。
- 错误处理策略统一：同一模块里不要一部分返回 `-1`、一部分返回 `NULL`、一部分直接 `exit`。选定一种（如枚举状态码），并提供把状态码转成文字的函数。
- 前置/后置条件：前置条件是调用者必须保证的事，后置条件是函数承诺的结果。把它们写进注释，并用 `assert` 在调试阶段检查前置条件。

**需要掌握**：模块接口设计；单一职责与函数长度控制；错误处理策略统一；为函数写清前置/后置条件

**真实例子**：下面把一个容量为 4 的环形队列写成小模块（实际项目中接口放头文件、实现放源文件），统一用状态码报告错误，并为每个公共函数写出前置/后置条件。

```c
#include <stdio.h>
#include <assert.h>

#define RING_CAP 4

typedef enum { RING_OK = 0, RING_EMPTY, RING_FULL } ring_status;
typedef struct { int buf[RING_CAP]; size_t head, count; } ring;

/* 前置：r 非 NULL。后置：成功返回 RING_OK 且 v 追加到队尾；满则不改变 r。 */
ring_status ring_push(ring *r, int v) {
    assert(r != NULL);
    if (r->count == RING_CAP) return RING_FULL;
    r->buf[(r->head + r->count) % RING_CAP] = v;
    r->count++;
    return RING_OK;
}

/* 前置：r、out 非 NULL。后置：成功时 *out 为队首元素并移除；空则 *out 不变。 */
ring_status ring_pop(ring *r, int *out) {
    assert(r != NULL && out != NULL);
    if (r->count == 0) return RING_EMPTY;
    *out = r->buf[r->head];
    r->head = (r->head + 1) % RING_CAP;
    r->count--;
    return RING_OK;
}

const char *ring_strerror(ring_status s) {
    static const char *msg[] = { "ok", "empty", "full" };
    return msg[s];
}

int main(void) {
    ring r = {0};
    int v;
    for (int i = 1; i <= 5; i++)
        printf("push %d: %s\n", i, ring_strerror(ring_push(&r, i)));
    while (ring_pop(&r, &v) == RING_OK) printf("pop %d\n", v);
    printf("pop: %s\n", ring_strerror(ring_pop(&r, &v)));
    return 0;
}
```

预期输出：`push 1` 到 `push 4` 显示 `ok`，`push 5: full`；随后依次 `pop 1` 到 `pop 4`，最后 `pop: empty`。

**常见错误**：
- 函数承担过多职责：一个函数里既读输入、又计算、又打印，难以复用和测试。
- 每个模块错误处理风格不同：调用者要为每个模块记一套规则，容易漏处理。

```c
/* 错误：一个函数同时做解析、计算和输出，失败时直接 exit */
void process(const char *line) {
    int a, b;
    if (sscanf(line, "%d %d", &a, &b) != 2) exit(1);
    printf("%d\n", a + b);
}

/* 正确：解析、计算分开，由调用者决定怎么处理错误和输出 */
int parse_pair(const char *line, int *a, int *b) {   /* 成功返回 0 */
    return sscanf(line, "%d %d", a, b) == 2 ? 0 : -1;
}
int add_pair(int a, int b) { return a + b; }
```

**应该练习**：为一个 500 行以内的程序重构出清晰的函数层次；为公共函数补全文档注释

**使用前检查清单**：
- [ ] 每个公共函数都有注释，写明参数、返回值、前置/后置条件和所有权。
- [ ] 头文件只暴露必要的接口，内部函数用 `static` 隐藏。
- [ ] 同一模块的错误处理风格一致，并提供错误码转文字的函数。
- [ ] 库函数不在内部调用 `exit` 或直接打印错误，把决定权交给调用者。
- [ ] 每个函数只做一件事，名字能准确描述它，长度不超过一屏。
- [ ] 别人只读头文件和注释，就能写出正确的调用代码。

**自测小问题**：
1. 为什么库函数不应该在出错时直接调用 `exit`？
2. 前置条件和后置条件分别由谁负责保证？
3. 怎样判断一个函数是否承担了过多职责？
4. 为什么要给公共函数写明内存所有权（谁分配、谁释放）？
5. `assert` 适合用来检查用户输入是否合法吗？

<details><summary>参考答案</summary>

1. 调用者失去了处理错误的机会（重试、清理资源、给出提示），库也难以在不同程序中复用。应返回错误码，由调用者决定。
2. 前置条件由调用者保证，后置条件由函数实现保证。违反前置条件属于调用者的 bug。
3. 如果很难用一句话说清它做什么、名字里需要出现“和”，或者它混合了输入、计算、输出等层次，就应该拆分。
4. 所有权不清会造成内存泄漏或重复释放，这类约定无法从函数签名看出来，必须写在文档里。
5. 不适合。用户输入出错是可预期的运行时错误，应通过返回码处理；`assert` 只用于检查程序员的错误，且在发布版本中可能被移除。

</details>

**达标标准**：别人只看头文件和注释就能正确使用你的函数



---

## 第 3 章 数组与字符串

### L1 入门

**一句话定义**：能正确声明、初始化并遍历一维数组，理解 C 字符串就是以 `'\0'` 结尾的 `char` 数组，并始终守住下标边界。

**核心概念**：
- 数组是一块连续的、同类型元素的内存，长度在声明时确定。
- 下标从 0 开始，长度为 `n` 的数组合法下标是 `0 ~ n-1`。
- C 不做越界检查，越界访问是未定义行为（可能崩溃，也可能悄悄改坏别的数据）。
- 字符串是以 `'\0'` 结尾的 `char` 数组，存储长度比可见字符数多 1。
- `sizeof arr / sizeof arr[0]` 可在数组所在作用域内算出元素个数。

**知识点详解**：
- 一维数组声明与初始化：`int a[5] = {1, 2};` 未写出的元素自动补 0；省略长度时 `int a[] = {1, 2, 3};` 由初始化列表决定长度。这样写能让数组大小和数据保持一致。
- 下标访问：`a[i]` 等价于“从首地址偏移 `i` 个元素”，所以下标从 0 开始。理解这一点，就不会再把下标当作“第几个”。
- 用循环遍历：`for (int i = 0; i < n; i++)` 是标准写法，条件用 `<` 而不是 `<=`。多数越界错误都出在这个边界条件上。
- `char` 数组与 `'\0'`：`"abc"` 实际占 4 个字节，最后一个是 `'\0'`。`printf("%s")`、`strlen` 都靠它判断字符串结束，缺了它就会一直读到别处的内存。

**需要掌握**：一维数组声明与初始化；下标访问；用循环遍历；`char` 数组与 `'\0'`

**真实例子**：

```c
#include <stdio.h>

int main(void) {
    int scores[5] = {82, 95, 67, 74, 90};              // 合法下标 0~4
    int n = (int)(sizeof scores / sizeof scores[0]);   // 元素个数 = 5
    int max = scores[0];
    long sum = 0;
    for (int i = 0; i < n; i++) {                      // 用 < 而不是 <=
        if (scores[i] > max) max = scores[i];
        sum += scores[i];
    }
    printf("max=%d avg=%.1f\n", max, (double)sum / n);

    char word[] = "Hello";                             // 实际占 6 字节，含 '\0'
    int count_l = 0;
    for (int i = 0; word[i] != '\0'; i++) {            // 以 '\0' 作为结束条件
        if (word[i] == 'l') count_l++;
    }
    printf("%s has %d 'l', sizeof=%zu\n", word, count_l, sizeof word);
    return 0;
}
```

预期输出：`max=95 avg=81.6` 和 `Hello has 2 'l', sizeof=6`。

**常见错误**：下标从 1 开始；越界访问；数组长度用变量而不理解 VLA 限制
- VLA（变长数组）：C99 引入、C11 改为可选特性，不能初始化，过大时还可能栈溢出。初学阶段长度优先用常量或宏。
- 错误写法与正确写法对照：

```c
int a[5] = {1, 2, 3, 4, 5};
// 错误：<= 导致 a[5] 越界；写 a[5] 则是未定义行为
for (int i = 0; i <= 5; i++) printf("%d ", a[i]);
// 正确：下标范围 0 ~ n-1
for (int i = 0; i < 5; i++) printf("%d ", a[i]);

// 错误：长度 5 装不下结尾的 '\0'（C 允许这样写，但结果不是字符串）
char bad[5] = "hello";     // printf("%s", bad) 会读到数组之外
// 正确：让编译器决定长度
char good[] = "hello";
```

**应该练习**：求数组最大值/平均值；数组逆序；统计字符出现次数

**使用前检查清单**：
- [ ] 数组长度是否明确，并且用常量或 `sizeof` 表达式得到
- [ ] 所有循环的上界是否是 `i < n`
- [ ] 每次写 `a[i]` 前，能否说明 `i` 的取值范围
- [ ] 字符数组是否预留了 `'\0'` 的位置
- [ ] 局部数组是否已初始化，没有读取未初始化的元素
- [ ] 编译时是否打开了 `-Wall -Wextra`

**自测小问题**：
1. `int a[10];` 的合法下标范围是什么？
2. `char s[] = "abc";` 的 `sizeof s` 和 `strlen(s)` 分别是多少？
3. `int a[3] = {1};` 中 `a[1]` 和 `a[2]` 的值是多少？
4. 为什么遍历数组的条件通常写 `i < n` 而不是 `i <= n`？
5. 在 `main` 中定义的 `int a[8]`，如何计算元素个数？

<details><summary>参考答案</summary>

1. `0 ~ 9`，访问 `a[10]` 就越界。
2. `sizeof s` 是 4（含 `'\0'`），`strlen(s)` 是 3（不含）。
3. 都是 0；部分初始化时，未给出的元素补零。
4. 下标最大只能到 `n-1`，用 `<=` 会多访问一个元素，造成越界。
5. `sizeof a / sizeof a[0]`，结果为 8。

</details>

**进入下一层的标准**：能说出数组下标的合法范围并避免越界

### L2 基础

**一句话定义**：能处理二维数组和常用字符串函数，并在每次操作字符串、传递数组时都说清缓冲区大小。

**核心概念**：
- 二维数组按行连续存放，`m[i][j]` 先选行再选列，声明时列数不能省略。
- 数组作函数参数时会退化为指针，函数内部无法得知长度，必须另传 `size_t n`。
- `string.h` 的 `strcpy`、`strcat` 不检查目标空间，调用者必须自己保证够用。
- `fgets(buf, size, stdin)` 最多读 `size-1` 个字符并补 `'\0'`，是读行的标准选择。
- `gets` 已在 C11 中被移除，任何情况下都不要使用。

**知识点详解**：
- 二维数组：`int m[2][3]` 是 2 行 3 列，内存里依次是 `m[0][0..2]`、`m[1][0..2]`。传给函数时要写成 `int m[][3]`，因为编译器需要列数来计算偏移。
- `strlen` `strcpy` `strcmp` `strcat`：`strlen` 返回不含 `'\0'` 的长度；`strcpy` 和 `strcat` 把内容（含 `'\0'`）写入目标；`strcmp` 按字典序比较，相等返回 0，不能用 `==` 比较字符串。
- `fgets` 读行：它会保留行尾的 `'\n'`，需要时用 `strcspn(buf, "\n")` 找到位置并改成 `'\0'`。读不到数据时返回 `NULL`，要检查。
- 数组作参数同传长度：形参 `int a[]` 本质是 `int *a`，函数里的 `sizeof a` 只是指针大小。把长度作为单独参数传入，是避免越界的唯一可靠办法。

**需要掌握**：二维数组；`string.h` 常用函数（`strlen` `strcpy` `strcmp` `strcat`）；`fgets` 读行；数组作函数参数需同时传长度

**真实例子**：

```c
#include <stdio.h>
#include <string.h>

// 数组作参数：长度必须单独传入
static void bubble_sort(int a[], size_t n) {
    for (size_t i = 0; i + 1 < n; i++)
        for (size_t j = 0; j + 1 < n - i; j++)
            if (a[j] > a[j + 1]) { int t = a[j]; a[j] = a[j + 1]; a[j + 1] = t; }
}

int main(void) {
    int m[2][3] = {{1, 2, 3}, {4, 5, 6}};
    int t[3][2];                                  // 转置后 3 行 2 列
    for (int i = 0; i < 2; i++)
        for (int j = 0; j < 3; j++) t[j][i] = m[i][j];
    for (int i = 0; i < 3; i++) printf("%d %d\n", t[i][0], t[i][1]);

    int v[] = {5, 2, 9, 1, 7};
    size_t n = sizeof v / sizeof v[0];            // 只能在数组定义处计算
    bubble_sort(v, n);
    for (size_t i = 0; i < n; i++) printf("%d ", v[i]);
    putchar('\n');

    char buf[16];
    const char *src = "level", *suffix = "-2";
    if (strlen(src) + strlen(suffix) + 1 <= sizeof buf) {   // 先检查，再拷贝
        strcpy(buf, src);
        strcat(buf, suffix);
    }
    printf("%s %d\n", buf, strcmp(buf, "level-2") == 0);
    return 0;
}
```

预期输出：转置后三行 `1 4`、`2 5`、`3 6`，接着是 `1 2 5 7 9`，最后一行是 `level-2 1`。

**常见错误**：`gets` 的使用（已被移除）；在函数内对数组形参用 `sizeof`；`strcpy` 目标空间不足
- 另一个常见错误是用 `==` 比较两个字符串，比较的其实是地址，应使用 `strcmp`。
- 错误写法与正确写法对照：

```c
// 错误：形参 a 实际是 int *，sizeof a 只是指针大小（常见为 8），不是数组大小
void print_all(int a[]) {
    for (size_t i = 0; i < sizeof a / sizeof a[0]; i++) printf("%d ", a[i]);
}
// 正确：长度由调用者传入
void print_all_ok(const int a[], size_t n) {
    for (size_t i = 0; i < n; i++) printf("%d ", a[i]);
}

char name[4];
// 错误：gets(name);              已被移除，且无法限制长度
// 错误：strcpy(name, "Alice");   需要 6 字节，只有 4 字节，越界
// 正确：
if (fgets(name, sizeof name, stdin) != NULL) { /* 最多读 3 个字符 */ }
```

**应该练习**：冒泡/选择排序；二分查找；回文判断；矩阵转置

**使用前检查清单**：
- [ ] 每个接收数组的函数都带了长度参数
- [ ] 每次 `strcpy`/`strcat` 前，都核对过目标缓冲区大小（含 `'\0'`）
- [ ] 读取输入用 `fgets` 并检查返回值是否为 `NULL`
- [ ] 比较字符串用 `strcmp`，不用 `==`
- [ ] 二维数组作参数时写明了列数
- [ ] 处理 `fgets` 读入的行尾 `'\n'`

**自测小问题**：
1. 为什么在函数里对数组形参使用 `sizeof` 得不到元素个数？
2. `strcmp("a", "b")` 的返回值是正、负还是 0？如何判断两个字符串相等？
3. `char buf[8]; strcpy(buf, "12345678");` 有什么问题？
4. `fgets(buf, 8, stdin)` 最多能读入几个字符？行尾的换行符会怎样？
5. `int m[2][3]` 中 `m[1][0]` 之前有多少个元素？

<details><summary>参考答案</summary>

1. 数组形参会退化为指针，`sizeof` 得到的是指针大小，所以长度要另传。
2. 返回负数（`"a"` 小于 `"b"`）；两个字符串相等当且仅当 `strcmp(a, b) == 0`。
3. 需要 9 字节（8 个字符加 `'\0'`），而 `buf` 只有 8 字节，越界写入，属于未定义行为。
4. 最多 7 个字符，第 8 个位置放 `'\0'`；若一行不超过 7 个字符，换行符会被保留在缓冲区里。
5. 3 个（即 `m[0][0]`、`m[0][1]`、`m[0][2]`）。

</details>

**进入下一层的标准**：所有字符串操作都能说明目标缓冲区大小是否足够

### L3 熟练

**一句话定义**：能用带长度限制的函数安全地处理、转换任意输入，并且把空串和超长串都当作正常情况来对待。

**核心概念**：
- `snprintf` 永远不会写超过给定大小，且总是补 `'\0'`，返回值是“完整结果需要的长度”，可用来检测截断。
- `strncpy` 不保证补 `'\0'`，用它时必须手动收尾；很多场合 `snprintf` 更省心。
- `strtol`/`strtod` 通过 `end` 指针和 `errno` 报告转换是否成功，比 `atoi` 可靠。
- `memcpy` 要求源和目标不重叠，重叠时用 `memmove`；`memset` 按字节填充。
- `ctype.h` 函数的参数必须是 `unsigned char` 范围或 `EOF`，传入 `char` 前要先转换。

**知识点详解**：
- `snprintf`：`snprintf(buf, sizeof buf, "%s", src)` 是安全拷贝字符串的常用写法。若返回值 `>= sizeof buf`，说明内容被截断了。
- `strncpy`：它最多拷贝 `n` 个字符，源串太长时不会写 `'\0'`，源串太短时又会把剩余部分全部补零。所以需要在拷贝后手动写 `buf[n-1] = '\0'`。
- `strtol`/`strtod`：转换后检查 `end == s`（没有任何数字）、`*end != '\0'`（尾部有杂质）和 `errno == ERANGE`（溢出）。`atoi` 对 `"abc"` 与 `"0"` 都返回 0，无法区分。
- `memcpy`/`memmove`/`memset`：它们按字节处理任意类型的内存。`memmove` 内部会处理重叠，所以在不确定时选它更安全。
- `ctype.h`：`isdigit`、`isalpha`、`isspace`、`toupper` 等用于逐字符分类。对可能为负数的 `char` 先转成 `(unsigned char)`，否则是未定义行为。

**需要掌握**：`snprintf`、`strncpy` 的正确用法；`strtol`/`strtod` 做输入转换；`memcpy`/`memmove`/`memset`；`ctype.h`

**真实例子**：

```c
#include <ctype.h>
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 安全解析整数：成功返回 0，失败返回 -1
static int parse_int(const char *s, long *out) {
    char *end;
    errno = 0;
    long v = strtol(s, &end, 10);
    if (end == s || errno == ERANGE) return -1;      // 没有数字或溢出
    while (isspace((unsigned char)*end)) end++;      // 允许尾部空白
    if (*end != '\0') return -1;                     // 尾部有杂质
    *out = v;
    return 0;
}

int main(void) {
    const char *tests[] = {"42", "  -7 ", "12abc", "", "99999999999999999999"};
    for (size_t i = 0; i < sizeof tests / sizeof tests[0]; i++) {
        long v;
        if (parse_int(tests[i], &v) == 0) printf("\"%s\" -> %ld\n", tests[i], v);
        else printf("\"%s\" -> invalid\n", tests[i]);
    }
    char buf[8];
    int need = snprintf(buf, sizeof buf, "%s", "truncated-text");  // 自动截断并补 '\0'
    printf("buf=%s need=%d\n", buf, need);
    char s[] = "abcdef";
    memmove(s + 2, s, 4);                            // 源和目标重叠，必须用 memmove
    printf("%s\n", s);
    return 0;
}
```

预期输出：`42` 和 `-7` 转换成功，`12abc`、空串、超大数都是 `invalid`；然后是 `buf=truncat need=14`，最后一行 `ababcd`。若在旧版 MinGW（Windows）上编译，需加 `-D__USE_MINGW_ANSI_STDIO=1`，否则其 `snprintf` 与 `%zu` 不符合 C99/C11 行为。

**常见错误**：`strncpy` 不自动补 `'\0'`；`memcpy` 用于重叠内存；用 `atoi` 无法判断转换失败
- 另一个常见错误是把 `char` 直接传给 `isalpha` 等函数，遇到负值（如 UTF-8 的高位字节）会触发未定义行为。
- 错误写法与正确写法对照：

```c
char dst[8];
// 错误：源串长度 >= 8 时，strncpy 不会写 '\0'，dst 不是合法字符串
strncpy(dst, "0123456789", sizeof dst);
// 正确：手动收尾；或直接用 snprintf
strncpy(dst, "0123456789", sizeof dst - 1);
dst[sizeof dst - 1] = '\0';
snprintf(dst, sizeof dst, "%s", "0123456789");

int n = atoi("abc");               // 错误：得到 0，无法知道是失败还是输入了 "0"
char *end;
long v = strtol("abc", &end, 10);  // 正确：end 仍指向 "abc"，可据此判定失败
```

**应该练习**：实现自己的 `strlen`/`strcpy`/`strstr`；写单词计数程序；写简单的分词函数

**使用前检查清单**：
- [ ] 拷贝字符串时，目标是否一定以 `'\0'` 结尾
- [ ] 是否检查了 `snprintf` 的返回值以发现截断
- [ ] 数字转换是否用 `strtol`/`strtod`，并检查 `end` 与 `errno`
- [ ] 内存区域可能重叠时是否用了 `memmove`
- [ ] 传给 `ctype.h` 函数的值是否已转成 `unsigned char`
- [ ] 是否测试过空串、只有空白的串和超长串

**自测小问题**：
1. `snprintf(buf, 8, ...)` 最多往 `buf` 写入多少个可见字符？
2. 为什么说 `strncpy` 容易用错？
3. 用 `strtol` 转换后，怎样判断整个字符串都被成功解析？
4. `memcpy` 与 `memmove` 的区别是什么？什么时候必须用后者？
5. `isdigit(c)` 中的 `c` 为什么要转成 `unsigned char`？

<details><summary>参考答案</summary>

1. 最多 7 个，第 8 个字节留给 `'\0'`。
2. 源串长度不小于 `n` 时它不写 `'\0'`；源串较短时又会把剩余空间全部补零，既不安全也浪费。
3. 要同时满足：`end != s`、`*end == '\0'`（或只剩空白）、`errno != ERANGE`。
4. `memcpy` 要求区域不重叠，`memmove` 允许重叠。不能保证不重叠时（例如在同一数组内移动数据）必须用 `memmove`。
5. 这些函数只接受 `unsigned char` 范围的值或 `EOF`，`char` 为负值时是未定义行为。

</details>

**进入下一层的标准**：处理任意输入都不会崩溃或越界，空串与超长串都有测试

### L4 进阶

**一句话定义**：能自己实现可自动扩容的动态字符串，理解 UTF-8 的多字节本质，并在查找与排序中做出合理的算法选择。

**核心概念**：
- 动态字符串至少需要三个字段：数据指针、当前长度、容量。
- 扩容用“几何增长”（每次翻倍），让追加操作的均摊代价为常数。
- `realloc` 可能移动内存，旧指针立即失效，要用临时变量接收返回值。
- UTF-8 中一个字符占 1~4 字节，续字节的高两位恒为 `10`，按字节切割会破坏字符。
- 算法选择看场景：KMP 做单模式匹配，哈希表做词频统计，快排与归并排序各有取舍。

**知识点详解**：
- 动态字符串缓冲区：把 `data`、`len`、`cap` 封装成结构体，所有追加操作都经过同一个函数，在其中统一检查容量。这样“没检查就写”的错误只会出现在一个地方。
- 自动扩容：需要的空间是 `len + n + 1`（别忘了 `'\0'`）。容量不足时循环翻倍直到够用，再 `realloc`。失败时原指针仍然有效，必须先保存。
- UTF-8 与宽字符：`strlen` 返回字节数而不是字符数。统计字符数时，只数“不是续字节”的字节。`wchar_t` 在不同平台宽度不同（Windows 为 16 位），跨平台处理文本时优先坚持 UTF-8。
- 查找算法：朴素匹配最坏是 O(nm)；KMP 通过失败函数保证 O(n+m)；哈希表以空间换时间，平均 O(1) 查找，适合词频统计。
- 排序算法比较：快排平均 O(n log n)、原地但最坏 O(n²) 且不稳定；归并排序稳定、最坏也是 O(n log n)，但需要额外空间。标准库 `qsort` 可满足大多数需求。

**需要掌握**：动态字符串缓冲区（自动扩容）；UTF-8 与宽字符基础；高效查找算法（KMP、哈希）；排序算法比较（快排、归并）

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct { char *data; size_t len, cap; } StrBuf;

// 追加字符串，必要时翻倍扩容；成功返回 0
static int sb_append(StrBuf *sb, const char *s) {
    size_t n = strlen(s), need = sb->len + n + 1;
    if (need > sb->cap) {
        size_t cap = sb->cap ? sb->cap : 8;
        while (cap < need) cap *= 2;
        char *p = realloc(sb->data, cap);           // 用临时指针接收，失败时不丢旧块
        if (!p) return -1;
        sb->data = p;
        sb->cap = cap;
    }
    memcpy(sb->data + sb->len, s, n + 1);           // 连 '\0' 一起拷贝
    sb->len += n;
    return 0;
}

// 统计 UTF-8 字符数：跳过形如 10xxxxxx 的续字节
static size_t utf8_count(const char *s) {
    size_t c = 0;
    for (; *s; s++) if (((unsigned char)*s & 0xC0) != 0x80) c++;
    return c;
}

int main(void) {
    StrBuf sb = {0};
    for (int i = 0; i < 5; i++)
        if (sb_append(&sb, "你好,C;") != 0) { free(sb.data); return 1; }
    printf("bytes=%zu chars=%zu cap=%zu\n", sb.len, utf8_count(sb.data), sb.cap);
    free(sb.data);
    return 0;
}
```

预期输出：`bytes=45 chars=25 cap=64`（每段 9 字节、5 个字符，源文件需保存为 UTF-8）。

**常见错误**：把 UTF-8 中文当单字节切割；扩容后仍使用旧指针
- 另一个常见错误是直接写 `p = realloc(p, n)`，失败时 `p` 被覆盖为 `NULL`，原内存泄漏。
- 错误写法与正确写法对照：

```c
char *p = sb->data;                  // 保存了旧指针
sb_append(sb, "more text ...");      // 内部 realloc 可能移动内存
printf("%s\n", p);                   // 错误：p 可能已失效（悬空指针）
printf("%s\n", sb->data);            // 正确：每次都通过结构体取最新指针

// 错误：按 3 个字节截断 "你好"，若截在字符中间会得到乱码
// char cut[4]; memcpy(cut, "你好", 2); cut[2] = '\0';
// 正确：先找到字符边界，再截断（此处 3 字节恰好是一个汉字）
char cut[4]; memcpy(cut, "你好", 3); cut[3] = '\0';
```

**应该练习**：实现可扩容的 `StringBuilder`；统计 UTF-8 文本字符数；用哈希表统计词频

**使用前检查清单**：
- [ ] 每次 `realloc` 都用临时变量接收，并检查 `NULL`
- [ ] 扩容后是否只通过结构体访问数据，没有保留旧指针
- [ ] 容量计算是否包含了结尾的 `'\0'`
- [ ] 处理多字节文本时，是否按字符边界而不是字节边界切割
- [ ] 是否有对应的 `free`，并用 ASan 或 Valgrind 验证过
- [ ] 比较算法时是否考虑了稳定性、额外空间和最坏情况

**自测小问题**：
1. 为什么扩容要用翻倍策略，而不是每次增加固定的几个字节？
2. `realloc` 失败时，原来的内存块会怎样？
3. UTF-8 中 `(c & 0xC0) == 0x80` 表示什么？
4. “你好” 的 `strlen` 是多少？它有几个字符？
5. 快速排序与归并排序在稳定性和额外空间上有何区别？

<details><summary>参考答案</summary>

1. 翻倍使追加操作的均摊时间复杂度为 O(1)；每次加固定值会导致频繁 `realloc` 和拷贝，总代价接近 O(n²)。
2. 返回 `NULL`，原内存块保持有效且未被释放，所以必须用临时变量接收返回值。
3. 表示这是一个续字节（多字节字符的非首字节），统计字符数时应跳过它。
4. `strlen` 为 6（每个汉字 3 字节），字符数为 2。
5. 快排不稳定、原地（栈空间除外），最坏 O(n²)；归并稳定、需要 O(n) 额外空间，最坏 O(n log n)。

</details>

**进入下一层的标准**：动态字符串类型通过 ASan 测试，无泄漏无越界

### L5 独立

**一句话定义**：能独立设计带长度的安全字符串接口，并用模糊测试和性能分析证明输入处理在任意数据下都可靠。

**核心概念**：
- 带长度的字符串（指针加长度）不依赖 `'\0'`，可表示子串、含零字节的数据，也避免了反复 `strlen`。
- API 的安全性来自设计：缓冲区大小随参数一起传递，错误通过返回值显式报告。
- 模糊测试用大量随机或变异输入验证不变量，配合 ASan/UBSan 能发现越界和未定义行为。
- 不变量是检验正确性的基础，例如“字段总长度加分隔符数等于输入长度”。
- 性能分析要先测量再优化，工具如 `gprof`、`perf`，或在代码中计时。

**知识点详解**：
- 设计安全的字符串 API：把指针和长度打包成 `Str` 结构体，函数只依赖 `n` 而不依赖结尾符。所有输出缓冲区都附带容量参数，并在放不下时返回错误，而不是静默截断。
- 模糊测试（fuzzing）：解析器的输入来自外部，写法再谨慎也难以穷举边界。随机生成大量输入（包含空串、全分隔符、含 `'\0'` 的数据），检查程序不崩溃、不变量成立，成本低而收益高。可以先从手写随机循环开始，再进阶到 libFuzzer 或 AFL。
- 性能分析：先用 `-O2` 编译并计时，再用 profiler 找热点。字符串处理的常见热点是重复 `strlen`、频繁小块 `malloc` 和不必要的拷贝；带长度的设计可以直接避免前两项。

**需要掌握**：设计安全的字符串 API（带长度的字符串结构）；模糊测试（fuzzing）输入处理；性能分析

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct { const char *p; size_t n; } Str;    // 带长度的只读视图

// 取下一个以逗号分隔的字段；没有更多字段时返回 0
static int next_field(Str *rest, Str *field) {
    if (!rest->p) return 0;
    const char *c = memchr(rest->p, ',', rest->n);   // 只在 n 字节内查找
    if (c) {
        field->p = rest->p;
        field->n = (size_t)(c - rest->p);
        rest->n -= field->n + 1;
        rest->p = c + 1;
    } else {
        *field = *rest;                              // 最后一个字段
        rest->p = NULL;
        rest->n = 0;
    }
    return 1;
}

int main(void) {
    static const char alpha[] = {'a', 'b', ',', ' ', '\n', '\0'};  // 含零字节
    char buf[64];
    srand(12345);                                    // 固定种子，可复现
    for (int iter = 0; iter < 100000; iter++) {
        size_t len = (size_t)(rand() % (int)sizeof buf);
        for (size_t i = 0; i < len; i++) buf[i] = alpha[rand() % (int)sizeof alpha];
        Str rest = {buf, len}, f;
        size_t fields = 0, total = 0;
        while (next_field(&rest, &f)) { fields++; total += f.n; }
        if (total + fields - 1 != len) { printf("BUG at %d\n", iter); return 1; }  // 不变量
    }
    puts("fuzz ok: 100000 cases");
    return 0;
}
```

预期输出：`fuzz ok: 100000 cases`；若字段切分有错，会打印 `BUG at` 加迭代编号。建议再用 `-fsanitize=address,undefined` 编译运行一遍。

**常见错误**：自行拼接字符串到固定缓冲区而未检查；忽略输入编码
- 另一个常见错误是把外部输入当作以 `'\0'` 结尾的字符串，而输入里可能含零字节，导致只处理了前半部分。
- 错误写法与正确写法对照：

```c
char out[32];
// 错误：name 来自外部输入，长度不受控，strcat 会越界
strcpy(out, "user=");
strcat(out, name);
// 正确：用 snprintf 并检查返回值，放不下就报错
int r = snprintf(out, sizeof out, "user=%s", name);
if (r < 0 || (size_t)r >= sizeof out) return -1;   // 输入过长，拒绝处理
```

**应该练习**：实现一个小型 JSON 或 CSV 解析器，并用随机输入做模糊测试

**使用前检查清单**：
- [ ] 每个公共函数的签名里，输入和输出缓冲区都带长度
- [ ] 所有失败路径都有明确的返回值或错误码，并且调用者必须处理
- [ ] 是否写出了可被检查的不变量，并放进测试循环
- [ ] 是否用 `-fsanitize=address,undefined` 跑过模糊测试
- [ ] 随机输入是否包含空串、超长串、全分隔符和含 `'\0'` 的数据
- [ ] 优化前是否先测量，确认瓶颈所在

**自测小问题**：
1. 带长度的字符串相比 `'\0'` 结尾字符串有哪些优点？
2. 为什么模糊测试要使用固定的随机种子？
3. 示例中的不变量 `total + fields - 1 == len` 为什么成立？
4. 为什么要同时使用 ASan 和 UBSan 来运行模糊测试？
5. 设计 API 时，缓冲区放不下结果，应该如何处理？

<details><summary>参考答案</summary>

1. 可表示子串而不用拷贝，能包含零字节，获取长度是 O(1)，边界检查不依赖结尾符。
2. 保证失败可复现，便于调试；需要更多覆盖时可以换多个种子循环。
3. 每个分隔符把输入分成两侧，`fields` 个字段之间正好有 `fields-1` 个逗号，所有字节要么属于某个字段，要么是逗号。
4. ASan 检测越界与释放后使用，UBSan 检测整数溢出、对齐等未定义行为，两者覆盖的问题不同。
5. 返回错误（或所需长度）让调用者决定，不要静默截断，也不要越界写入。

</details>

**达标标准**：解析器在大量随机输入下不崩溃，错误信息可读



---

## 第 4 章 指针

### L1 入门

**一句话定义**：理解指针就是“存放地址的变量”，能用 `&` 和 `*` 读写别人的变量。

**核心概念**：
- 每个变量都住在内存的某个地址上，指针保存的就是这个地址。
- `&x` 取 `x` 的地址；`*p` 访问 `p` 所指向的那个对象。
- 指针本身也是变量，有自己的类型（`int *` 与 `char *` 不同）和自己的地址。
- `NULL` 表示“不指向任何对象”，使用前必须判断。
- 指针必须先指向合法对象，才能解引用。

**知识点详解**：
- 指针是存放地址的变量：它的值是一个内存地址，类型决定了通过它读写时按多少字节、什么格式解释数据。
- `&` 取地址、`*` 解引用：两者互为逆运算，`*(&a)` 就是 `a` 本身。声明里的 `*` 只表示“这是指针类型”，表达式里的 `*` 才是解引用。
- 指针声明与初始化：声明后立刻赋值，要么指向已有变量，要么设为 `NULL`，避免留下随机地址。
- `NULL`：标准保证它不等于任何有效对象的地址，用来表示“暂时没有指向”，解引用它是未定义行为。

- **需要掌握**：指针是存放地址的变量；`&` 取地址、`*` 解引用；指针声明与初始化；`NULL`

**真实例子**：

```c
#include <stdio.h>

// 通过指针交换两个整数
static void swap(int *x, int *y) {
    int t = *x;
    *x = *y;
    *y = t;
}

int main(void) {
    int a = 10, b = 20;
    int *p = &a;                 // p 保存 a 的地址
    int *q = NULL;               // 明确表示“不指向任何对象”

    printf("a=%d, &a=%p, *p=%d\n", a, (void *)&a, *p);
    *p = 99;                     // 通过指针修改 a
    printf("修改后 a=%d\n", a);

    swap(&a, &b);                // 传入地址，函数内部才能改到原变量
    printf("交换后 a=%d, b=%d\n", a, b);

    if (q == NULL) {
        puts("q 是空指针，不能解引用");
    }
    return 0;
}
```

预期输出：第一行的地址因运行环境而异；随后依次是 `修改后 a=99`、`交换后 a=20, b=99`、`q 是空指针，不能解引用`。

**常见错误**：
- 使用未初始化的野指针
- 解引用 `NULL`
- 声明 `int* a, b;` 误以为 `b` 也是指针

错误与正确写法对照：

```c
// 错误：* 只修饰紧跟的那个名字，b 其实是 int；p 未初始化
int* a, b;
int *p;
*p = 5;                 // 野指针写入，未定义行为

// 正确：每个指针单独声明，并初始化
int x = 0;
int *a = &x, *b = &x;
int *p = &x;
*p = 5;
```

**应该练习**：
- 用指针交换两个数
- 打印变量地址和值
- 通过指针修改变量

**使用前检查清单**：
- [ ] 每个指针声明时都已初始化（指向有效对象或 `NULL`）
- [ ] 解引用之前确认指针不是 `NULL`
- [ ] 一行声明多个指针时，每个名字前都写了 `*`
- [ ] 用 `%p` 打印地址时，参数已转换为 `(void *)`
- [ ] 函数要修改调用方变量时，传的是地址而不是值

**自测小问题**：
1. `int *p = &a;` 中，`p`、`*p`、`&p` 分别是什么？
2. `int* a, b;` 里 `b` 是什么类型？
3. 为什么 `swap(int x, int y)` 无法交换调用方的两个变量？
4. 解引用 `NULL` 会发生什么？标准如何规定？
5. 指针变量占用的空间和它所指对象的大小有关吗？

<details><summary>参考答案</summary>

1. `p` 是保存 `a` 地址的指针；`*p` 是 `a` 本身（可读写）；`&p` 是指针变量 `p` 自己的地址，类型为 `int **`。
2. `int`。只有 `a` 是指针，应写成 `int *a, *b;`。
3. 参数按值传递，函数里交换的只是副本；传入地址后才能通过 `*` 改到原变量。
4. 未定义行为，常见表现是段错误崩溃，但标准不保证一定崩溃。
5. 无关。指针大小由平台决定（如 64 位系统通常 8 字节），与所指类型的大小无关。

</details>

**进入下一层的标准**：能画出变量、指针、地址三者的关系图

### L2 基础

**一句话定义**：掌握指针与数组的对应关系和指针算术，能用指针参数返回结果，并读懂 `const` 修饰的位置。

**核心概念**：
- 数组名在多数表达式中会退化为指向首元素的指针，`a[i]` 等价于 `*(a + i)`。
- 指针加 1 是前进一个元素（`sizeof(T)` 字节），不是一个字节。
- 两个指向同一数组的指针相减，得到元素个数（`ptrdiff_t`）。
- 输出参数：传入变量地址，函数通过 `*` 写回结果。
- `const` 看 `*` 的左右：左边限制所指内容，右边限制指针本身。

**知识点详解**：
- 指针与数组的关系：数组在内存中连续排列，指针指向首元素后就能顺序访问全部元素。注意 `sizeof` 对数组名和指针结果不同。
- 指针算术：`p + n` 的地址增量是 `n * sizeof(*p)`。只有结果仍落在数组内或“末尾后一位”才合法，越界即未定义行为。
- 指针作函数参数（输出参数）：C 只有按值传递，想让函数返回多个结果，就传多个指针让它写回。
- `const` 指针：`const int *p` 不能通过 `p` 改内容但 `p` 可改指向；`int *const p` 可改内容但不能改指向。

- **需要掌握**：指针与数组的关系；指针算术；指针作函数参数（输出参数）；`const` 指针（`const int *` 与 `int *const` 的区别）

**真实例子**：

```c
#include <stdio.h>
#include <stddef.h>

// 用指针遍历实现 strlen；const 表示不会修改字符串内容
static size_t my_strlen(const char *s) {
    const char *p = s;
    while (*p != '\0') {
        p++;                     // 指针加 1 = 前进一个 char
    }
    return (size_t)(p - s);      // 指针相减得到元素个数
}

// 输出参数：同时返回最大值和最小值
static void min_max(const int *arr, size_t n, int *min, int *max) {
    *min = *max = arr[0];
    for (const int *p = arr + 1; p < arr + n; p++) {
        if (*p < *min) *min = *p;
        if (*p > *max) *max = *p;
    }
}

int main(void) {
    int data[] = {7, 3, 9, -2, 5};
    int lo, hi;
    min_max(data, sizeof data / sizeof data[0], &lo, &hi);
    printf("len=%zu, min=%d, max=%d\n", my_strlen("pointer"), lo, hi);
    return 0;
}
```

预期输出：`len=7, min=-2, max=9`。

**常见错误**：
- 指针算术越界
- 混淆指针加 1 与地址加 1 字节
- 返回局部变量地址

错误与正确写法对照：

```c
// 错误：返回局部数组地址，函数返回后该内存已失效
char *bad_name(void) {
    char buf[16] = "tom";
    return buf;
}

// 正确一：由调用方提供缓冲区
void good_name(char *out, size_t cap) {
    snprintf(out, cap, "%s", "tom");
}

// 正确二：返回静态存储期的字符串字面量
const char *good_name2(void) {
    return "tom";
}
```

**应该练习**：
- 用指针遍历数组
- 实现 `strlen`
- 写函数通过指针同时返回最大值与最小值

**使用前检查清单**：
- [ ] 指针算术的结果始终落在数组范围内（或末尾后一位）
- [ ] 不读写“末尾后一位”所指的位置
- [ ] 函数返回的指针不指向局部非静态变量
- [ ] 只读参数加了 `const`（如 `const char *s`）
- [ ] 把数组传给函数时，同时传了元素个数

**自测小问题**：
1. 设 `int a[5]; int *p = a;`，`p + 1` 比 `p` 大多少字节（`int` 为 4 字节）？
2. `a[i]` 与 `*(a + i)` 有区别吗？
3. `const int *p`、`int *const p` 各自限制了什么？
4. 函数参数写 `int arr[]`，在函数里 `sizeof arr` 得到什么？
5. 为什么不能返回局部数组的地址？

<details><summary>参考答案</summary>

1. 4 字节，即一个 `int` 的大小。
2. 没有，编译器把 `a[i]` 定义为 `*(a + i)`。
3. `const int *p`：不能通过 `p` 修改所指内容，`p` 本身可改指向；`int *const p`：`p` 不能改指向，所指内容可改。
4. 得到指针大小（如 8），因为参数已退化为 `int *`，所以要另传长度。
5. 局部数组的生命周期随函数返回结束，返回的地址成为悬空指针，使用即未定义行为。

</details>

**进入下一层的标准**：能正确读懂 `const char *p`、`char *const p`、`const char *const p`

### L3 熟练

**一句话定义**：能处理多级指针、指针数组、函数指针和 `void *`，并读写复杂的指针声明。

**核心概念**：
- `char **` 是“指向 `char *` 的指针”，常用于字符串数组和需要修改指针本身的场景。
- 指针数组 `T *a[N]` 是 N 个指针；数组指针 `T (*p)[N]` 是一个指向 N 元素数组的指针。
- 函数指针保存函数入口，可放进数组做分发表。
- `void *` 可存放任意对象指针，但不能直接解引用或做算术，使用前要转回具体类型。
- `argv` 是 `char *argv[]`，以 `NULL` 结尾，`argv[0]` 通常是程序名。

**知识点详解**：
- 指针的指针（`char **`）：当函数需要修改调用方的指针（如让它指向新分配的内存）时，必须传该指针的地址。
- 指针数组与数组指针：优先级决定含义。`int *a[3]` 是三个 `int *`；`int (*p)[3]` 是指向含三个 `int` 的数组的指针，二维数组的行指针就是这种类型。
- 函数指针数组：`int (*fp[3])(int)` 把一组同签名函数放在一起，按下标调用，常用于状态机和命令分发。
- `void *` 通用指针与类型转换：`malloc`、`qsort`、`memcpy` 都用它做类型擦除。C 中 `void *` 可隐式转换为其他对象指针，但解引用前必须有明确的目标类型。
- `argv` 的结构：`argc` 是个数，`argv` 指向一个指针数组，每个元素指向一个以 `'\0'` 结尾的参数字符串，`argv[argc]` 为 `NULL`。

- **需要掌握**：指针的指针（`char **`）；指针数组与数组指针；函数指针数组；`void *` 通用指针与类型转换；`argv` 的结构

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 通用交换：按字节搬运，不关心具体类型
static void swap_any(void *a, void *b, size_t size) {
    unsigned char tmp[64];                 // 示例限制：size <= 64
    memcpy(tmp, a, size);
    memcpy(a, b, size);
    memcpy(b, tmp, size);
}

static int cmp_str(const void *x, const void *y) {
    const char *const *a = x;              // void * 先转成真正的类型
    const char *const *b = y;
    return strcmp(*a, *b);
}

static int add(int x) { return x + 1; }
static int dbl(int x) { return x * 2; }

int main(int argc, char *argv[]) {
    int m = 1, n = 2;
    swap_any(&m, &n, sizeof m);
    const char *names[] = {"pear", "apple", "fig"};   // 指针数组
    qsort(names, 3, sizeof names[0], cmp_str);
    int (*fp[2])(int) = {add, dbl};                   // 函数指针数组
    printf("m=%d n=%d, %s %s %s, fp: %d %d, argc=%d, argv[0]=%s\n",
           m, n, names[0], names[1], names[2], fp[0](5), fp[1](5), argc, argv[0]);
    return 0;
}
```

预期输出：`m=2 n=1, apple fig pear, fp: 6 10, argc=1, argv[0]=` 后接程序路径（无命令行参数时）。

**常见错误**：
- `void *` 解引用前未转换
- 二维数组与指针数组混淆
- `sizeof(指针)` 当作数组大小

错误与正确写法对照：

```c
// 错误：把指针当数组，sizeof 得到的是指针大小（如 8）
void fill(int *arr) {
    for (size_t i = 0; i < sizeof(arr) / sizeof(arr[0]); i++) arr[i] = 0;
}

// 正确：显式传入元素个数
void fill_ok(int *arr, size_t n) {
    for (size_t i = 0; i < n; i++) arr[i] = 0;
}

// 错误：void * 不能直接解引用
// int v = *vp;
// 正确：先转换成明确的类型
// int v = *(const int *)vp;
```

另外注意：`int a[3][4]` 是连续的 12 个 `int`，而 `int *b[3]` 只是三个指针，每个可以指向不同长度的数组，两者不能互相替代传参。

**应该练习**：
- 实现通用的 `swap(void *, void *, size_t)`
- 用指针数组对字符串排序
- 实现链表的插入与删除

**使用前检查清单**：
- [ ] `void *` 在解引用或算术之前已转换为明确类型
- [ ] 传递数组给函数时，长度单独传入，不用 `sizeof(指针)`
- [ ] 复杂声明按“从名字出发，先右后左”拆解过一遍
- [ ] 函数指针调用前确认非 `NULL`，签名与目标函数一致
- [ ] 访问 `argv[i]` 前检查 `i < argc`
- [ ] 通用函数的 `size` 参数与实际类型大小一致

**自测小问题**：
1. `int *a[3]` 与 `int (*a)[3]` 有什么区别？
2. 如何声明“指向返回 `int`、参数为 `int` 的函数的指针”？
3. 为什么函数想修改调用方的 `char *` 时要传 `char **`？
4. `void *` 为什么不能做 `p++`？
5. `argv[argc]` 的值是什么？

<details><summary>参考答案</summary>

1. 前者是含 3 个 `int *` 的数组；后者是指向含 3 个 `int` 的数组的指针。
2. `int (*fp)(int);`
3. 参数按值传递，只传 `char *` 会修改副本；传 `char **` 才能改到调用方的指针本身。
4. `void` 没有大小，编译器不知道前进多少字节（GCC 的扩展不属于标准 C）。
5. `NULL`，标准规定 `argv` 以空指针结尾。

</details>

**进入下一层的标准**：能读懂并写出 `int (*fp[3])(int)` 这类复杂声明

### L4 进阶

**一句话定义**：能在链表、树、图等数据结构中安全地操作指针，并用工具验证内存行为。

**核心概念**：
- 节点通过指针连接，插入、删除本质上是改写“指向它的那个指针”。
- 用二级指针（`Node **`）可统一处理头节点与中间节点，免去特判。
- 每个 `malloc` 都要有对应的 `free`；释放后立即置 `NULL` 可避免悬空指针。
- `restrict` 承诺“该指针是访问此对象的唯一途径”，让编译器优化，违反则未定义。
- 对齐要求不满足的指针转换是未定义行为；动态分配内存的对齐已满足任意标准类型。

**知识点详解**：
- 链表、树、图中的指针操作：关键是先保存后继再改链接，释放节点前先取出 `next`；树和图还要考虑递归深度与重复访问。
- 指向指针的指针修改头节点：`Node **pp` 指向“当前要检查的指针”，删除时直接 `*pp = (*pp)->next`，头节点不再是特例。
- 别名与 `restrict`：两个指针指向同一对象称为别名，编译器默认必须保守处理；`restrict` 是调用方对“无重叠”的承诺，如 `memcpy` 与 `memmove` 的区别。
- 内存对齐对指针转换的影响：把 `char *` 强转为 `int *` 并解引用，若地址未对齐可能崩溃或违反严格别名规则；需要时用 `memcpy` 搬运。
- 用 gdb 查看指针：`print p`、`print *p`、`print *p->next`、`x/4wx p` 可查看地址、对象和原始内存，配合 `watch` 观察指针何时被改写。

- **需要掌握**：链表、树、图中的指针操作；指向指针的指针修改头节点；别名与 `restrict`；内存对齐对指针转换的影响；用 gdb 查看指针

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int val; struct Node *next; } Node;

// 二级指针：直接修改“指向节点的那个指针”，头节点无需特判
static void push_front(Node **head, int v) {
    Node *n = malloc(sizeof *n);
    if (n == NULL) return;
    n->val = v;
    n->next = *head;
    *head = n;
}

static void remove_val(Node **head, int v) {
    for (Node **pp = head; *pp != NULL; pp = &(*pp)->next) {
        if ((*pp)->val == v) {
            Node *dead = *pp;
            *pp = dead->next;
            free(dead);
            return;
        }
    }
}

int main(void) {
    Node *head = NULL;
    for (int i = 1; i <= 5; i++) push_front(&head, i);   // 5 4 3 2 1
    remove_val(&head, 5);                                // 删除头节点
    remove_val(&head, 3);                                // 删除中间节点
    while (head != NULL) {
        Node *next = head->next;                         // 先保存再释放
        printf("%d ", head->val);
        free(head);
        head = next;
    }
    putchar('\n');                                       // 输出：4 2 1
    return 0;
}
```

预期输出：`4 2 1 `（末尾有一个空格并换行）。可用 `gcc -g -fsanitize=address,undefined` 编译验证无泄漏。

**常见错误**：
- 释放节点后未置 `NULL`
- 循环链表遍历死循环
- 二级指针使用混乱

错误与正确写法对照：

```c
// 错误：free 之后继续读 cur->next（使用已释放内存）
for (Node *cur = head; cur != NULL; cur = cur->next) {
    free(cur);
}

// 正确：先保存后继，再释放；最后把头指针置空
Node *cur = head;
while (cur != NULL) {
    Node *next = cur->next;
    free(cur);
    cur = next;
}
head = NULL;
```

循环链表遍历应以“回到起点”作终止条件，例如 `do { ... p = p->next; } while (p != start);`，而不是判断 `NULL`。

**应该练习**：
- 实现双向链表、二叉搜索树
- 反转链表
- 检测链表环

**使用前检查清单**：
- [ ] 每个 `malloc` 的返回值都检查过 `NULL`
- [ ] 释放节点前已保存其后继指针
- [ ] 释放后相关指针已置 `NULL`，不再被使用
- [ ] 删除、插入头节点的路径单独测试过
- [ ] 带 `restrict` 的参数，调用方确保内存区域不重叠
- [ ] 已用 `-fsanitize=address,undefined` 和 Valgrind 各跑一遍

**自测小问题**：
1. 为什么删除链表节点时用 `Node **` 比用 `Node *` 更简洁？
2. 怎样检测单链表是否有环？
3. `restrict` 能保证程序更安全吗？
4. 释放后置 `NULL` 能解决所有悬空指针问题吗？
5. gdb 中怎样查看 `p` 所指向的 4 个 `int`？

<details><summary>参考答案</summary>

1. 用 `Node **` 可以统一修改“指向目标节点的那个指针”，头节点和中间节点处理方式相同，无需额外判断。
2. 快慢指针（Floyd 判圈）：慢指针每次走一步，快指针走两步，若相遇则有环。
3. 不能。它是对编译器的承诺，违反会导致未定义行为；它的作用是允许优化，而不是增加检查。
4. 不能。其他保存了同一地址的指针仍然悬空；置 `NULL` 只能保护被置空的那一个。
5. `x/4dw p` 或 `print *p@4`。

</details>

**进入下一层的标准**：所有数据结构实现都通过 ASan 与 Valgrind 检查

### L5 独立

**一句话定义**：能设计所有权清晰、内部细节隐藏、使用者不会泄漏内存的指针型 API。

**核心概念**：
- 所有权约定：谁分配谁释放，或明确“转移所有权”，并写进接口文档。
- 不透明指针：头文件只声明 `typedef struct X X;`，字段留在 `.c` 中。
- 回调加 `void *userdata`：库不理解用户数据，只原样传回。
- 创建与销毁成对出现，销毁函数接受 `NULL`。
- 失败路径同样不能泄漏，`realloc` 失败时旧指针仍有效。

**知识点详解**：
- 所有权约定（谁分配谁释放）：接口注释要写清每个指针参数和返回值由谁负责释放，否则调用方与库方会重复释放或都不释放。
- 不透明指针封装：使用者只能通过函数操作对象，库的内部结构随时可改而不破坏使用者代码，也避免被误改字段。
- 回调中的 `void *userdata` 模式：C 没有闭包，用一个通用指针把上下文传给回调，是库与用户代码解耦的标准做法。
- 设计无泄漏 API：成对的 `new/free`，错误路径统一清理，对 `free(NULL)` 式的空指针安全，并让每个失败状态都可预期。

- **需要掌握**：所有权约定（谁分配谁释放）；不透明指针（opaque pointer）封装；回调中的 `void *userdata` 模式；设计无泄漏 API

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>

/* 头文件部分：只暴露不透明类型 */
typedef struct Vec Vec;
typedef void (*VecVisit)(int value, void *userdata);

/* 实现部分：使用者看不到字段 */
struct Vec { int *data; size_t len, cap; };

static Vec *vec_new(void) { return calloc(1, sizeof(Vec)); }   // 谁 new 谁 free
static void vec_free(Vec *v) {
    if (v == NULL) return;
    free(v->data);
    free(v);
}
static int vec_push(Vec *v, int x) {
    if (v->len == v->cap) {
        size_t ncap = v->cap ? v->cap * 2 : 4;
        int *p = realloc(v->data, ncap * sizeof *p);
        if (p == NULL) return -1;          // 失败时旧数据仍然有效
        v->data = p;
        v->cap = ncap;
    }
    v->data[v->len++] = x;
    return 0;
}
static void vec_each(const Vec *v, VecVisit fn, void *ud) {
    for (size_t i = 0; i < v->len; i++) fn(v->data[i], ud);
}

static void sum_cb(int x, void *ud) { *(long *)ud += x; }

int main(void) {
    Vec *v = vec_new();
    if (v == NULL) return 1;
    for (int i = 1; i <= 10; i++) vec_push(v, i);
    long sum = 0;
    vec_each(v, sum_cb, &sum);             // userdata 携带回调状态
    printf("sum=%ld\n", sum);              // 输出：sum=55
    vec_free(v);
    return 0;
}
```

预期输出：`sum=55`。实际工程中前三段应拆成 `vec.h`、`vec.c`、`main.c`，示例为便于单文件编译而合并。

**常见错误**：
- 接口没写明所有权，调用方与库方重复释放

错误与正确写法对照：

```c
// 错误：用 realloc 的返回值直接覆盖原指针，失败时丢失旧内存
v->data = realloc(v->data, n * sizeof(int));

// 正确：先用临时变量接收，成功后再赋值
int *tmp = realloc(v->data, n * sizeof *tmp);
if (tmp == NULL) return -1;
v->data = tmp;
```

文档对照：`/* 返回新分配的字符串，调用方须用 free 释放 */` 比不写任何说明明确得多；若库自己持有，则应注明“返回内部指针，不可 free，下次调用后失效”。

**应该练习**：
- 写一个泛型容器库（动态数组或哈希表），头文件只暴露不透明类型

**使用前检查清单**：
- [ ] 每个返回指针的函数，文档中写明由谁释放
- [ ] 头文件中没有暴露结构体字段
- [ ] 创建函数失败时返回 `NULL`，且没有部分泄漏
- [ ] 销毁函数接受 `NULL`，重复调用的约定已说明
- [ ] 回调签名带 `void *userdata`，并说明其生命周期
- [ ] 用 ASan/Valgrind 跑过全部使用示例与失败路径

**自测小问题**：
1. 为什么不透明指针能让库升级时不破坏使用者代码？
2. 回调中的 `userdata` 解决了什么问题？
3. 库函数返回一个指针，调用方怎么知道要不要 `free`？
4. `realloc` 失败时原指针会怎样？
5. 为什么让销毁函数接受 `NULL` 是好习惯？

<details><summary>参考答案</summary>

1. 使用者只持有指针，不依赖结构体布局，内部字段增减不影响其编译结果。
2. C 没有闭包，`userdata` 让回调能获取调用方的上下文状态，而库无需知道其类型。
3. 只能靠接口文档与命名约定说明，所以所有权必须写进接口说明里。
4. 原指针保持有效，原内存不会被释放，因此要用临时变量接收返回值。
5. 清理路径可以统一，不必为 `NULL` 逐个判断，也与标准库 `free(NULL)` 的行为一致。

</details>

**达标标准**：库的使用者无需了解内部结构就能正确使用，且不会内存泄漏









---

## 第 5 章 结构体与动态内存

### L1 入门

**一句话定义**：能用 `struct` 把相关数据打包成一个类型，并用 `malloc`/`free` 在堆上申请和归还内存，做到申请与释放一一对应。

**核心概念**：
- `struct` 把不同类型的成员组合成一个整体，`typedef` 可以给它起一个简短的类型名。
- 成员访问：变量用 `.`，指针用 `->`，`p->x` 等价于 `(*p).x`。
- 结构体数组在内存中连续存放，下标访问与普通数组一致。
- 堆内存由程序员负责：`malloc` 申请，`free` 归还，二者必须成对出现。
- `malloc` 返回 `void *`，失败时返回 `NULL`，使用前要判断。

**知识点详解**：
- `struct` 定义与成员访问（`.`、`->`）：`struct` 是自定义的复合类型，成员各占自己的空间。对变量用 `.`，对指向结构体的指针用 `->`，后者是前者加解引用的简写，函数传指针时最常用。
- `typedef struct`：给结构体类型取别名，之后可以直接写 `Student s;`，不必每次都带 `struct` 关键字，代码更短也更易读。
- 结构体数组：把同类记录放进一个数组，便于用循环批量录入和输出，例如一个班的学生信息。
- `malloc` 与 `free` 基本用法：`malloc(n)` 在堆上申请 n 字节，使用完必须 `free`，否则内存一直占用直到进程结束，这就是内存泄漏。`free` 之后指针不再有效，不能再访问。

**需要掌握**：`struct` 定义与成员访问（`.`、`->`）；`typedef struct`；结构体数组；`malloc` 与 `free` 基本用法

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>   /* malloc / free */

typedef struct {
    char name[20];
    int score;
} Student;

int main(void) {
    /* 结构体数组 */
    Student cls[3] = {{"Alice", 90}, {"Bob", 85}, {"Cindy", 77}};
    for (int i = 0; i < 3; i++) {
        Student *p = &cls[i];                 /* 指针用 -> 访问 */
        printf("%s: %d\n", p->name, (*p).score);
    }

    /* 动态分配整型数组并求和 */
    int n = 5;
    int *a = malloc(n * sizeof(*a));          /* sizeof(*a) 随类型自动适配 */
    if (a == NULL) {
        return 1;                             /* 分配失败 */
    }
    int sum = 0;
    for (int i = 0; i < n; i++) {
        a[i] = i + 1;
        sum += a[i];
    }
    printf("sum = %d\n", sum);
    free(a);                                  /* 与 malloc 成对 */
    return 0;
}
```

预期输出：先按行打印三名学生的姓名与分数，最后一行为 `sum = 15`。

**常见错误**：`malloc` 大小写错（应为 `sizeof(*p)` 或 `sizeof(类型)`）；忘记 `free`；忘记 `#include <stdlib.h>`

下面对照最典型的大小写错：给指针变量本身取 `sizeof`，只得到指针的大小（常为 8 字节），而不是它指向对象的大小。

```c
/* 错误：sizeof(p) 是指针大小，分配的空间不够，写入会越界 */
Student *p = malloc(sizeof(p));

/* 正确：用 sizeof(*p)，类型改了也不用改这里 */
Student *p = malloc(sizeof(*p));
```

另一类常见问题：`free(p)` 后继续使用 `p`，应在释放后立刻 `p = NULL;` 以便后续判断。

**应该练习**：学生信息结构体录入与输出；动态分配一个整型数组并求和

**使用前检查清单**：
- [ ] 已 `#include <stdlib.h>`，`malloc` 返回值已与 `NULL` 比较
- [ ] 大小写法为 `sizeof(*p)` 或 `sizeof(类型)`，而不是 `sizeof(p)`
- [ ] 每一个 `malloc` 都能在代码里找到对应的 `free`
- [ ] 指针成员访问用 `->`，变量成员访问用 `.`，没有混用
- [ ] `free` 之后不再读写该指针，必要时置为 `NULL`

**自测小问题**：
1. `p->x` 与 `(*p).x` 有什么关系？
2. 为什么推荐写 `malloc(n * sizeof(*a))` 而不是 `malloc(n * sizeof(int))`？
3. `typedef struct { ... } Student;` 带来了什么好处？
4. 忘记 `free` 会造成什么后果？
5. `malloc` 返回 `NULL` 表示什么？应该怎么处理？

<details><summary>参考答案</summary>

1. 两者完全等价，`->` 是解引用加成员访问的简写。
2. 前者把元素类型绑定在变量 `a` 上，改了 `a` 的类型这里自动跟着变，不会写出不一致的大小。
3. 可以直接用 `Student` 声明变量，不用每次写 `struct`，代码更简洁。
4. 内存泄漏：程序运行期间这块堆内存无法再被使用，长期运行的程序会越占越多。
5. 表示分配失败（内存不足等）。必须检查并处理，例如返回错误或退出，不能继续解引用。

</details>

**进入下一层的标准**：每个 `malloc` 都能配上对应的 `free`

### L2 基础

**一句话定义**：能把结构体当作参数在函数间合理传递，并用 `calloc`/`realloc` 管理可变大小的数据，且每次分配都检查失败。

**核心概念**：
- 值传递会拷贝整个结构体，指针传递只拷贝地址，大结构体和需要修改的场合应传指针。
- 只读参数用 `const T *`，既避免拷贝又表明不会修改。
- 结构体可以嵌套，嵌套成员用连续的 `.` 访问。
- `calloc(n, size)` 分配并清零；`realloc` 调整已有块的大小，可能移动位置。
- 所有分配函数都可能返回 `NULL`，必须检查。

**知识点详解**：
- 结构体作参数（值传递 vs 指针传递）：值传递时函数拿到的是副本，改动不影响调用者，且大结构体拷贝有开销。传指针可以修改原对象并节省拷贝，只读时加 `const`。
- 嵌套结构体：例如矩形由两个点组成，`r.min.x` 逐层访问。嵌套让数据模型更贴近现实，也便于复用。
- `realloc` 扩容：动态数组容量不够时调用，数据会被保留，但地址可能变化，所以旧指针不能再用。倍增扩容可使均摊成本为常数。
- `calloc`：与 `malloc` 的区别是自动清零，且参数分开写（个数、单个大小），内部会检查乘法溢出，更安全。
- 检查分配失败：内存不足时返回 `NULL`，不检查就解引用会崩溃。`realloc` 失败时原块仍然有效，需要先保存。

**需要掌握**：结构体作参数（值传递 vs 指针传递）；嵌套结构体；`realloc` 扩容；`calloc`；检查分配失败

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int *data;
    size_t len, cap;
} Vec;

/* 追加元素，必要时倍增扩容；成功返回 0，失败返回 -1 */
static int vec_push(Vec *v, int x) {
    if (v->len == v->cap) {
        size_t ncap = v->cap ? v->cap * 2 : 2;
        int *tmp = realloc(v->data, ncap * sizeof(*tmp));  /* 先用临时指针接收 */
        if (tmp == NULL) {
            return -1;                  /* 原 v->data 仍有效，不会丢失 */
        }
        v->data = tmp;
        v->cap = ncap;
    }
    v->data[v->len++] = x;
    return 0;
}

int main(void) {
    Vec v = {0};                        /* data 为 NULL，realloc(NULL, n) 等价于 malloc */
    for (int i = 0; i < 10; i++) {
        if (vec_push(&v, i * i) != 0) {
            free(v.data);
            return 1;
        }
    }
    printf("len=%zu cap=%zu last=%d\n", v.len, v.cap, v.data[v.len - 1]);
    free(v.data);
    return 0;
}
```

预期输出：`len=10 cap=16 last=81`（容量按 2、4、8、16 倍增）。

**常见错误**：`realloc` 失败时直接覆盖原指针导致泄漏；结构体按值传递带来不必要拷贝；未检查 `NULL`

`realloc` 失败返回 `NULL`，如果直接赋给原指针，原来那块内存就再也找不到了。

```c
/* 错误：失败时 p 变成 NULL，原内存泄漏 */
p = realloc(p, new_size);

/* 正确：先用临时指针接收，成功后再赋值 */
int *tmp = realloc(p, new_size);
if (tmp == NULL) {
    /* 处理失败，p 仍然有效，可继续使用或释放 */
} else {
    p = tmp;
}
```

大结构体按值传递的对照：

```c
/* 不理想：每次调用拷贝整个结构体 */
double area_by_value(Rect r);

/* 推荐：传 const 指针，不拷贝也不会被修改 */
double area_by_ptr(const Rect *r);
```

**应该练习**：实现动态数组（自动扩容的 vector）；用结构体表示点、矩形并计算面积

**使用前检查清单**：
- [ ] 每次 `malloc`/`calloc`/`realloc` 之后都检查了 `NULL`
- [ ] `realloc` 用临时指针接收，成功后才更新原指针
- [ ] 大结构体和需要修改的场合使用指针传参，只读用 `const`
- [ ] 扩容后更新了 `cap`，没有继续使用旧地址（如之前保存的元素指针）
- [ ] 需要清零的内存用 `calloc`，而不是依赖 `malloc` 的内容

**自测小问题**：
1. 值传递和指针传递结构体，各自的优缺点是什么？
2. 为什么 `p = realloc(p, n)` 不安全？
3. `calloc(n, size)` 与 `malloc(n * size)` 有哪些区别？
4. `realloc` 成功后，之前保存的指向旧块内部的指针还能用吗？
5. 倍增扩容比每次加 1 好在哪里？

<details><summary>参考答案</summary>

1. 值传递：安全，函数里改动不影响外部，但要拷贝整个结构体。指针传递：不拷贝、可修改原对象，但要注意空指针和意外修改，只读时加 `const`。
2. 失败时返回 `NULL` 并覆盖了 `p`，原内存地址丢失，造成泄漏。
3. `calloc` 会把内存清零，并且检查 `n * size` 溢出；`malloc` 内容未初始化，乘法溢出需自己处理。
4. 不能，块可能被移动，旧指针成为悬空指针，应通过新的 `data` 和下标重新取址。
5. 倍增使扩容次数是对数级，均摊每次追加的成本为常数；每次加 1 会频繁拷贝，总成本是平方级。

</details>

**进入下一层的标准**：`realloc` 的安全写法（先用临时指针接收）能默写

### L3 熟练

**一句话定义**：能解释结构体在内存中的真实布局（对齐与填充），并用柔性数组、`union`、`enum` 设计紧凑且安全的数据类型，同时分清深拷贝与浅拷贝。

**核心概念**：
- 每个成员按自身对齐要求放置，编译器在成员间和末尾插入填充字节。
- 成员顺序会影响 `sizeof`，把大的成员放前面通常更省空间。
- 柔性数组成员 `T data[];` 必须是最后一个成员，结构体与数据一次分配。
- `union` 的成员共享同一块内存，需要一个标签（`enum`）记录当前有效的成员。
- 浅拷贝只复制指针值，深拷贝复制指针指向的内容。

**知识点详解**：
- 结构体内存对齐与填充：CPU 访问按边界对齐的数据更高效，所以编译器让每个成员的偏移是其对齐值的倍数，并把整体大小补成最大对齐的倍数。可以用 `offsetof` 与 `sizeof` 验证。
- 柔性数组成员：用 `malloc(sizeof(S) + n)` 一次分配头部和变长数据，释放也只需一次 `free`，适合带长度的字符串、报文。
- `union` 与位域：`union` 让多种类型复用同一块内存，节省空间。位域可以按位打包标志，但位序和布局由实现定义，不可移植。
- `enum` 配合 `switch`：用枚举做类型标签，`switch` 逐个处理，开启 `-Wswitch` 时漏掉枚举值会有警告。
- 深拷贝与浅拷贝：结构体赋值是逐成员复制，含指针成员时两个对象会指向同一块内存，各自释放就会重复释放，需要自己写拷贝函数分配新内存。

**需要掌握**：结构体内存对齐与填充；柔性数组成员；`union` 与位域；`enum` 配合 `switch`；深拷贝与浅拷贝

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>
#include <stddef.h>   /* offsetof */
#include <string.h>

struct A { char c; int i; char d; };   /* 布局松散，有填充 */
struct B { int i; char c; char d; };   /* 重排后更紧凑 */

typedef enum { T_INT, T_DBL } Tag;
typedef struct {
    Tag tag;                           /* 标签记录当前有效成员 */
    union { int i; double d; } as;
} Variant;

typedef struct { size_t len; char data[]; } Str;   /* 柔性数组成员 */

static void show(const Variant *v) {
    switch (v->tag) {
    case T_INT: printf("int %d\n", v->as.i); break;
    case T_DBL: printf("double %.2f\n", v->as.d); break;
    }
}

int main(void) {
    printf("A=%zu B=%zu offsetof(A,i)=%zu\n",
           sizeof(struct A), sizeof(struct B), offsetof(struct A, i));
    Variant a = {.tag = T_INT, .as.i = 42};
    Variant b = {.tag = T_DBL, .as.d = 3.14};
    show(&a);
    show(&b);

    Str *s = malloc(sizeof(Str) + 6);  /* 头部与数据一次分配 */
    if (s == NULL) return 1;
    s->len = 5;
    memcpy(s->data, "hello", 6);       /* 含结尾的 '\0' */
    printf("%s %zu\n", s->data, s->len);
    free(s);
    return 0;
}
```

预期输出（典型 64 位平台）：`A=12 B=8 offsetof(A,i)=4`，随后 `int 42`、`double 3.14`、`hello 5`。`sizeof` 的具体数值由平台决定。

**常见错误**：结构体含指针时浅拷贝导致重复释放；依赖位域的内存布局

结构体含指针成员时，直接赋值只复制了指针值：

```c
typedef struct { char *name; } Person;

/* 错误：浅拷贝，p1 与 p2 指向同一块内存，分别 free 会 double free */
Person p2 = p1;

/* 正确：深拷贝，为 p2 分配独立的内存 */
Person p2;
size_t n = strlen(p1.name) + 1;
p2.name = malloc(n);
if (p2.name != NULL) {
    memcpy(p2.name, p1.name, n);
}
```

另外，不要把位域当作协议或文件格式的布局依据，改用显式的位移和掩码。

**应该练习**：实现栈、队列；实现带 `union` 的“变体”类型；用 `offsetof` 验证对齐

**使用前检查清单**：
- [ ] 用 `sizeof` 和 `offsetof` 实际打印验证过结构体的大小和偏移
- [ ] 需要紧凑布局时已调整成员顺序，而不是盲目使用打包指令
- [ ] 柔性数组成员放在结构体最后，分配大小为 `sizeof(S) + 数据长度`
- [ ] `union` 配有标签字段，读取前先按标签判断
- [ ] 含指针成员的结构体写了专门的拷贝函数和销毁函数

**自测小问题**：
1. 为什么 `struct { char c; int i; }` 的大小通常是 8 而不是 5？
2. 调整成员顺序为什么能改变 `sizeof`？
3. 柔性数组成员有什么限制？如何分配与释放？
4. `union` 为什么需要配合标签使用？
5. 浅拷贝为什么会导致重复释放？

<details><summary>参考答案</summary>

1. `int` 要求 4 字节对齐，`c` 后补 3 字节填充，总共 8 字节。
2. 填充取决于相邻成员的对齐要求，把对齐大的成员放前面可以减少中间的填充。
3. 必须是最后一个成员，且结构体至少要有一个其他成员；用 `malloc(sizeof(S) + n)` 分配，用一次 `free` 释放，不能直接按值复制整个对象。
4. `union` 本身不记录当前存的是哪个成员，读错成员会得到无意义的值，所以用 `enum` 标签记录并在读取前判断。
5. 浅拷贝只复制指针值，两个对象指向同一块堆内存，各自销毁时会对同一地址 `free` 两次。

</details>

**进入下一层的标准**：能手算任意结构体的 `sizeof` 并用代码验证

### L4 进阶

**一句话定义**：能设计自己的分配策略（如 arena、引用计数、哈希表）来控制内存生命周期，并用 ASan、Valgrind 等工具定位和修复内存错误的根因。

**核心概念**：
- arena 分配器一次申请大块内存，按需切分，最后整体释放，省去逐个 `free`。
- 分配返回的地址必须满足对齐要求，否则可能出现未定义行为。
- 常见内存错误：越界读写、双重释放、释放后使用、泄漏。
- AddressSanitizer（`-fsanitize=address`）与 Valgrind 能在运行时报告这些错误及调用栈。
- 哈希表需要处理冲突（链地址法或开放寻址）和负载因子触发的扩容；引用计数让共享对象在最后一个使用者离开时释放。

**知识点详解**：
- 内存池与 arena 分配器：同一批对象生命周期相同时，arena 分配只需移动偏移量，速度快，也不会产生碎片，释放时一次归还全部。缺点是不能单独释放某个对象。
- 内存泄漏与越界排查：用 `gcc -g -fsanitize=address,undefined` 编译后运行，错误会给出类型、地址和源码行；Valgrind 无需重新编译，适合已有的可执行文件。
- 哈希表实现：把键映射到桶，冲突时用链表挂接；元素过多时重新分配更大的桶数组并重新插入，保持查找接近常数时间。
- 引用计数：对象里保存使用者数量，增加使用者时加一，释放时减一，减到零才真正 `free`，用来解决共享所有权的释放时机问题，要注意循环引用。

**需要掌握**：内存池与 arena 分配器；内存泄漏与越界排查（ASan、Valgrind）；哈希表实现；引用计数

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>
#include <stddef.h>   /* max_align_t */

typedef struct {
    unsigned char *base;
    size_t cap, used;
} Arena;

static int arena_init(Arena *a, size_t cap) {
    a->base = malloc(cap);
    a->cap = cap;
    a->used = 0;
    return a->base ? 0 : -1;
}

/* 从 arena 切出 n 字节，按最大对齐要求对齐 */
static void *arena_alloc(Arena *a, size_t n) {
    size_t align = _Alignof(max_align_t);
    size_t start = (a->used + align - 1) & ~(align - 1);
    if (start > a->cap || n > a->cap - start) {
        return NULL;                        /* 空间不足 */
    }
    a->used = start + n;
    return a->base + start;
}

static void arena_destroy(Arena *a) {       /* 一次性释放全部对象 */
    free(a->base);
    a->base = NULL;
    a->cap = a->used = 0;
}

typedef struct Node { int val; struct Node *next; } Node;

int main(void) {
    Arena ar;
    if (arena_init(&ar, 1024) != 0) return 1;
    Node *head = NULL;
    for (int i = 0; i < 5; i++) {
        Node *n = arena_alloc(&ar, sizeof(*n));
        if (n == NULL) { arena_destroy(&ar); return 1; }
        n->val = i;
        n->next = head;
        head = n;
    }
    for (Node *p = head; p != NULL; p = p->next) printf("%d ", p->val);
    printf("\nused=%zu\n", ar.used);
    arena_destroy(&ar);                     /* 链表节点无需逐个 free */
    return 0;
}
```

预期输出：`4 3 2 1 0`，随后一行 `used=` 加数值（`Node` 通常为 16 字节，5 个节点为 `used=80`）。

**常见错误**：双重释放（double free）；释放后使用（use-after-free）；内存碎片

释放后继续使用指针，行为未定义，有时看起来“正常”，所以更危险。

```c
/* 错误：释放后仍访问，且可能再次释放 */
free(p);
printf("%d\n", p->val);   /* use-after-free */
free(p);                  /* double free */

/* 正确：释放后立刻置空，free(NULL) 是安全的空操作 */
free(p);
p = NULL;
```

用 ASan 检查：`gcc -g -fsanitize=address -o demo demo.c && ./demo`，错误类型、分配位置和释放位置都会被打印出来。碎片问题可以通过 arena、对象池按固定大小分配来缓解。

**应该练习**：实现 arena 分配器；实现哈希表（含冲突处理与扩容）；故意制造各类内存错误并用工具定位

**使用前检查清单**：
- [ ] 开发和测试构建默认带 `-g -fsanitize=address,undefined`
- [ ] arena 的返回地址满足对齐，并检查了空间不足的情况
- [ ] 每个对象的所有者明确，释放后指针立即置 `NULL`
- [ ] 引用计数的增减成对出现，没有循环引用
- [ ] 哈希表扩容后重新计算了所有元素的桶位置

**自测小问题**：
1. arena 分配器相比逐个 `malloc`/`free` 的优点和缺点各是什么？
2. 为什么 `arena_alloc` 要做对齐处理？
3. use-after-free 为什么有时不会立即崩溃？
4. ASan 与 Valgrind 在使用方式上有什么不同？
5. 引用计数为什么处理不了循环引用？

<details><summary>参考答案</summary>

1. 优点：分配快、无碎片、释放简单；缺点：不能单独释放某个对象，整块生命周期一致才合适。
2. 未对齐的地址访问在某些平台上会出错或变慢，对齐到 `max_align_t` 能满足任何标准类型的要求。
3. 被释放的内存可能仍属于进程且内容未变，访问不会触发硬件异常，错误被隐藏，之后才在别处表现出来。
4. ASan 需要编译时加 `-fsanitize=address` 并重新构建，开销较小；Valgrind 直接运行未插桩的程序，更慢但无需重新编译。
5. 环中的对象互相持有，计数永远不会降到零，因而一直不被释放，需要弱引用或手动断环。

</details>

**进入下一层的标准**：能用工具定位并修复常见内存错误，并说明根因

### L5 独立

**一句话定义**：能在真实项目中建立统一的资源管理约定和检查机制，使所有路径（包括出错路径）都不泄漏，并让这一点由 CI 持续保证。

**核心概念**：
- 每种资源类型提供成对的 `init`/`destroy`（或 `create`/`free`），所有权在文档里写清楚。
- 把分配和释放集中在少数函数，避免到处各自 `malloc`/`free`。
- 函数中有多个资源时，用单一的 `goto cleanup` 出口按需释放。
- 让 `destroy` 能安全处理零初始化或部分初始化的对象，`free(NULL)` 本身是安全的。
- 在 CI 中加入 sanitizer 构建与测试，把内存问题挡在合并之前。

**知识点详解**：
- 资源管理模式（`init`/`destroy` 成对）：每个初始化函数对应一个销毁函数，调用方只要对称使用就不会漏。初始化失败时要自己清理已分配的部分并让对象回到可销毁状态。
- 统一的内存分配策略：约定谁分配谁释放，或者统一走项目自己的分配封装，这样可以集中检查失败、统计用量或替换分配器，所有权不再混乱。
- 异常路径清理（`goto cleanup`）：C 没有异常和析构，多处 `return` 容易漏释放。把清理代码放在函数末尾，所有错误都跳到那里，结构清晰且只维护一份。
- 在项目中建立内存检查的 CI：在构建脚本里增加 ASan/UBSan 的测试目标，每次提交自动运行，发现泄漏或越界直接让构建失败。

**需要掌握**：资源管理模式（`init`/`destroy` 成对）；统一的内存分配策略；异常路径清理（`goto cleanup`）；在项目中建立内存检查的 CI

**真实例子**：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    char *name;
    int *scores;
    size_t n;
} Record;

/* 成功返回 0；失败时自行清理，对象保持可安全 destroy 的状态 */
static int record_init(Record *r, const char *name, size_t n) {
    size_t len = strlen(name) + 1;
    r->name = NULL;
    r->scores = NULL;
    r->n = 0;
    r->name = malloc(len);
    if (r->name == NULL) goto fail;
    memcpy(r->name, name, len);
    r->scores = calloc(n, sizeof(*r->scores));
    if (r->scores == NULL) goto fail;
    r->n = n;
    return 0;
fail:
    free(r->name);
    free(r->scores);
    r->name = NULL;
    r->scores = NULL;
    return -1;
}

static void record_destroy(Record *r) {      /* 对零初始化对象同样安全 */
    free(r->name);
    free(r->scores);
    r->name = NULL;
    r->scores = NULL;
    r->n = 0;
}

int main(void) {
    Record a = {0}, b = {0};
    int rc = EXIT_FAILURE;
    if (record_init(&a, "alice", 3) != 0) goto cleanup;
    if (record_init(&b, "bob", 2) != 0) goto cleanup;
    a.scores[0] = 90;
    b.scores[1] = 80;
    printf("%s:%d %s:%d\n", a.name, a.scores[0], b.name, b.scores[1]);
    rc = EXIT_SUCCESS;
cleanup:                                      /* 所有路径统一出口 */
    record_destroy(&b);
    record_destroy(&a);
    return rc;
}
```

预期输出：`alice:90 bob:80`，且在 ASan/LeakSanitizer 下无任何报告。

**常见错误**：错误路径漏释放；多处各自分配释放，所有权混乱

典型漏释放：第二次分配失败时，第一次分配的内存没有归还。

```c
/* 错误：第二个 malloc 失败时，a 泄漏 */
char *a = malloc(10);
char *b = malloc(20);
if (a == NULL || b == NULL) return -1;

/* 正确：统一从 cleanup 出口释放，free(NULL) 是安全的 */
char *a = malloc(10);
char *b = malloc(20);
int rc = -1;
if (a == NULL || b == NULL) goto cleanup;
/* ... 使用 a 和 b ... */
rc = 0;
cleanup:
free(a);
free(b);
return rc;
```

所有权混乱的解法是写明约定：谁创建谁销毁，或者通过 `xxx_take` 之类的命名表明所有权转移，并在头文件注释里写清楚。

**应该练习**：把一个泄漏的小项目修成零泄漏；在构建脚本中加入 sanitizer 测试

**使用前检查清单**：
- [ ] 每种资源类型都有成对的 `init`/`destroy`，所有权规则写在头文件注释里
- [ ] 所有函数的出错分支都走统一的 `cleanup` 出口，没有中途遗漏的 `return`
- [ ] `destroy` 对零初始化和部分初始化的对象都安全
- [ ] 构建脚本中有 `-fsanitize=address,undefined` 的测试目标
- [ ] CI 在每次提交时运行该目标，并在报告泄漏时失败

**自测小问题**：
1. 为什么要让 `destroy` 能处理零初始化的对象？
2. `goto cleanup` 相比多处 `return` 有什么好处？有什么注意点？
3. 如何向调用者表明“这个指针的所有权转移给你”？
4. 在 CI 里启用 sanitizer 时，应该关注哪些编译选项？
5. 为什么 `free(NULL)` 可以直接调用，而不需要先判断？

<details><summary>参考答案</summary>

1. 这样 `goto cleanup` 出口可以无条件调用 `destroy`，即使某些对象还没初始化成功也不会出错。
2. 清理逻辑只写一份，不容易漏；注意不要跳过变长数组等需要初始化的声明，并保证 cleanup 处用到的变量都已初始化。
3. 在函数命名和文档注释里写明，例如“返回的指针需由调用者 `free`”，或使用 `take`、`create` 等约定词，并在接口层面统一风格。
4. 使用 `-g -fsanitize=address,undefined`，可加 `-fno-omit-frame-pointer` 获得更好的调用栈，并保证测试以非零状态码失败。
5. C 标准规定 `free(NULL)` 什么也不做，这样清理代码不需要为每个指针写条件判断。

</details>

**达标标准**：项目在 sanitizer 下全部测试通过，无泄漏报告



---

## 第 6 章 文件、预处理与工程化

### L1 入门
**一句话定义**：能用标准库安全地读写文本文件，并会用 `#include` 与 `#define` 组织简单程序，每次文件操作都知道失败了怎么办。

**核心概念**：
- `FILE *` 是文件流的句柄，`fopen` 成功返回非空指针，失败返回 `NULL`。
- 打开模式决定行为：`"r"` 读、`"w"` 写（清空）、`"a"` 追加，文本文件即这三种。
- `fprintf`/`fscanf` 是带格式的读写，`fgets` 是按行安全读取。
- 用完必须 `fclose`，缓冲区中的数据才保证落盘。
- `#include` 是文本插入，`#define` 是文本替换，二者都发生在编译之前。

**知识点详解**：
- `fopen`/`fclose`：`fopen` 向操作系统申请一个文件流，`fclose` 刷新缓冲并归还资源。不关闭会泄漏句柄，写入的数据也可能丢失。
- `fprintf`/`fscanf`：用法与 `printf`/`scanf` 相同，只是多了一个流参数。`fscanf` 返回成功匹配的项数，必须用它判断读取是否成功，而不是假设一定成功。
- `fgets`：最多读 `n-1` 个字符并补 `'\0'`，不会像已废弃的 `gets` 那样溢出缓冲区，读行首选它。
- 文本文件读写：文本模式下换行符会按平台转换（Windows 上 `\n` 写成 `\r\n`），所以文本文件用文本模式，不要混用二进制思维。
- `#include` 与 `#define`：`#include <...>` 搜标准路径，`#include "..."` 先搜当前目录。`#define` 常用来给魔数起名，如 `#define N 5`，避免代码里散落意义不明的数字。

**需要掌握**：`fopen`/`fclose`；`fprintf`/`fscanf`；`fgets`；文本文件读写；`#include` 与 `#define`

**真实例子**：
```c
#include <stdio.h>

#define N 5                       // 数组长度，用宏命名魔数

int main(void) {
    int a[N] = {10, 20, 30, 40, 50};
    FILE *fp = fopen("data.txt", "w");
    if (fp == NULL) {             // 每次打开都检查
        perror("fopen data.txt");
        return 1;
    }
    for (int i = 0; i < N; i++) fprintf(fp, "%d\n", a[i]);
    fclose(fp);

    fp = fopen("data.txt", "r");
    if (fp == NULL) { perror("fopen data.txt"); return 1; }
    int b[N], n = 0;
    while (n < N && fscanf(fp, "%d", &b[n]) == 1) n++;  // 检查匹配数
    fclose(fp);

    for (int i = 0; i < n; i++) printf("%d ", b[i]);
    printf("\n共读回 %d 个数\n", n);
    return 0;
}
```
预期输出：`10 20 30 40 50 ` 换行后输出 `共读回 5 个数`，同时当前目录出现 `data.txt`。

**常见错误**：打开文件不检查是否成功；忘记 `fclose`；路径写错（Windows 反斜杠需转义）

错误写法与正确写法对照：
```c
/* 错误：未检查 NULL，路径中的 \t 被当成制表符 */
FILE *fp = fopen("C:\test\data.txt", "r");
fscanf(fp, "%d", &x);             // fp 为 NULL 时直接崩溃

/* 正确：转义反斜杠（或直接用正斜杠），并检查返回值 */
FILE *fp = fopen("C:\\test\\data.txt", "r");
if (fp == NULL) { perror("fopen"); return 1; }
if (fscanf(fp, "%d", &x) != 1) { fprintf(stderr, "读取失败\n"); }
fclose(fp);
```

**应该练习**：把数组写入文件再读回；统计文件行数；复制文本文件

**使用前检查清单**：
- [ ] 每个 `fopen` 之后都立刻检查是否为 `NULL`
- [ ] 每个成功打开的文件都有对应的 `fclose`，包括提前 `return` 的路径
- [ ] `fscanf` 的返回值与期望项数做了比较
- [ ] 读行用 `fgets` 并传入正确的缓冲区大小，不使用 `gets`
- [ ] Windows 路径中的反斜杠已转义，或改用正斜杠

**自测小问题**：
1. `fopen` 失败时返回什么？应当如何报告原因？
2. `"w"` 与 `"a"` 模式有什么区别？
3. 为什么读行要用 `fgets` 而不是 `gets`？
4. `fscanf` 的返回值表示什么？
5. `#define N 5` 与 `const int n = 5;` 在处理阶段上有何不同？

<details><summary>参考答案</summary>

1. 返回 `NULL`；可用 `perror("fopen")` 或 `strerror(errno)` 输出原因。
2. `"w"` 打开时清空原内容，`"a"` 保留原内容并在末尾追加。
3. `gets` 不限制长度，会造成缓冲区溢出，已从 C11 标准中移除；`fgets` 可指定最大长度。
4. 表示成功匹配并赋值的项数，遇到文件末尾且尚未匹配时返回 `EOF`。
5. `#define` 在预处理阶段做文本替换，没有类型和作用域；`const int` 是编译期处理的带类型对象。

</details>

**进入下一层的标准**：每次文件操作都有失败处理

### L2 基础
**一句话定义**：能读写二进制数据、定位文件位置、用 `errno` 诊断错误，并把程序拆成 `.h` + `.c` 多个文件手动编译链接。

**核心概念**：
- 二进制读写按字节原样存取内存，`fread`/`fwrite` 返回实际处理的元素个数。
- `fseek`/`ftell` 移动与查询文件位置，可用来求文件大小或随机访问。
- `errno` 保存最近一次失败的原因，`perror` 把它翻译成可读文字。
- 头文件放声明，源文件放定义；include guard 防止同一头文件被重复展开。
- 编译（每个 `.c` 变成 `.o`）与链接（把 `.o` 合并并解析符号）是两个独立阶段。

**知识点详解**：
- `fread`/`fwrite`：签名为 `fread(ptr, size, count, fp)`，返回成功处理的元素数。返回值小于 `count` 说明出错或到达文件末尾，必须检查。结构体直接写入时要注意填充字节和字节序，文件不一定能跨平台通用。
- `fseek`/`ftell`：`fseek(fp, 0, SEEK_END)` 后 `ftell` 即文件字节数；`SEEK_SET` 从头算，`SEEK_CUR` 从当前位置算。二进制文件上这两个函数行为最可靠。
- `perror` 与 `errno`：`errno` 在 `<errno.h>` 中，只有函数失败后才有意义，成功不会把它清零。`perror("前缀")` 输出“前缀: 原因”。
- 多文件项目：把接口（函数声明、类型）放在 `.h`，实现放在 `.c`，使用者只 `#include` 头文件。这样修改实现不影响调用方。
- include guard：`#ifndef X_H` / `#define X_H` / `#endif` 保证头文件内容只展开一次，避免重复定义类型。
- `extern`：声明“这个变量在别处定义”，头文件里写 `extern int g;`，在且仅在一个 `.c` 里写 `int g;`。

**需要掌握**：二进制读写 `fread`/`fwrite`；`fseek`/`ftell`；`perror` 与 `errno`；多文件项目：`.h` + `.c`；include guard；`extern`

**真实例子**：
```c
#include <stdio.h>
#include <string.h>

typedef struct { int id; char name[16]; double score; } Student;

int main(void) {
    Student s[2] = {{1, "Alice", 91.5}, {2, "Bob", 78.0}};
    FILE *fp = fopen("stu.bin", "wb");
    if (!fp) { perror("fopen"); return 1; }
    if (fwrite(s, sizeof s[0], 2, fp) != 2) { perror("fwrite"); fclose(fp); return 1; }
    fclose(fp);

    fp = fopen("stu.bin", "rb");
    if (!fp) { perror("fopen"); return 1; }
    fseek(fp, 0, SEEK_END);
    long size = ftell(fp);                    // 文件总字节数
    printf("文件大小 %ld，记录数 %ld\n", size, size / (long)sizeof(Student));

    fseek(fp, (long)sizeof(Student), SEEK_SET);  // 直接跳到第 2 条
    Student t;
    if (fread(&t, sizeof t, 1, fp) != 1) { perror("fread"); fclose(fp); return 1; }
    printf("%d %s %.1f\n", t.id, t.name, t.score);
    fclose(fp);
    return 0;
}
```
预期输出：先输出文件大小与记录数 2（大小为 `sizeof(Student)` 的 2 倍，常见平台为 64），再输出 `2 Bob 78.0`。

多文件拆分示例（`gcc main.c util.c -o app` 编译）：
```c
/* util.h */
#ifndef UTIL_H
#define UTIL_H
int add(int a, int b);          /* 只放声明 */
extern int call_count;          /* 声明，不分配空间 */
#endif

/* util.c */
#include "util.h"
int call_count = 0;             /* 定义只在这里一次 */
int add(int a, int b) { call_count++; return a + b; }

/* main.c */
#include <stdio.h>
#include "util.h"
int main(void) {
    int r = add(2, 3);          /* 先调用，再读计数，避免求值顺序歧义 */
    printf("%d %d\n", r, call_count);
    return 0;
}
```
预期输出：`5 1`。

**常见错误**：头文件里定义变量导致重复定义；忘记 include guard；`fread` 返回值未检查

错误写法与正确写法对照：
```c
/* 错误：config.h 中定义变量，被两个 .c 包含后链接报 multiple definition */
int debug_level = 0;

/* 正确：头文件声明，一个 .c 文件定义 */
/* config.h */  extern int debug_level;
/* config.c */  int debug_level = 0;
```

**应该练习**：把学生信息结构体存成二进制文件；把一个程序拆成 3 个源文件

**使用前检查清单**：
- [ ] 二进制文件使用 `"rb"`/`"wb"` 模式打开
- [ ] `fread`/`fwrite` 的返回值与期望元素数比较
- [ ] 每个头文件都有 include guard
- [ ] 头文件中只有声明，全局变量用 `extern` 声明、在唯一 `.c` 中定义
- [ ] 失败时调用 `perror` 或打印 `strerror(errno)`，并在读取前不依赖旧的 `errno`

**自测小问题**：
1. `fread(buf, 4, 10, fp)` 返回 7 意味着什么？
2. 如何用 `fseek` 与 `ftell` 求文件大小？
3. include guard 解决什么问题？
4. 为什么不能在头文件里写 `int g = 0;`？
5. 编译与链接分别做什么？`undefined reference` 属于哪个阶段的错误？

<details><summary>参考答案</summary>

1. 只成功读到 7 个元素，可能到达文件末尾或发生错误，需用 `feof`/`ferror` 区分。
2. `fseek(fp, 0, SEEK_END)` 后调用 `ftell(fp)`，返回值就是字节数。
3. 防止同一头文件在一个编译单元中被重复展开，导致类型重复定义。
4. 每个包含它的 `.c` 都会产生一份定义，链接时报重复定义。
5. 编译把每个 `.c` 翻译成目标文件；链接把目标文件和库合并并解析符号。`undefined reference` 是链接阶段错误。

</details>

**进入下一层的标准**：能手动用 `gcc a.c b.c -o app` 编译多文件程序并解释链接过程

### L3 熟练
**一句话定义**：能用 Makefile 或 CMake 管理多文件项目，用条件编译与安全的宏控制行为，并会制作和使用静态库、动态库。

**核心概念**：
- 构建系统按“目标、依赖、命令”增量重建，只编译改动过的文件。
- 条件编译 `#ifdef`/`#if` 在预处理阶段决定哪些代码参与编译。
- 宏是纯文本替换，没有类型也不求值一次，所以参数和整体都要加括号。
- 静态库（`.a`）在链接时拷入可执行文件，动态库（`.so`/`.dll`）在运行时加载。
- `assert` 检查程序员假设，日志宏在调试与发布版之间切换。

**知识点详解**：
- Makefile 或 CMake：Makefile 靠依赖关系和时间戳决定重编译哪些文件；CMake 则生成 Makefile 或其他构建文件，便于跨平台。核心价值是“一条命令可重复构建”。
- 条件编译 `#ifdef`：常用于调试开关（`-DDEBUG`）、平台差异和头文件保护。未启用的分支不会进入编译，也就没有运行开销。
- 带参宏的陷阱：`#define SQ(x) x*x` 遇到 `SQ(a+1)` 会展开成 `a+1*a+1`。参数和整体都要加括号，且参数若带副作用（如 `i++`）会被多次求值。
- `do { } while (0)` 惯用法：让多语句宏在 `if (c) MACRO(); else ...` 中表现得像一条语句，不会破坏 `else` 的配对。
- 静态库与动态库：静态库用 `ar rcs libx.a x.o` 打包，链接时 `-L. -lx`；动态库用 `-shared -fPIC` 生成，运行时要能找到它。
- `assert` 与日志宏：`assert(cond)` 失败时终止并报告位置，定义 `NDEBUG` 后消失，所以不能把有副作用的语句放进去。日志宏借助 `__FILE__`、`__LINE__` 和 `__VA_ARGS__` 输出上下文。

**需要掌握**：Makefile 或 CMake 基础；条件编译 `#ifdef`；带参宏的陷阱与 `do { } while (0)` 惯用法；静态库与动态库；`assert` 与日志宏

**真实例子**：
```c
/* log.c：用 gcc -DDEBUG log.c -o log 编译开启日志，不加则关闭 */
#include <stdio.h>
#include <assert.h>

#ifdef DEBUG
#define LOG(fmt, ...) \
    do { fprintf(stderr, "[%s:%d] " fmt "\n", __FILE__, __LINE__, __VA_ARGS__); } while (0)
#else
#define LOG(fmt, ...) do { } while (0)
#endif

#define SQUARE(x) ((x) * (x))     // 参数与整体都加括号

static int divide(int a, int b) {
    assert(b != 0);               // 记录“除数不为 0”的假设
    return a / b;
}

int main(void) {
    int n = 3;
    LOG("n = %d", n);
    printf("SQUARE(n+1) = %d\n", SQUARE(n + 1));
    printf("divide = %d\n", divide(10, 2));
    return 0;
}
```
预期输出：`SQUARE(n+1) = 16`、`divide = 5`；使用 `-DDEBUG` 时 stderr 额外出现 `[log.c:21] n = 3`（行号以实际文件为准）。

配套 Makefile（命令行缩进必须是 Tab）：
```makefile
CC     = gcc
CFLAGS = -std=c11 -Wall -Wextra -g
OBJS   = main.o util.o

app: $(OBJS)
	$(CC) $(OBJS) -o app

%.o: %.c util.h
	$(CC) $(CFLAGS) -c $< -o $@

libutil.a: util.o
	ar rcs $@ $^

clean:
	rm -f $(OBJS) app libutil.a
.PHONY: clean
```
执行 `make` 构建，修改 `util.h` 后再 `make` 会重新编译全部对象，`make clean` 清理产物。

**常见错误**：宏参数未加括号；Makefile 依赖写漏导致不重新编译；头文件循环包含

错误写法与正确写法对照：
```c
/* 错误：参数未加括号，且多语句宏在 if/else 中会出错 */
#define SQ(x)   x * x
#define SWAP(a, b) int t = a; a = b; b = t;
if (c) SWAP(x, y); else foo();     // 展开后 else 悬空，编译失败

/* 正确 */
#define SQ(x)   ((x) * (x))
#define SWAP(a, b) do { int t = (a); (a) = (b); (b) = t; } while (0)
```

**应该练习**：写一个 Makefile 管理多文件项目；制作静态库并链接使用；添加调试开关宏

**使用前检查清单**：
- [ ] 宏的每个参数和整个表达式都有括号
- [ ] 多语句宏用 `do { } while (0)` 包裹
- [ ] Makefile 的 `.o` 规则列出了所依赖的头文件
- [ ] 提供 `clean` 目标并声明为 `.PHONY`
- [ ] `assert` 内不含有副作用的表达式
- [ ] 头文件互相包含时用前向声明或拆分来避免循环

**自测小问题**：
1. `#define SQ(x) x*x` 中 `SQ(2+3)` 的结果是多少？为什么？
2. 为什么多语句宏要写成 `do { ... } while (0)`？
3. Makefile 为什么只改了头文件却没触发重编译？如何修复？
4. 静态库与动态库在链接和部署上有什么区别？
5. 为什么不要把 `i++` 放进 `assert` 里？

<details><summary>参考答案</summary>

1. 展开为 `2+3*2+3`，结果是 11，不是 25，因为宏只做文本替换。
2. 这样宏在语法上等价于一条以分号结尾的语句，在 `if`/`else` 中不会破坏结构。
3. `.o` 规则没有把头文件列为依赖；加上 `util.h` 依赖，或用 `-MMD -MP` 自动生成依赖。
4. 静态库在链接时拷入可执行文件，部署简单但体积大；动态库在运行时加载，可共享更新，但需要保证运行时能找到它。
5. 定义 `NDEBUG` 后 `assert` 被整个移除，`i++` 也随之消失，导致调试版与发布版行为不同。

</details>

**进入下一层的标准**：新环境中一条命令即可构建项目

### L4 进阶
**一句话定义**：能为模块写自动化测试，用 gdb 和性能工具定位问题，并理解缓冲 I/O 与系统调用、基本多线程带来的新风险。

**核心概念**：
- 单元测试把一个函数放在受控输入下验证，失败要能自动报告并让进程返回非 0。
- gdb 让程序停在断点，观察变量、监视内存变化、查看调用栈。
- 性能分析先测量再优化，`gprof` 给出函数耗时，`perf` 给出采样热点。
- 缓冲 I/O 在用户空间合并读写，系统调用每次都陷入内核，开销不同。
- 多线程共享内存，没有同步的读写就是数据竞争，结果不可预测。

**知识点详解**：
- 单元测试（Unity、cmocka 或自写测试宏）：最小方案是一个 `CHECK(cond)` 宏，失败时打印文件行号并累计失败数，`main` 最后返回失败数。框架在此基础上提供断言种类、测试分组和桩函数。
- gdb 调试：`break` 设断点，`run` 启动，`next`/`step` 单步，`print` 看值，`watch var` 在变量被改写时停下，`backtrace` 看谁调用了谁。编译时需要 `-g`。
- 性能分析：`gcc -pg` 加 `gprof` 看函数级耗时；Linux 上 `perf record` 与 `perf report` 看热点。先找瓶颈再动手，否则优化的多半是错的地方。
- 缓冲 I/O 与系统调用差异：`fwrite` 先写入用户态缓冲，满了或 `fflush` 才调用 `write`。小块数据频繁直接调用 `write` 会慢得多；程序崩溃时缓冲区里的数据也可能丢失。
- 基本多线程（`pthread`）：`pthread_create` 启动、`pthread_join` 等待，共享数据用 `pthread_mutex_lock`/`unlock` 保护。链接时需加 `-pthread`。

**需要掌握**：单元测试（Unity、cmocka 或自写测试宏）；gdb 调试（断点、watch、backtrace）；性能分析（`gprof`、`perf`）；缓冲 I/O 与系统调用差异；基本多线程（`pthread`）

**真实例子**：
```c
// test_counter.c：gcc -std=c11 -Wall -pthread test_counter.c -o t
#include <stdio.h>
#include <pthread.h>

static int failures = 0;
#define CHECK(cond) do { if (!(cond)) { \
    fprintf(stderr, "FAIL %s:%d: %s\n", __FILE__, __LINE__, #cond); failures++; } } while (0)

static long counter = 0;
static pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

static void *worker(void *arg) {
    (void)arg;
    for (int i = 0; i < 100000; i++) {
        pthread_mutex_lock(&lock);      // 去掉加锁则出现数据竞争
        counter++;
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}

int main(void) {
    pthread_t t[4];
    for (int i = 0; i < 4; i++) pthread_create(&t[i], NULL, worker, NULL);
    for (int i = 0; i < 4; i++) pthread_join(t[i], NULL);
    CHECK(counter == 400000);
    CHECK(1 + 1 == 2);
    printf(failures ? "有 %d 项失败\n" : "全部通过\n", failures);
    return failures != 0;
}
```
预期输出：`全部通过`，进程退出码为 0；若删去加锁，`CHECK` 大概率失败并打印失败位置。

**常见错误**：测试覆盖不到错误路径；多线程数据竞争；只在调试版测试

错误写法与正确写法对照：
```c
/* 错误：只测正常路径，且线程间无保护地累加 */
CHECK(parse("42") == 42);
void *worker(void *a) { for (...) counter++; return NULL; }   // 数据竞争

/* 正确：同时测错误输入，并保护共享数据 */
CHECK(parse("42") == 42);
CHECK(parse("abc") == -1);          // 错误路径
CHECK(parse(NULL) == -1);           // 空指针
void *worker(void *a) { for (...) { pthread_mutex_lock(&lock); counter++; pthread_mutex_unlock(&lock); } return NULL; }
```

**应该练习**：为数据结构库写完整测试；用 gdb 定位一次段错误；用 `-fsanitize=thread` 检查竞争

**使用前检查清单**：
- [ ] 每个公开函数至少有正常、边界、错误三类测试
- [ ] 测试失败时进程返回非 0，便于脚本和 CI 判断
- [ ] 同时用调试版和 `-O2` 发布版跑过测试
- [ ] 带 `-g` 编译后用 gdb 复现过一次崩溃并看过 `backtrace`
- [ ] 所有线程共享的数据都有锁或其他同步手段
- [ ] 用 `-fsanitize=address,undefined` 与 `-fsanitize=thread` 各跑过一遍

**自测小问题**：
1. 为什么测试程序的退出码很重要？
2. gdb 中 `break`、`watch`、`backtrace` 各自的用途是什么？
3. 数据竞争是什么？为什么它在调试版里可能“消失”？
4. `fwrite` 与 `write` 的主要区别是什么？
5. 线程程序编译时为什么要加 `-pthread`？

<details><summary>参考答案</summary>

1. 脚本和持续集成靠退出码判断成败，非 0 表示有测试失败。
2. `break` 在指定位置停下；`watch` 在变量被修改时停下；`backtrace` 显示当前调用栈。
3. 多个线程无同步地访问同一数据且至少一个在写；调试版时序和优化不同，问题可能被掩盖，换成发布版或换机器才暴露。
4. `fwrite` 带用户态缓冲，积累后才调用 `write`；`write` 每次直接进入内核，开销大但数据立即交给操作系统。
5. 让编译器和链接器启用线程支持并链接 pthread 库。

</details>

**进入下一层的标准**：每个模块都有自动化测试，并能一键运行

### L5 独立
**一句话定义**：能独立从需求出发完成一个上千行的 C 项目，包括模块设计、错误处理、构建、测试、文档与版本管理，并能读懂和改动成熟开源代码。

**核心概念**：
- 先设计后编码：需求、模块边界、接口契约先于实现。
- 模块低耦合：对外只暴露头文件接口，内部细节用不透明指针隐藏。
- 统一的错误处理约定：返回码或错误枚举，谁分配谁释放。
- 质量闭环：一键构建、自动测试、sanitizer、静态检查。
- 协作工具：Git 提交历史清晰，代码审查发现设计与安全问题。

**知识点详解**：
- 完整项目设计：先写清楚要解决什么问题和不做什么，再划分模块并定义接口，最后实现。一开始就写代码，通常会在后期因为接口不合适而大规模返工。
- 版本管理（Git）：小步提交、提交信息说明原因，分支隔离实验。这样出问题时能二分定位、回滚，也方便别人审查。
- 阅读并改动成熟开源 C 项目：先编译运行，再用 `grep`/`ctags` 追踪入口与数据结构，从修一个小 bug 或加一个小功能开始。能读懂别人的代码风格与约定，是独立工作的重要能力。
- 代码审查：检查接口是否清晰、错误路径是否释放资源、边界是否检查、是否有测试。审查别人代码也在训练自己的判断力。

**需要掌握**：完整项目设计：需求、模块划分、接口、错误处理、构建、测试、文档；版本管理（Git）；阅读并改动成熟开源 C 项目；代码审查

**真实例子**：
```c
/* kv.h：键值存储模块的对外接口，内部结构对调用方不可见 */
#ifndef KV_H
#define KV_H
#include <stddef.h>
typedef struct KV KV;                          /* 不透明类型 */
typedef enum { KV_OK = 0, KV_ERR_NOMEM, KV_ERR_NOTFOUND, KV_ERR_ARG } KVStatus;
KV      *kv_create(void);
void     kv_destroy(KV *kv);
KVStatus kv_put(KV *kv, const char *key, int value);
KVStatus kv_get(const KV *kv, const char *key, int *out);
#endif
```

```c
/* kv.c：简单链表实现，每个函数都检查参数并返回明确状态 */
#include <stdlib.h>
#include <string.h>
#include "kv.h"

typedef struct Node { char *key; int value; struct Node *next; } Node;
struct KV { Node *head; };

KV *kv_create(void) { return calloc(1, sizeof(KV)); }

void kv_destroy(KV *kv) {
    if (!kv) return;
    for (Node *n = kv->head, *next; n; n = next) { next = n->next; free(n->key); free(n); }
    free(kv);
}

KVStatus kv_put(KV *kv, const char *key, int value) {
    if (!kv || !key) return KV_ERR_ARG;
    for (Node *n = kv->head; n; n = n->next)
        if (strcmp(n->key, key) == 0) { n->value = value; return KV_OK; }
    Node *n = malloc(sizeof *n);
    if (!n) return KV_ERR_NOMEM;
    n->key = malloc(strlen(key) + 1);
    if (!n->key) { free(n); return KV_ERR_NOMEM; }   /* 失败路径不泄漏 */
    strcpy(n->key, key);
    n->value = value; n->next = kv->head; kv->head = n;
    return KV_OK;
}

KVStatus kv_get(const KV *kv, const char *key, int *out) {
    if (!kv || !key || !out) return KV_ERR_ARG;
    for (const Node *n = kv->head; n; n = n->next)
        if (strcmp(n->key, key) == 0) { *out = n->value; return KV_OK; }
    return KV_ERR_NOTFOUND;
}
```

```c
/* main.c：用 gcc -std=c11 -Wall -Wextra -fsanitize=address,undefined main.c kv.c -o kvdemo 构建 */
#include <stdio.h>
#include "kv.h"

int main(void) {
    KV *kv = kv_create();
    if (!kv) { fprintf(stderr, "内存不足\n"); return 1; }
    kv_put(kv, "apple", 3);
    kv_put(kv, "pear", 5);
    kv_put(kv, "apple", 7);                    /* 覆盖旧值 */
    int v;
    if (kv_get(kv, "apple", &v) == KV_OK) printf("apple=%d\n", v);
    if (kv_get(kv, "banana", &v) == KV_ERR_NOTFOUND) printf("banana 不存在\n");
    kv_destroy(kv);
    return 0;
}
```
预期输出：`apple=7` 与 `banana 不存在`，AddressSanitizer 不报任何泄漏或越界。

**常见错误**：一开始就写代码不设计；模块耦合严重；没有测试就重构

错误写法与正确写法对照：
```c
/* 错误：接口暴露内部结构，调用方直接改字段，模块无法演进 */
struct KV { Node *head; int count; };      /* 写在 .h 里 */
kv->count = 0;                             /* 调用方绕过接口 */

/* 正确：头文件只有不透明类型和函数，状态变化都经过接口 */
typedef struct KV KV;
KVStatus kv_put(KV *kv, const char *key, int value);
```

**应该练习**：独立完成一个 1000 行以上的项目，例如：简易 shell、键值存储、HTTP 小服务器、文本编辑器、内存分配器

**使用前检查清单**：
- [ ] 动手前写了需求说明、模块划分和每个接口的错误约定
- [ ] 每个模块有自己的测试，修改前先保证测试全绿
- [ ] 一条命令完成构建与测试（如 `make test`）
- [ ] AddressSanitizer、UBSan 全部通过，无泄漏
- [ ] 提交信息清晰，README 写明构建、用法与设计要点
- [ ] 找人做过一次代码审查，并处理了反馈

**自测小问题**：
1. 为什么要用不透明指针隐藏结构体细节？
2. 一个模块的错误处理约定应当包含哪些内容？
3. 为什么说“没有测试就重构”很危险？
4. 读一个陌生的开源 C 项目，应该从哪些步骤入手？
5. 代码审查时在资源管理方面主要看什么？

<details><summary>参考答案</summary>

1. 调用方只能通过接口访问，内部实现可以随时修改而不影响调用方，降低耦合。
2. 约定返回值或状态枚举的含义、谁分配谁释放内存、失败时是否保持对象有效、是否使用 `errno`。
3. 无法确认改动后行为没变，错误会被悄悄引入，只能到运行时才发现。
4. 先编译并运行，找到入口和核心数据结构，用搜索工具追踪调用关系，再从修复一个小问题开始。
5. 每条分配都有对应释放，所有错误路径都没有泄漏，也没有重复释放或释放后使用。

</details>

**达标标准（完成学习）**：项目有清晰的模块划分与文档，构建一键完成，测试和 sanitizer 全绿，别人能读懂并扩展


---

# 附录：学习路线与项目建议

| 阶段 | 参考时间 | 推荐项目 |
|---|---|---|
| L1 → L2 | 2~4 周 | 学生成绩管理（控制台） |
| L2 → L3 | 1~2 月 | 通讯录（文件存储）、字符串工具库 |
| L3 → L4 | 2~3 月 | 链表/哈希表库、简易 JSON 解析器 |
| L4 → L5 | 3~6 月 | 键值数据库、简易 shell、HTTP 服务器 |

推荐资料：《C Primer Plus》（入门）、《C 程序设计语言》K&R（经典）、《C 陷阱与缺陷》、《深入理解计算机系统》（进阶）。

调试与检查工具：`gcc -Wall -Wextra -g`、AddressSanitizer、UndefinedBehaviorSanitizer、Valgrind、gdb、`clang-tidy`、`cppcheck`。
