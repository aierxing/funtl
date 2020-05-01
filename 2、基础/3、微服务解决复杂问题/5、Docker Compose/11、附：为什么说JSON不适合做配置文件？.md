# [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#附：为什么说-json-不适合做配置文件？)附：为什么说 JSON 不适合做配置文件？

> 很多项目使用 JSON 作为配置文件，最明显的例子就是 npm 和 yarn 使用的 package.json 文件。当然，还有很多其他文件，例如 CloudFormation（最初只有 JSON，但现在也支持 YAML）和 composer（PHP）。

但是，JSON 实际上是一种非常糟糕的配置语言。别误会我的意思，我其实是喜欢 JSON 的。它是一种相对灵活的文本格式，对于机器和人类来说都很容易阅读，而且是一种非常好的数据交换和存储格式。但作为一种配置语言，它有它的不足。

## [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#为什么流行使用-json-作为配置语言？)为什么流行使用 JSON 作为配置语言？

将 JSON 用作配置文件有几个方面的原因，其中最大的原因可能是它很容易实现。很多编程语言的标准库都支持 JSON，开发人员或用户可能已经很熟悉 JSON，所以不需要学习新的配置格式就可以使用那些产品。现在几乎所有的工具都提供 JSON 支持，包括语法突出显示、自动格式化、验证工具等。

这些都是很好的理由，但这种无处不在的格式其实不适合用作配置。

## [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#json-的问题)JSON 的问题

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#缺乏注释)缺乏注释

注释对于配置语言而言绝对是一个重要的功能。注释可用于标注不同的配置选项、解释为什么要配置成特定的值，更重要的是，在使用不同的配置进行测试和调试时需要临时注释掉部分配置。当然，如果只是把 JSON 当作是一种数据交换格式，那么就不需要用到注释。

我们可以通过一些方法给 JSON 添加注释。一种常见的方法是在对象中使用特殊的键作为注释，例如“//”或“__comment”。但是，这种语法的可读性不高，并且为了在单个对象中包含多个注释，需要为每个注释使用唯一的键。David Crockford（JSON 的发明者）建议使用预处理器来删除注释。如果你的应用程序需要使用 JSON 作为配置，那么完全没问题，不过这确实带来了一些额外的工作量。

一些 JSON 库允许将注释作为输入。例如，Ruby 的 JSON 模块和启用了 JsonParser.Feature.ALLOW_COMMENTS 功能的 Java Jackson 库可以处理 JavaScript 风格的注释。但是，这不是标准的方式，而且很多编辑器无法正确处理 JSON 文件中的注释，这让编辑它们变得更加困难。

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#过于严格)过于严格

JSON 规范非常严格，这也是为什么实现 JSON 解析器会这么简单，但在我看来，它还会影响可读性，并且在较小程度上会影响可写性。

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#低信噪比)低信噪比

与其他配置语言相比，JSON 显得非常嘈杂。JSON 的很多标点符号对可读性毫无帮助，况且，对象中的键几乎都是标识符，所以键的引号其实是多余的。

此外，JSON 需要使用花括号将整个文档包围起来，所以 JSON 是 JavaScript 的子集，并在流中发送多个对象时用于界定不同的对象。但是，对于配置文件来说，最外面的大括号其实没有任何用处。在配置文件中，键值对之间的逗号也是没有必要的。通常情况下，每行只有一个键值对，所以使用换行作为分隔符更有意义。

说到逗号，JSON 居然不允许在结尾出现逗号。如果你需要在每个键值对之后使用逗号，那么至少应该接受结尾的逗号，因为有了结尾的逗号，在添加新条目时会更容易，而且在进行 commit diff 时也更清晰。

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#长字符串)长字符串

JSON 作为配置格式的另一个问题是，它不支持多行字符串。如果你想在字符串中换行，必须使用 “\n” 进行转义，更糟糕的是，如果你想要一个字符串在文件中另起一行显示，那就彻底没办法了。如果你的配置项里没有很长的字符串，那就不是问题。但是，如果你的配置项里包括了长字符串，例如项目描述或 GPG 密钥，你可能不希望只是使用 “\n” 来转义而不是使用真实的换行符。

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#数字)数字

此外，在某些情况下，JSON 对数字的定义可能会有问题。JSON 规范中将数字定义成使用十进制表示的任意精度有限浮点数。对于大多数应用程序来说，这没有问题。但是，如果你需要使用十六进制表示法或表示无穷大或 NaN 等值时，那么 TOML 或 YAML 将能够更好地处理它们。

```text
{

  "name": "example",

  "description": "A really long description that needs multiple lines.\nThis is a sample project to illustrate why JSON is not a good configuration format. This description is pretty long, but it doesn't have any way to go onto multiple lines.",

  "version": "0.0.1",

  "main": "index.js",

  "//": "This is as close to a comment as you are going to get",

  "keywords": ["example", "config"],

  "scripts": {

    "test": "./test.sh",

    "do_stuff": "./do_stuff.sh"

  },

  "bugs": {

    "url": "https://example.com/bugs"

  },

  "contributors": [{

    "name": "John Doe",

    "email": "johndoe@example.com"

  }, {

    "name": "Ivy Lane",

    "url": "https://example.com/ivylane"

  }],

  "dependencies": {

    "dep1": "^1.0.0",

    "dep2": "3.40",

    "dep3": "6.7"

  }

}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53

## [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#json-的替代方案)JSON 的替代方案

选择哪一种配置语言取决于你的应用程序。每种语言都有各自的优缺点，下面列出了一些可以考虑的选项。它们都是为配置而设计的语言，每一种都比 JSON 这样的数据语言更好。

```text
name = "example"

description = """

A really long description that needs multiple lines.

This is a sample project to illustrate why JSON is not a \

good configuration format. This description is pretty long, \

but it doesn't have any way to go onto multiple lines."""



version = "0.0.1"

main = "index.js"

# This is a comment

keywords = ["example", "config"]



[bugs]

url = "https://example.com/bugs"



[scripts]



test = "./test.sh"

do_stuff = "./do_stuff.sh"



[[contributors]]

name = "John Doe"

email = "johndow@example.com"



[[contributors]]

name = "Ivy Lane"

url = "https://example.com/ivylane"



[dependencies]



dep1 = "^1.0.0"

# Why we depend on dep2

dep2 = "3.40"

dep3 = "6.7"
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#hjson)HJSON

HJSON 是一种基于 JSON 的格式，但具有更大的灵活性，可读性也更强。它支持注释、多行字符串、不带引号的键和字符串，以及可选的逗号。如果你想要 JSON 结构的简单性，同时对配置文件更友好，那么可以考虑 HJSON。有一些可以将 HJSON 转换为 JSON 的命令行工具，如果你使用的工具是基于 JSON 的，可以先用 HJSON 编写配置，然后再转换成 JSON。JSON5 是另一个与 HJSON 非常相似的配置语言。

```text
{

  name: example

  description: '''

  A really long description that needs multiple lines.

  This is a sample project to illustrate why JSON is 

  not a good configuration format.  This description 

  is pretty long, but it doesn't have any way to go 

  onto multiple lines.

  '''

  version: 0.0.1

  main: index.js

  # This is a a comment

  keywords: ["example", "config"]

  scripts: {

    test: ./test.sh

    do_stuff: ./do_stuff.sh

  }

  bugs: {

    url: https://example.com/bugs

  }

  contributors: [{

    name: John Doe

    email: johndoe@example.com

  } {

    name: Ivy Lane

    url: https://example.com/ivylane

  }]

  dependencies: {

    dep1: ^1.0.0

    # Why we have this dependency

    dep2: "3.40"

    dep3: "6.7"

  }

}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#hocon)HOCON

HOCON 是为 Play 框架设计的配置格式，在 Scala 项目中非常流行。它是 JSON 的超集，因此可以使用现有的 JSON 文件。除了注释、可选逗号和多行字符串这些标准特性外，HOCON 还支持从其他文件导入和引用其他值的键，避免重复代码，并使用以点作为分隔符的键来指定值的路径，因此用户可以不必将所有值直接放在花括号对象中。

```text
name = example

description = """

A really long description that needs multiple lines.



This is a sample project to illustrate why JSON is 

not a good configuration format.  This description 

is pretty long, but it doesn't have any way to go 

onto multiple lines.

"""

version = 0.0.1

main = index.js

# This is a a comment

keywords = ["example", "config"]

scripts {

  test = ./test.sh

  do_stuff = ./do_stuff.sh

}

bugs.url = "https://example.com/bugs"

contributors = [

  {

    name = John Doe

    email = johndoe@example.com

  }

  {

    name = Ivy Lane

    url = "https://example.com/ivylane"

  }

]

dependencies {

  dep1 = ^1.0.0

  # Why we have this dependency

  dep2 = "3.40"

  dep3 = "6.7"

}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#yaml)YAML

YAML（YAML 不是标记语言）是一种非常灵活的格式，几乎是 JSON 的超集，已经被用在一些著名的项目中，如 Travis CI、Circle CI 和 AWS CloudFormation。YAML 的库几乎和 JSON 一样无处不在。除了支持注释、换行符分隔、多行字符串、裸字符串和更灵活的类型系统之外，YAML 也支持引用文件，以避免重复代码。

YAML 的主要缺点是规范非常复杂，不同的实现之间可能存在不一致的情况。它将缩进视为严格语法的一部分（类似于 Python），有些人喜欢，有些人不喜欢。这会让复制和粘贴变得很麻烦。

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#脚本语言)脚本语言

如果你的应用程序是使用 Python 或 Ruby 等脚本语言开发的，并且你知道配置的来源是可靠的，那么最好的选择可能就是使用这些语言进行配置。如果你需要一个真正灵活的配置选项，也可以在编译语言中嵌入诸如 Lua 之类的脚本语言。这样可以获得脚本语言的灵活性，而且比使用不同的配置语言更容易实现。使用脚本语言的缺点是它可能过于强大，当然，如果配置来源是不受信任的，可能会引入严重的安全问题。

### [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#自定义配置格式)自定义配置格式

如果由于某种原因，键值配置格式不能满足你的要求，并且由于性能或大小限制而无法使用脚本语言，那么可以考虑自定义配置格式。如果是这种情况，那么在做出选择之前要想清楚，因为你不仅要编写和维护一个解析器，还要让你的用户熟悉另一种配置格式。

## [#](https://funtl.com/zh/docker-compose/为什么说-JSON-不适合做配置文件.html#结论)结论

有了这么多更好的配置语言，没有理由还要使用 JSON。如果要创建需要用到配置的新应用程序、框架或库，请选择 JSON 以外的其他选项。

英文原文：https://www.lucidchart.com/techblog/2018/07/16/why-json-isnt-a-good-configuration-language/

上次更新: 2018-12-30 15:32:03

← [YAML 配置文件语言](https://funtl.com/zh/docker-compose/YAML-配置文件语言.html)