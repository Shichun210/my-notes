# 一级标题

## 二级标题



wefje

wekfjew

---

***

**粗体**

~~删除线~~

<u>下划线</u>

*斜体*

==高亮==

x^2^

H~2~O

- [ ] lunch

- [ ] wfjwjfjj

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

