## A.intro
The regular expression language is relatively small and restricted, so not all possible string processing tasks can be done using regular expressions

### * metacharacters(元字符）
> 元字符不会匹配自己

`. ^ $ * + ? { } [ ] \ | ( )`

匹配元字符如(：`[(]` 或 `\(`

`[+-(]`这种`-`前后都有字符时，`-`表示范围，需使用`\-`z才不出错。
只有`-`前或后为`\`,`/`时不表示范围。
### * group
分组顺序,默认为0：`r'^non-group(group1(2))(3(4(5)))(6)'`

对组调用：组名或组顺序

* result.group('quote')
* result.group(2)
### * Repeating Things

1. greedy match `*`: the matching engine will try to repeat it as many times as possible. If later portions of the pattern don’t match, the matching engine will then back up（指回退） and try again with fewer repetitions.
 
2. non-greedy match `?`: add `?` after metacharacter for repeating,the matching engine will try to repeat it as few times as possible.

examples:

* from `b?` to `b??`
* from `b*` to `b*?`
* from `b{2,3}` to `b{2,3}?` matches b twice at start
* from `b+` to `b+?` matches b once at start
#### match process
regular expression: `a[bcd]*b`

string to be matched: `abcbd`

1. Re`a` matches str `a`
2. Re`[bcd]*` matches as many as possible,in this case str `bcbd`
3. Re`b` tries to match but  the current position is at the end of the string, so it fails.
4. Re`[bcd]*` backes up once and matches str `bcb`
5. Re`b` doesn't match str `d` and it fails.
6. Re`[bcd]*` backes up once till matches or fails.

### * raw string
> As stated earlier, regular expressions use the backslash character ('\') to indicate special forms or to allow special characters to be used without invoking their special meaning. This conflicts with Python’s usage of the same character for the same purpose in string literals.

Regular String: `"\\\\section\\w+"`

Raw string: `r"\\section\w+"`

## B.Performing Matches

### 1. match functions

* match()

	Determine if the RE matches at the beginning of the string.

* search()

	Scan through a string, looking for any location where this RE matches.

* findall()

	Find all substrings where the RE matches, and returns them as a list.

* finditer()

	Find all substrings where the RE matches, and returns them as an iterator.

* group()

	Return the string matched by the RE

* start()

	Return the starting position of the match

* end()

	Return the ending position of the match

* span()：位置为匹配到的第一个字符之前，最后一个字符之后

	Return a tuple containing the (start, end) positions of the match

### 2. Compilation Flags

调用：re.compile('regex', re.I | re.M)

ASCII, A

	Makes several escapes like \w, \b, \s and \d match only on ASCII characters with the respective property.

DOTALL, S

	Make . match any character, including newlines.

IGNORECASE, I

	Do case-insensitive matches.

LOCALE, L

	Do a locale-aware match.

MULTILINE, M

	Multi-line matching, affecting ^ and $.

VERBOSE, X (for ‘extended’):regex中可换行加空格及注释，帮助阅读

	Enable verbose REs, which can be organized more cleanly and understandably.


### 3. Non-capturing and Named Groups

> 源于Perl5。当时由于选取单个字符作为元字符来扩展Re的功能，会与旧版本产生兼容性问题，例如选`&`,旧版本便不能正确识别。

> `(?`，由于?前没有东西会出错，使用它来扩展功能就不会出现兼容性问题

* `(?:...)`: 忽略自身括号，不计入group中

*example*

	>>> m = re.match("([abc]+(x))", "abcx")
	>>> m.group(1)
	abcx
	>>> m.group(2)
	x
	
	>>> m = re.match("(?:[abc]+(x))", "abcx")
	>>> m.group(1)
	x

* `(?P<name>...)`: 为group命名，计入group

*example*

	>>> p = re.compile(r'(?P<word>\b\w+\b)')
	>>> m = p.search( '(((( Lots of punctuation )))' )
	>>> m.group('word')
	'Lots'
	>>> m.group(1)
	'Lots'
	
	>>> m.groupdict() # 用来获得已命名组的字典
	{'word': 'Lots'}

quote in the same pattern：
* `(?P=name)`: 用于(?P<name>...)之后，且其匹配会与(?P<name>...)完全相同,不计入group。
* `\integer`:\1-\99之间，\integer调用group(integer)

*example*

	>>> m = re.match(r'(?P<first>(p|w))23(?P=first) (\d)+', 'p23p 22')
	# 作用同上>>> m = re.match(r'(?P<first>(p|w))23\1 (\d)+', 'p23p 22')
	>>> m.groups()
	('p', 'p', '2')
	
	# 命名组匹配为p,（即使p|w),调用组也为p而非w
	>>> m = re.match(r'(?P<first>(p|w))23(?P=first) (\d)+', 'p23w 22')
	>>> m.groups()
	AttributeError: 'NoneType' object has no attribute 'groups'C

### Lookahead Assertions（匹配特定位置）
> 都不计入group，且不在返回值中显示,不损耗字符串内容

用途：匹配特定格式的文件名，或匹配非特定格式的文件名

`...(?=...)`：前置匹配

	Positive lookahead assertion. This succeeds if the contained regular expression, 
	represented here by ..., successfully matches at the current location, and fails
	otherwise. But, once the contained expression has been tried, the matching engine
	doesn’t advance at all; the rest of the pattern is tried right where the assertion 
	started.

*example*
	
	# 与(?:...)做对比
	>>> m = re.match(r'(\w+)(?:95|98|NT|3000)(.)','Windows300089')
	>>> m.group()
	('Windows30008')
	
	# 匹配特定后缀的windows，且(?=...)不损耗字符串内容。因此对300089而非89匹配
	>>> m = re.match(r'(\w+)(?=95|98|NT|3000)(.)','Windows300089')
	>>> m.group()
	('Windows3')
	

	>>> m = re.match(r'(\w+)(?=95|98|NT|3000)(.)','Windows300089')
	>>> print(m.group(2))
	3

`(?!...)`：常用，能匹配非的长字符串，不像[^c]只能匹配非的单个字符。

	Negative lookahead assertion. This is the opposite of the positive assertion; 
	it succeeds if the contained expression doesn’t match at the current position 
	in the string.

*example*

	>>> m = re.match(r'.*[.](?!bat$|exe$)[^.]*$','notEndsWithBat.orExe')
	# any filename ends with bat or exe returns None.

`(?<=...)`: 后置匹配,匹配需固定长度

Matches if the current position in the string is preceded by a match for ... that ends at the current position. This is called a positive lookbehind assertion. (?<=abc)def will find a match in 'abcdef', since the lookbehind will back up 3 characters and check if the contained pattern matches. The contained pattern must only match strings of some fixed length, meaning that abc or a|b are allowed, but a* and a{3,4} are not. Note that patterns which start with positive lookbehind assertions will not match at the beginning of the string being searched; you will most likely want to use the search() function rather than the match() function:

*example*

	>>> import re
	>>> m = re.search('(?<=abc)def', 'abcdef')
	>>> m.group(0)
	'def'

	This example looks for a word following a hyphen:
	
	>>> m = re.search(r'(?<=-)\w+', 'se-spam-egg')
	>>> m.group(0)
	'spam'
	Changed in version 3.5: Added support for group references of fixed length.

`(?<!...)`： 匹配需固定长度

Matches if the current position in the string is not preceded by a match for .... This is called a negative lookbehind assertion. Similar to positive lookbehind assertions, the contained pattern must only match strings of some fixed length. Patterns which start with negative lookbehind assertions may match at the beginning of the string being searched.


### 4. Modifying Strings

split()

	Split the string into a list, splitting it wherever the RE matches

*example*

	>>> p = re.compile(r'\W+')
	# >>> p = re.compile(r'(\W+)')添加括号则可同时返回所有分隔符
	>>> p.split('This is a test, short and sweet, of split().')
	['This', 'is', 'a', 'test', 'short', 'and', 'sweet', 'of', 'split', '']
	>>> p.split('This is a test, short and sweet, of split().', 3)
	['This', 'is', 'a', 'test, short and sweet, of split().']

sub()

	Find all substrings where the RE matches, and replace them with a 
	different string

*example*
	
	# quote in re.sub()
	# \g<1>,\g<name>仅在re.sub中使用，但不能使用(?P=name)
	>>> p = re.compile('section{ (?P<name> [^}]* ) }', re.VERBOSE)
	>>> p.sub(r'subsection{\1}','section{First}')
	'subsection{First}'
	>>> p.sub(r'subsection{\g<1>}','section{First}')
	'subsection{First}'
	>>> p.sub(r'subsection{\g<name>}','section{First}')
	'subsection{First}'

subn()

	Does the same thing as sub(), but returns the new string and the 
	number of replacements

re.escape(pattern)：获得转义后的字符串
	
	>>> print(re.escape('http://www.python.org'))
	http://www\.python\.org


*Empty matches* are replaced only when they’re not adjacent to a previous empty match.
	
	>>> p = re.compile('x*')
	>>> p.sub('-', 'abxd')
	'-a-b--d-'


### 5. refrence link

[refrence](https://docs.python.org/3/howto/regex.html#compilation-flags)

### 6. groups判断

分组优先级为：1.从外到内,2.从左到右

matched.groups('default value')
	
	空组可设置'default value'，不设置则默认返回None。

matched.lastindex: 最后的一级括号(最外层的括号）是否为命名组，否则返回None。

	r'(1)(2)(3(4)(named))non-group-content'，返回None
	r'(1)(2)(3(4)(5))(named)non-group-content'，返回named

matched.lastindex: (1)(2)(3(4)(5))，返回3而非5

## C.测试

findall是进行多次match（每找到一个便中止去进行下一次匹配）并且消耗字符（未找到匹配结果也会消耗字符，被消耗的字符不参与后续匹配）。并会尝试对最后的位置（无字符）进行一次匹配。
若第一次匹配结果为空，如(.\*?)默认尝试0次匹配，则当前字符并未被消耗，(.\*?)会尝试1次匹配，1次未匹配到则多次也不可能匹配到，消耗当前字符。多个(.\*?)时优先尝试最后一个(.\*?)的1次匹配

* `.*` 默认优先匹配到最长可匹配字符

* `.?` 默认匹配1次`.`，未匹配再匹配0次

* (.\*?)不同于((.?)\*)，不同于(.\*)?

* (.\*)?会先尝试1次匹配，再尝试0次匹配，内部依然贪婪匹配

* (.\*?),?对\*进行修饰，会先尝试0次(最低次数）匹配，再尝试1次（更高次数）匹配。例如.{3,4}*先尝试3次，再尝试4次，此时不会再去匹配0次

* ((.?)\*)会每次尝试匹配一个字符，若有多个符合`.`的字符会贪心匹配。尝试次数始终从\*的最大次数开始，直到0次，(效果上((.?){3，5})等同于(.{0，5}))。其中(.?)组会由于跟随了\*，只会在达到\*的最大匹配长度，如((.?){3，5})中的5时组内容才为`.`，否则为空。

#### 总结
不定长度默认匹配最长字符串，使用？（不用`(`隔开）从最少次数进行匹配

## D.Question
1. 无法通过分组匹配前后的符号不同但要同时有或同时没有
string-conds = `[Russia]`,`(China)`,`{England}`,`America`
p = re.compile(r'

匹配中文字符的正则表达式： [u4e00-u9fa5]

