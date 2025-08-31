[TOC]



# 一级标题

> 而非理解为

## 二级标题

wefje

wekfjew

---

### fejlwjef

**粗体**

~~删除线~~

<u>下划线</u>

*斜体*

==高亮==

> [!attention]

x^2^

H~2~O

> fef

### 三级标题

> wefwef

<blockquote alt = 'purple'>wefwelfjwekfjweflkjs
wlekfjewfkw
welfjwlekfjwe</blockquote>



### 又一个三级标题



####  四级标题



##### 五级标题

> 访问附件二

- [ ] lunch

- [ ] wfjwjfjj

## 又一个二级标题



>
>
>wefwew
>
>wefwe
>
>>
>>
>>wefwefe
>>
>>>
>>>
>>>wfejwef

```python
wefwejflflwef `int a=0;`wefef
```


www.baidu.com

[baidu](https://www.baidu.com)

[baidu](www.baidu.com)

> [!note]
>
> fefefewkf



> [!tip]
>
> lfwekfldjwfel

> [!warning]



> [!caution]



> [!important]

<span style="background:#0078d7; color:white; padding:3px 8px; border-radius:12px;">Python</span>
<span style="background:#28a745; color:white; padding:3px 8px; border-radius:12px;">AI</span>
<span style="background:#ff9800; color:white; padding:3px 8px; border-radius:12px;">Note</span>

```
<section alt="note">note text</section><br>
```

| efw  | fwf  | ffw  |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |

```mermaid
graph LR
A[f]==>B(圆角)
B==>C{条件A}
C-->|a=1|D[result 1]
C-->|a=2|E[result 2]
F[横向流程图]
```

```sequence
Alice->B: hello, how are you
Note right of B: B thanks
B->Alice: I'm good.
```

```flow

```

```js

```

$$
wwlkefwf
$$

$ gongshi$



H~2~O

x^2^

${a\over b}$

$\sqrt[2]{3}$

$\Alpha$

$\beta$

$\Downarrow$

$\color{lime}{color}$

<kbd>内容</kbd>



```flow
st=>start: Start
op=>operation: Your Operation
cond=>condition: Yes or No?
e=>end

st->op->cond
cond(yes)->e
cond(no)->op
```



```mermaid
%% Example of sequence diagram
  sequenceDiagram
    Alice->>Bob: Hello Bob, how are you?
    alt is sick
    Bob->>Alice: Not so good :(
    else is well
    Bob->>Alice: Feeling fresh like a daisy
    end
    opt Extra response
    Bob->>Alice: Thanks for asking
    end
```

```mermaid
graph LR
A[Hard edge] -->B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
```

```mermaid
classDiagram
      Animal <|-- Duck
      Animal <|-- Fish
      Animal <|-- Zebra
      Animal : +int age
      Animal : +String gender
      Animal: +isMammal()
      Animal: +mate()
      class Duck{
          +String beakColor
          +swim()
          +quack()
      }
      class Fish{
          -int sizeInFeet
          -canEat()
      }
      class Zebra{
          +bool is_wild
          +run()
      }
```

```mermaid
pie
    title Pie Chart
    "Dogs" : 386
    "Cats" : 85
    "Rats" : 150 
```

```mermaid
gitGraph
       commit
       commit
       branch develop
       checkout develop
       commit
       commit
       checkout main
       merge develop
       commit
       commit
```

```mermaid
mindmap
  root((mindmap))
    Origins
      Long history
      ::icon(fa fa-book)
      Popularisation
        British popular psychology author Tony Buzan
    Research
      On effectiveness<br/>and features
      On Automatic creation
        Uses
            Creative techniques
            Strategic planning
            Argument mapping
    Tools
      Pen and paper
      Mermaid
```

zenuml    title Demo    Alice->John: Hello John, how are you?    John->Alice: Great!    Alice->John: See you later!

```zenuml
    title Demo
    Alice->John: Hello John, how are you?
    John->Alice: Great!
    Alice->John: See you later!
```



```mermaidzenuml
    title Demo
    Alice->John: Hello John, how are you?
    John->Alice: Great!
    Alice->John: See you later!
```

```mermaid
zenuml
    title Annotators
    @Actor Alice
    @Database Bob
    Alice->Bob: Hi Bob
    Bob->Alice: Hi Alice

```


***


```mermaid
graph TD
    A[矩形] --> B(圆角矩形)
    B --> C((圆形))
    C --> D{菱形}
    D --> E{{六边形}}
    E --> F[/平行四边形/]
    F --> G[\反向平行四边形\]
    G --> H>梯形]
```



```mermaid

graph TD
    A([开始]) --> B[初始化队列: 加入起点]
    B --> C[初始化已访问集合]
    C --> D{队列是否为空?}
    D -->|是| E([搜索失败: 无解])
    D -->|否| F[取出队首节点]
    F --> G{该节点是否为目标?}
    G -->|是| H([返回路径])
    G -->|否| I[获取节点的所有邻居]
    I --> J[过滤已访问的邻居]
    J --> K[将新邻居加入队列并标记为已访问]
    K --> D

```

```mermaid
gantt
    title 项目开发计划
    dateFormat  YYYY-MM-DD
    section 需求
    分析需求          :done,    des1, 2025-08-01, 3d
    section 开发
    编码              :active,  des2, 2025-08-04, 5d
    测试              :         des3, after des2, 3d
```

```mermaid
stateDiagram-v2
    [*] --> 待机
    待机 --> 工作 : 开机
    工作 --> 待机 : 关机
```

```mermaid
pie title 饮料偏好
    "咖啡" : 40
    "茶" : 30
    "水" : 30
```

```mermaid
erDiagram
    "用户" ||--o{ "订单" : "拥有"
    "订单" ||--|{ "订单项" : "包含"
    "产品" ||--o{ "订单项" : "被包含"
```





```mermaid
flowchart TD
  A[Input: cell and count] --> B[Mark cell as moved and safe]
  B --> C[Construct new sentence for neighbors]
  C --> D["Add sentence to knowledge base (KB)"]
  D --> E{"Any inference possible?"}
  E -->|Yes| F[Infer new safe/mine cells and update KB]
  F --> D
  E -->|No| G[KB converged, exit loop]
```

```mermaid
%%{init: {"theme": "default"}}%%
radar-beta
  title Skills Comparison
  axis py["Python"], math["Math"], ai["AI"], comm["Communication"], team["Teamwork"]
  curve alice["Alice"]{80, 70, 90, 60, 75}
  curve bob["Bob"]{65, 85, 80, 70, 90}
  showLegend true
  max 100
  min 0
  graticule polygon
  ticks 5
```

```mermaid
%%{init: {"theme": "default"}}%%
treemap-beta
  "AI Project"
    "Data Preparation": 30
    "Model Training": 40
    "Evaluation": 20
    "Deployment": 10
```



