# doc

```yaml title=""
%YAML 1.2
---
# See http://www.sublimetext.com/docs/3/syntax.html
name: Move
file_extensions:
  - move
scope: source.move

contexts:
  # The prototype context is prepended to all contexts but those setting
  # meta_include_prototype: false.
  prototype:
    - include: comments
```

- `\b(if|else|for|while)\b`中的`\b`是用来确保单词边界；

1. context

```yaml title=""
contexts:
  main:
    - match: \b(if|else|for|while)\b
      scope: keyword.control.c
    - match: '"' # 匹配一个双引号，注意双引号的写法
      push: string # 压入一个新的context: string（即下面的内容将使用该context，而非main的上下文，直至string上下文出栈）

  string:
    - meta_scope: string.quoted.double.c # 在string context在栈内时，该scope适用所有的文本
    - match: \\.
      scope: constant.character.escape.c
    - match: '"' # 遇到
      pop: true  # 
```
- 通过`Ctrl+Alt+Shift+P`查看哪些scope被应用到当前文本；
- 该string context有两个模式：第一个匹配反斜杠字符（后面是任何其它字符）；第二个匹配一个双引号。
- 注意：最后一个模式指定了一个动作，当遇到未转义的引号时，string context将从栈中弹出，回到main context
- 当一个context有多个模式，当多个模式在同一位置匹配时，第一个定义的模式将被选中

2. meta pattern

meta_scope: 将给定的scope用给context中所有的文本，包括将context压入和弹出的模式

meta_content_scope：同上，但不适用于触发上下文的文本（如上例子，就不会应用到引号上）
meta_include_prototype：用来阻止当前上下文自动包括原型上下文

clear_scopes：这个设置允许从当前堆栈中删除作用域名称。它可以是一个整数，也可以是删除所有作用域名称的值true。它被应用在meta_scope和meta_content_scope之前。这通常只在一种语法嵌入另一种语法时使用。

meta_prepend：一个布尔值，在继承过程中控制上下文名称冲突的解决。如果指定了这个，这个上下文中的规则将被插入到祖先语法定义中具有相同名称的上下文的任何现有规则`之前`

meta_append：一个布尔值，在继承过程中控制上下文名称冲突的解决。如果指定了这个，这个上下文中的规则将被插入到祖先语法定义中具有相同名称的上下文的任何现有规则`之后`

3. MATCH PATTERNS

每个模式匹配可以包括下面的关键词：
- match：正则匹配
- scope：针对匹配文本使用的scope
- captures：一个数字到scope的映射，将scope应用给正则匹配所捕获的部分
- push：要压入当前栈的新的上下文（一个或多个或匿名）
- pop：从栈中弹出一个context（给定正整数的话，可以弹出多个context）（可以和push, set, embed, branch组合使用，将在其它action执行之前弹出context）
- set：类似push，但是会先弹出一个context；
- embed：类似push，但遇到escape模式就会跳出任意数量的嵌套context中跳出来（很适合：在一个syntax中嵌入另一个syntax的场景）
	- escape: 必须配套embed，用以跳出embed的context
	- embed_scope：该scope会分配给match后escape前的所有文本
	- escape_captures：一个capture组到scope名字的映射，对于escape模式。使用capture group 0来将scope应用到整个escape匹配。
- branch：接受多个context,会按序匹配，错误后自动进入下一个。`branch_point`是分支的唯一标识符，在匹配使用失败动作时被指定
- fail：接受一个branch_point的名字，以便回滚来匹配下一个context

> push, pop, set, embed, branch, fail同时只能出现一个


4. match exmaples

```yaml title="基本使用，对整个匹配内容应用该scope"
- match: \w+ # 匹配字母，数字，下划线的多个字符
	  scope: variable.parameter.move
```

```yaml title="将不同的scopes应用给正则捕获组"
- match: ^\\s*(#)\\s*\\b(include)\\b # ^表示了字符串开头，\\s*表示空格或多个空字符，(#)表示匹配#字符，\\b表示单词边界
  captures:
    1: meta.preprocessor.c++ # 匹配的#应用的scope
    2: keyword.control.include.c++ # 匹配的include应用的scope
```

```yaml title="将另一个context压入"
- match: \b\w+(?=\() # 以变量为开头，后面是
  scope: entity.name.function.c++
  push: function-parameters # 压入处理函数参数的context
```















```yaml title="旧版-手写的样式"
%YAML 1.2
---
# See http://www.sublimetext.com/docs/3/syntax.html
name: Move
file_extensions:
  - move
scope: source.move

contexts:
  # The prototype context is prepended to all contexts but those setting
  # meta_include_prototype: false.
  prototype:
    - include: comments

  main:
    # The main context is the initial starting point of our syntax.
    # Include other contexts from here (or specify them directly).
    - include: move_keyworks
    - include: move_definitions
    - include: move_features
    - include: move_types
    - include: move_others
    - include: move_inner_functions
    - include: keywords
    - include: numbers
    - include: strings
    - include: primitives

  # primitives:
  #   # Keywords are if, else for and while.
  #   # Note that blackslashes don't need to be escaped within single quoted
  #   # strings in YAML. When using single quoted strings, only single quotes
  #   # need to be escaped: this is done by using two single quotes next to each
  #   # other.
  #   - match: '\b(module|use|fun|public|friend|struct|const|let|has|key|store|drop)\b'
  #     scope: keyword.declaration.move

  move_keyworks:
    # Color: Red
    # Key: use & public friend module has
    - match: '\b(use|mut|&|public|friend|module|has|script|native)\b'
      scope: keyword.context.move

  move_definitions:
    # Color: Blue
    # Key: const let fun struct
    - match: '\b(const|let|fun|struct|acquires)\b'
      scope: keyword.declaration.move # variable.annotation.move ?

  move_features:
    # Color: Purple
    # Key: key, store, drop, entry
    # Key: move_to assert! !exists
    - match: '\b(key|store|drop|entry|copy)\b'
      scope: constant.character.escape.move

  move_types:
    # Color: Orange
    # Key: u8 u16 u32 u64 u128 u256
    - match: '\b(bool|&|u8|u16|u32|u64|u128|u256|address|&signer|vector|signer|true|false)\b'
      scope: constant.numeric.move

  move_others:
    # Color: Orange
    # Key: self &self Self
    - match: '\b(&self|self|Self)\b'
      scope: constant.character.escape.move

  move_inner_functions:
    # Color: Green
    # Key: move_to assert! exists ...
    - match: '\b(move_to|move_from|assert!|!exists|exists|borrow_global|borrow_global_mut)\b'
      scope: constant.numeric.move

  keywords:
    # Red
    # Key: if else for while
    - match: '\b(if|else|for|while)\b'
      scope: keyword.control.move

  numbers:
    - match: '\b(-)?[0-9.]+\b'
      scope: constant.numeric.move

  strings:
    # Strings begin and end with quotes, and use backslashes as an escape
    # character.
    - match: '"'
      scope: punctuation.definition.string.begin.move
      push: inside_string

  inside_string:
    - meta_include_prototype: false
    - meta_scope: string.quoted.double.move
    - match: '\.'
      scope: constant.character.escape.move
    - match: '"'
      scope: punctuation.definition.string.end.move
      pop: true

  comments:
    # Comments begin with a '//' and finish at the end of the line.
    - match: '//'
      scope: punctuation.definition.comment.move
      push:
        # This is an anonymous context push for brevity.
        - meta_scope: comment.line.double-slash.move
        - match: $\n?
          pop: true
```