# sublime_move_highlight
A syntax highlight extension of Move language in sublime for linux

## How to use

1. Place the file `Move.sublime-syntax` in the directory: `/home/<USERNAME>/.config/sublime-text-3/Packages/User/`

2. Restart the sublime


## How to develop

1. Install `PackageDev`

2. Tools - Packages - Package Development - New Syntax Definition

3. Edit the auto generated `.sublime-syntax`

```yaml title=".sublime-syntax"
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
    - match: '\b(use|mut|&|public|friend|module|has|script)\b'
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

> Location: `/home/stevekeol/.config/sublime-text-3/Packages/User/Move.sublime-syntax`

## Memo
```yaml title="配色设计草案"
# 蓝色
const let fun struct

# 绿色
struct_name 
fun_name

# 红色 -ok
use &  if public friend module has

# 黄色
字符串

# 紫色
数字

# 橘色
函数入参, 

# 特性/内置函数 紫色
key, store, drop, entry
内置函数：move_to assert! !exists

# 类型 橘色
<xxx T>
vector address &signer
: 后面的类型
u8 u16 u32 u64 u128 u256

# 灰色
//
```
> Question: 颜色如何选取？特性如何提取(如`<T>`)等

## References
[官档](http://www.sublimetext.com/docs/syntax.html)
[临时参考](https://www.jianshu.com/p/6c21df66be72)