



## 序

看完了第一课Search的视频以及自己整理了课堂笔记，对课程内容有了一个较好的理解，是时候尝试做一个课后作业巩固学到的内容了。

第一个作业：degrees

## 需求分析

提供了一些作分析的数据，包括演员名字，电影名字，演员出演过的电影等等。

目标是找到两个演员之间通过电影合作的**最短关系链**。

比如演员A和C没有合作过电影，但是A和B在Movie 1中有过合作，B和C又在Movie 2中有过合作，那么Actor A和C之间的degrees就是2：

```css
Actor A → Movie 1 → Actor B → Movie 2 → Actor C
```

> 我们可以将其定义为一个搜索问题：我们的状态是人。我们的动作是电影，它将我们从一个演员带到另一个演员（一部电影确实可以带我们到多个不同的演员，但对于这个问题来说，这没问题）。我们的初始状态和目标状态由我们试图连接的两个人定义。通过使用广度优先搜索，我们可以找到从一个演员到另一个演员的最短路径。是的，找到最短路径需要用到BFS搜索。



## 数据来源

需要分析的csv数据已经提供：

**people.csv存储演员信息**

```python
id,name,birth
102,"Kevin Bacon",1958
129,"Tom Cruise",1962
144,"Cary Elwes",1962
158,"Tom Hanks",1956
1597,"Mandy Patinkin",1952
163,"Dustin Hoffman",1937
1697,"Chris Sarandon",1942
193,"Demi Moore",1962
197,"Jack Nicholson",1937
200,"Bill Paxton",1955
398,"Sally Field",1946
420,"Valeria Golino",1965
596520,"Gerald R. Molen",1935
641,"Gary Sinise",1955
705,"Robin Wright",1966
914612,"Emma Watson",1990

```

**movies.csv存储电影信息**

```python
id,title,year
112384,"Apollo 13",1995
104257,"A Few Good Men",1992
109830,"Forrest Gump",1994
93779,"The Princess Bride",1987
95953,"Rain Man",1988
```

**starts.csv表示某人出演了那部电影**

```python
person_id,movie_id
102,104257
102,112384
129,104257
129,95953
144,93779
158,109830
158,112384
1597,93779
163,95953
1697,93779
193,104257
197,104257
200,112384
398,109830
420,95953
596520,95953
641,109830
641,112384
705,109830
705,93779
```



## 数据结构设计

需要把上面的csv数据读入python，并构造可以快速查找的数据结构。为此，我们创建了3个字典

```python
# Maps names to a set of corresponding person_ids
names = { name.lower(): set_of_person_ids }

# Maps person_ids to a dictionary of: name, birth, movies (a set of movie_ids)
people = { person_id: {"name": name, "birth": birth, "movies": set_of_movie_ids} }

# Maps movie_ids to a dictionary of: title, year, stars (a set of person_ids)
movies = { movie_id: {"title": title, "year": year, "stars": set_of_person_ids} }
```

- 通过姓名可以找到对应的ID，这个ID是一个set。可以有重名的人，但是每个人对应一个唯一的person_id
- 通过person_id可以找到此人出演过的所有电影
- 通过movie_id可以找到所有参演演员的person_id



## 问题抽象

这个问题可以看作图搜索：

- 节点：包含一个演员ID，以及他的父节点
- 边界：电影ID，也就是通过那部电影把节点扩展到了这里，不同的演员是通过不同的电影关联起来的
- 目标：需要找到从source演员到target演员的**最短路径**，所以需要：
  - 使用BFS算法确保找到的是最短路径
  - 节点需要保存父节点的信息以便回溯生成路径



## 输入输出

输入示例：

`Actor Name：A, Actor Name：C`

输出示例：

`Degrees of separation: 2
1: Actor A and Actor B starred in Movie 1
2: Actor B and Actor C starred in Movie 2`



## 代码实现

代码如下。没太多可说的，注释比较清晰，核心功能实现是shortest_path函数。

### util.py

```python
# 节点保存person_id，父节点信息，以及action(通过那个movie_id到达此节点)
class Node():
    def __init__(self, state, parent, action):
        self.state = state
        self.parent = parent
        self.action = action

class StackFrontier():
    def __init__(self):
        self.frontier = []

    def add(self, node):
        self.frontier.append(node)

    def contains_state(self, state):
        return any(node.state == state for node in self.frontier)

    def empty(self):
        return len(self.frontier) == 0

    def remove(self):
        if self.empty():
            raise Exception("empty frontier")
        else:
            node = self.frontier[-1]
            self.frontier = self.frontier[:-1]
            return node

# BFS算法使用队列，节点remove遵循先进先出原则
class QueueFrontier(StackFrontier):
    def remove(self):
        if self.empty():
            raise Exception("empty frontier")
        else:
            node = self.frontier[0]
            self.frontier = self.frontier[1:]
            return node
```



### degrees.py

```python
import csv
import sys

from util import Node, StackFrontier, QueueFrontier

# Maps names to a set of corresponding person_ids
names = {}

# Maps person_ids to a dictionary of: name, birth, movies (a set of movie_ids)
people = {}

# Maps movie_ids to a dictionary of: title, year, stars (a set of person_ids)
movies = {}

"""
读取提供的csv文件，提取信息放入我们自己创建的字典
原则是根据每个person_id，可以找到此人出演过的所有电影
根据每个movie_id，可以找到此电影所有出演的演员
names用来处理重名的情况，名字重，person_id唯一
"""
def load_data(directory):
    """
    Load data from CSV files into memory.
    """
    # Load people
    with open(f"{directory}/people.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            people[row["id"]] = {
                "name": row["name"],
                "birth": row["birth"],
                "movies": set()
            }
            if row["name"].lower() not in names:
                names[row["name"].lower()] = {row["id"]}
            else:
                names[row["name"].lower()].add(row["id"])

    # Load movies
    with open(f"{directory}/movies.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            movies[row["id"]] = {
                "title": row["title"],
                "year": row["year"],
                "stars": set()
            }

    # Load stars
    with open(f"{directory}/stars.csv", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            try:
                people[row["person_id"]]["movies"].add(row["movie_id"])
                movies[row["movie_id"]]["stars"].add(row["person_id"])
            except KeyError:
                pass

def main():
    if len(sys.argv) > 2:
        sys.exit("Usage: python degrees.py [directory]")
    directory = sys.argv[1] if len(sys.argv) == 2 else "large"

    # Load data from files into memory
    print("Loading data...")
    load_data(directory)
    print("Data loaded.")

    source = person_id_for_name(input("Name: "))
    if source is None:
        sys.exit("Person not found.")
    target = person_id_for_name(input("Name: "))
    if target is None:
        sys.exit("Person not found.")

    path = shortest_path(source, target)

    if path is None:
        print("Not connected.")
    else:
        degrees = len(path)
        print(f"{degrees} degrees of separation.")
        path = [(None, source)] + path
        for i in range(degrees):
            person1 = people[path[i][1]]["name"]
            person2 = people[path[i + 1][1]]["name"]
            movie = movies[path[i + 1][0]]["title"]
            print(f"{i + 1}: {person1} and {person2} starred in {movie}")

def create_path(node, movie_id=None, person_id=None):
    """
    Already found the connections, then build the path from source to target.
    Return path
    """
    path = []
    if movie_id is not None and person_id is not None:
        path.append((movie_id, person_id))

    while node.parent is not None:
        path.append((node.action, node.state))
        node = node.parent
    path.reverse()
    return path

def shortest_path(source, target):
    """
    Returns the shortest list of (movie_id, person_id) pairs
    that connect the source to the target.

    If no possible path, returns None.
    """

    start = Node(state=source, parent=None, action=None)
    frontier = QueueFrontier()
    frontier.add(start)

    explored = set()

    while True:
        if frontier.empty():
            return None
        node = frontier.remove()

        if node.state == target:
            return create_path(node)

        explored.add(node.state)

        neighbors = neighbors_for_person(node.state)
        for movie_id, person_id in neighbors:
            if person_id == target:
                return create_path(node, movie_id, person_id)
            if person_id not in explored and not frontier.contains_state(person_id):
                child = Node(state=person_id, parent=node, action=movie_id)
                frontier.add(child)
    return None

def person_id_for_name(name):
    """
    Returns the IMDB id for a person's name,
    resolving ambiguities as needed.
    """
    person_ids = list(names.get(name.lower(), set()))
    if len(person_ids) == 0:
        return None
    elif len(person_ids) > 1:
        print(f"Which '{name}'?")
        for person_id in person_ids:
            person = people[person_id]
            name = person["name"]
            birth = person["birth"]
            print(f"ID: {person_id}, Name: {name}, Birth: {birth}")
        try:
            person_id = input("Intended Person ID: ")
            if person_id in person_ids:
                return person_id
        except ValueError:
            pass
        return None
    else:
        return person_ids[0]

# 通过person_id找到所有邻居节点
def neighbors_for_person(person_id):
    """
    Returns (movie_id, person_id) pairs for people
    who starred with a given person.
    """
    movie_ids = people[person_id]["movies"]
    neighbors = set()
    for movie_id in movie_ids:
        for person_id in movies[movie_id]["stars"]:
            neighbors.add((movie_id, person_id))
    return neighbors

if __name__ == "__main__":
    main()
```



## 课后思考

此项目难度不大，和视频课程讲解的Maze思路如出一辙，框架已经搭建好，只需要完成核心的shortest_path功能。理解了Maze代码，会比较容易完成degrees。代码语法不重要，重要的是搜索算法的思路。

接下来我将挑战下一个搜索算法，对抗性搜索。