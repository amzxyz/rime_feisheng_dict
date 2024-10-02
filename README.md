### 落霞词库

**配套方案** 万象系列方案基础版 [辅助码增强版本](https://github.com/amzxyz/rime_wanxiang_pinyin)  [同文定制版本](https://github.com/amzxyz/tongwen_wanxiang)

 

落霞词库基于[白霜词库](https://github.com/gaboolic/rime-frost)frost的基础上修改而来，继承了白霜优秀的词频，我为所有的字词添加了音调，为大字集添加更多字支持，那么为词库加上音调会发生什么样子的化学反应呢？

 

1. 可移植性：

在原来的方案中拼写运算的首位添加一句话，就可以像以前一样使用了，无任何不同，所以可以轻松迁移

```
 algebra:

  - xlit/āáǎàōóǒòēéěèīíǐìūúǔùǖǘǚǜü/aaaaooooeeeeiiiiuuuuvvvvv/
```

2. 简化配置和加载项：

从前我们对音调的利用需要配置专门的外挂词库，形式包括：

- 外挂方案，然后挂接到主方案中，进行反查；

- 通过opencc转换器覆盖，两种坏处，一个是需要加载独立文件，另一个是覆盖性的注释不利于多态共存，比如平时显示辅助码的时候，出现单一的错词提示的时候做不到都显示和逻辑替换；

- lua脚本的需要复杂的逻辑，加载文件，解析格式深加工格式，遍历候选，提交替换结果。

 

以前做好这几件事需要多个字表、多个lua协同，配置文件里要引入多个加载器。

而现在注释本来的面目就是带着音调的，你只需要完全暴露注释，他就是显示拼音音调的形态，你可以使用lua简单的加个括号等等简易的格式操作，可以借助lua接口将其替换到输入码preedit、注释comment，在反查时由于rime本身的注释生成特性，多音字都会被一一罗列到注释里，你无需额外维护多音字字表。在用户态里的感知就是无需复杂配置，无需加载过多的文件，节约内存开销，提升性能，减少方案文件维护成本。

3. 如何使用：

- 第一个是如果迁移到你的项目中，第1项中已有解答，或者使用单一变化 - xform/ā/a/

- 词库附带方案的使用，由于rime不可避免的人人都要修改配置，因此我将配置简化，避免多方案部署，带着疑问去使用本方案，有助于了解rime的结构。

 首先

打开万象项目主方案 ```wanxiang.schema.yaml```，编辑表头，选择方案

```
#############万象拼音无辅助码版本###########################
schema_name: 
  name: 万象拼音  #可以改成与你所选方案一致的描述，不改也行
set_shuru_schema:     #配置此项就是选择什么输入法,同时拆分反查和中英文混输也将匹配该输入方案
  __include: algebra_zrm  #可选解码规则有  algebra_pinyin, algebra_zrm, algebra_flypy,  algebra_ziguang, algebra_sogou, algebra_mspy, algebra_abc  选择一个填入
pro_comment_format:      # 超级注释模块
  candidate_length: 1     # 候选词注释提醒的生效长度，0为关闭  但同时清空其它，应当使用开关或者快捷键来处理   
  corrector_type: "{comment}"  #错音错词提示显示类型，比如"({comment})" 
########################以下是方案配置######################################################
```

可以说非常清晰了，定义方案名称、拼音类型、以及注释显示逻辑，配置完毕保存。

继续分别打开```radical_pinyin.schema.yaml```  ```melt_eng.schema``` 分别对反查和英文解码方案进行表头配置

```
###############选择与之匹配的拼音方案#####################

set_shuru_schema:

  __include: algebra_zrm   #可选的选项有（algebra_pinyin, algebra_zrm, algebra_flypy, algebra_mspy, algebra_sogou, algebra_abc, algebra_ziguang）
```

同样选择对应的拼音类型即可，保存后即可部署使用了

4.功能介绍：

**日期时间：** 

**输入：**`date     time  week   datetime          timestamp`

**得到：**` 2024-07-04  19:37  星期四  2024-07-04T19:38:47+08:00  1720093174`

**农历：**  lunar 获得当前日期的农历值

**Unicode：**  大写 U 开头，如 U62fc 得到「拼」。

**数字、金额大写：**  大写 R 开头，如 R1234 得到「一千二百三十四、壹仟贰佰叁拾肆元整」。

**农历指定日期：**  大写 N 开头，如 N20240210 得到「二〇二四年正月初一」。

 **/模式：**  通过输入 /sx 快捷输入关于“数学”的特殊符号，具体能输入什么可以打开 symbols.yaml学习。

 **计算器：**  通过输入大写V引导继续输入如：V3+5  候选框就会有8和3+5=8，基础功能 `+ - * / % ^` 还支持 `sin(x) cos(x)` 等众多运算方式 [点击全面学习](https://github.com/gaboolic/rime-shuangpin-fuzhuma/blob/main/md/calc.md)

 **自动上屏：**  例如：三位、四位简码唯一时，自动上屏如`jjkw岌岌可危` `zmhu怎么回事` 。默认未开启，方案文件中`speller:`字段下取消注释这两句开启 `#   auto_select: true  #  auto_select_pattern: ^[a-z]+/|^[a-df-zA-DF-Z]\w{3}|^e\w{4}`

 **错音错字提示：**  例如：输入`gei yu给予`，获得`jǐ yǔ`提示
 
 **快符：** 例如 ```'q``` 通过单引号键引导的26字母快速符号自动上屏，双击''重复上一个符号

 **快捷键相关：** 任意长度候选词的注释提示能力，默认开启1个字的长度

 输入状态下：Ctrl+a开启和关闭注释候选词带音调，输入状态下：Ctrl+s开启关闭输入码带音调。

 **Tab循环切换音节：**  当输入多个字词时想要给前面补充辅助码，可以多次按下tab循环切换，这种可能比那些复杂的快捷键好用一些。

  **翻译模式：**  输入状态按下Ctrl+E快捷键进入翻译模式，原理是opencc查表进行中英文互译，能否翻译取决于词表的丰富度；

  \* 更多功能可以编辑方案文件依据注释说明开启

 
