BSTCS61B Fall 2020

算是二刷吧。神课总是值得二刷的。

于2023年3月份复习数据结构的时候，对笔记进行了一些更新，每次复习都会有

# Lecture 1 Intro, Hello World Java

关于课程安排

![image-20211113110720868](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111131107020.png)

![image-20211113110344006](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111131103227.png)

![image-20211113110513998](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111131105163.png)

![image-20211113111554851](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111131115018.png)

![image-20211113113127127](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111131131283.png)

# Project 0

The skeleton exhibits two *design patterns* in common use: the Model-View-Controller Pattern (MVC), and the Observer Pattern.

The MVC pattern divides our problem into three parts:

- The **model** represents the subject matter being represented and acted upon – in this case incorporating the state of a board game and the rules by which it may be modified. Our model resides in the `Model`, `Side`, `Board`, and `Tile` classes. The instance variables of `Model` fully determine what the state of the game is. Note: You’ll only be modifying the `Model` class.
- A **view** of the model, which displays the game state to the user. Our view resides in the `GUI` and `BoardWidget` classes.
- A **controller** for the game, which translates user actions into operations on the model. Our controller resides mainly in the `Game` class, although it also uses the GUI class to read keystrokes.

The MVC pattern is not a topic of 61B, nor will you be expected to know or understand this design pattern on exams or future projects.

The second pattern utilized is the “Observer pattern”. Basically this means that the **model** doesn’t actually report changes to the **view**. Instead, the **view** *registers* itself as an *observer* of the `Model` object. This is a somewhat advanced topic so we will provide no additional information here.

# Lab2

- 关于`Junit`的测试

```java
import static org.junit.Assert.*;
import org.junit.Test;
@Test
public void testMethod() {
assertEquals(<expected>, <actual>);
}
```

```java
public class SquarePrimesTest {
import static org.junit.Assert.*;
import org.junit.Test;

/**
* Here is a test for isPrime method. Try running it.
* It passes, but the starter code implementation of isPrime
* is broken. Write your own JUnit Test to try to uncover the bug!
*/
@Test
public void testSquarePrimesSimple() {
IntList lst = IntList.of(14, 15, 16, 17, 18);
boolean changed = IntListExercises.squarePrimes(lst);
assertEquals("14 -> 15 -> 16 -> 289 -> 18", lst.toString());
assertTrue(changed);
}
}
```

# Lecture 5 SLLists, Nested Classes, Sentinel Nodes

- 2D数组采用的也是行优先，每一个数组单元存储的是某一行数组的指针。

# Lecture 7  ALists, Resizing, vs. SLists

- ArrayList，删除的时候不要`lazy deletion`，因为使用这种策略会导致要删除的内容仍然被保留
- 如下图，要删除item[2]，如果只是改变size，那么那副红色图片仍然存在。

![屏幕截图 2021-11-14 215524](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111142156560.png)

# Lecture 9  Extends, Casting, Higher Order Functions

- HOFDemo:

![image-20211120222142113](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111202221356.png)

# Lab 4：Debugging and Using Git

- Unstage a file that you haven’t yet committed:

```
$ git reset HEAD [file]
```

This will take the file’s status back to modified, leaving changes intact. Don’t worry about this command undoing any work. This command is the equivalent of deleting one of the temporary images that you’re going to combine into a panorama.

Why might we need to use this command? Let’s say you accidentally started tracking a file that you didn’t want to track. (an embarrassing video of yourself, for instance.) Or you were made some changes to a file that you thought you would commit but no longer want to commit quite yet.

Please note that this new amended commit will replace the previous commit.

- Revert a file to its state at the time of the most recent commit:

```
$ git checkout -- [file]
```

This next command is useful if you would like to actually undo your work. Let’s say that you have modified a certain `file` since committing previously, but you would like your file back to how it was before your modifications.

**Note**: This command is potentially quite dangerous because any changes you made to the file since your last commit will be removed. Use this with caution!

- Amend latest commit (changing commit message or add forgotten files):

```
$ git add [forgotten-file]
$ git commit --amend
```

Please note that this new amended commit will replace the previous commit.



- `git clone [remote-repo-URL]`: Makes a copy of the specified repository, but on your local computer. Also creates a working directory that has files arranged exactly like the most recent snapshot in the download repository. Also records the URL of the remote repository for subsequent network data transfers, and gives it the special remote-repo-name “origin”.
- `git remote add [remote-repo-name] [remote-repo-URL]`: Records a new location for network data transfers.
- `git remote -v`: Lists all locations for network data transfers.
- `git pull [remote-repo-name] master`: Get the most recent copy of the files as seen in remote-repo-name
- `git push [remote-repo-name] master`: Pushes the most recent copy of your files to the remote-repo-name.

## Git Branching

![graph1](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111241550013.svg)

Every command that we’ve covered so far was working with the default branch. This branch is conventionally called the `master` branch. However, there are cases when you may want to create branches in your code.

Branches allow you to keep track of multiple different versions of your work simultaneously. One way to think of branches are as alternate dimensions. Perhaps one branch is the result of choosing to use a linked list while another branch is the result of choosing to use an array.

![graph2](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111241552237.svg)

### Creating, Deleting, & Switching Branches

A special branch pointer called the `HEAD` references the branch you currently have as your working directory. Branching instructions modify branches and change what your `HEAD` points to so that you see a different version of your files.

- The following command will create a branch off of your current branch.

```
$ git branch [new-branch-name]
```

- This command lets you switch from one branch to another by changing which branch your `HEAD` pointer references.

```
$ git checkout [destination-branch]
```

By default, your initial branch is called `master`. It is advised that you stick with this convention. Every other branch, however, can be named whatever you’d like. It’s generally a good idea to call your branch something descriptive like `fixing-ai-heuristics` so that you can remember what commits it contains.

- You can combine the previous two commands on creating a new branch and then checking it out with this single command:

```
$ git checkout -b [new-branch-name]
```

- You can delete branches with the following command:

```
$ git branch -d [branch-to-delete]
```

- You can easily figure out which branch you are on with this command:

```
$ git branch -v
```

More particular, the `-v` flag will list the last commit on each branch as well.

### Merging

There are often times when you’d like to merge one branch into another. For example, let’s say that you like the work you’ve done on `fixing-ai-heuristics`. Your AI is now super-boss, and you’d like your `master` branch to see the commits you’ve made on `fixing-ai-heuristics` and delete the `fixing-ai-heuristics` branch.

![graph3](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111241614066.svg)

In this case, you should checkout the `master` branch and merge `fixing-ai-heuristics` into `master`.

```
$ git checkout master
$ git merge fixing-ai-heuristics
```

This `merge` command will create a new commit that joins the two branches together and change each branch’s pointer to reference this new commit. While most commits have only one parent commit, this new merge commit has two parent commits. The commit on the `master` branch is called its *first parent* and the commit on the `fixing-ai-heuristics` branch is called its *second parent*.

### Merge Conflicts

It may happen that two branches you are trying to merge have conflicting information. This can occur if commits on the two branches changed the same files. Git is sophisticated enough to resolve many changes, even when they occur in the same file (though distinct places).

However, there are times that conflicts cannot be resolved by Git because changes impact the same methods/lines of code. In these cases, it will present both changes from the different branches to you as a *merge conflict*.

### Resolving Merge Conflicts

Git will tell you which files have conflicts. You need to open the files that have conflicts and resolve them manually. After doing this, you must commit to complete the merge of the two branches.

The files with conflicts will contain segments that look something like this:

```java
<<<<<<< HEAD
for (int i = 0; i < results.length; i++) {
println(results[i]);
println("FIX ME!");
}
=======
int[] final = int[results.length];
for (int i = 0; i < results.length - 1; i++) {
final[i] = results[i] + 1;
println(final[i]);
}
>>>>>>> fixing-ai-heuristics
```

Basically, you’ll see two segments with similar pieces of code:

1. The top code snippet is from the branch you originally had checked out when you ran the `merge` command. It’s called `HEAD` because the `HEAD` pointer was referencing this branch at the time of the `merge`. Continuing our example above, this code would be from the `master` branch.
2. The bottom code snippet is from the branch you were merging into your checked out branch. This is why it shows that the code is from `fixing-ai-heuristics`.

Basically, you’ll need to go through all marked sections and pick which snippet of code you’d like to keep.

In the previous example, I like the bottom piece of code better because I just fixed the AI while the top piece still prints “FIX ME!” Thus, I will delete the top segment as well as the extraneous lines to get this:

```java
int[] final = int[results.length];
for (int i = 0; i < results.length - 1; i++) {
final[i] = results[i] + 1;
println(final[i]);
}
```

Doing this for all segments demarcated by conflict-resolution markers resolves your conflict. After doing this for all conflicting files, you can commit. This will complete your merge.

## Remote Repositories (Advanced)

#### Adding Remotes

Adding a remote repository means that you are telling git where the repo is located. You do not necessarily have read/write access to every repo you can add. Actually accessing and modifying files in a remote is discussed later and relies on having added the remote.

```
$ git remote add [short-name] [remote-url]
```

The remote URL will look something like `https://github.com/berkeley-cs61b/skeleton.git` if you are using HTTP or `git@github.com:berkeley-cs61b/skeleton.git` if you are using SSH.

By convention, the name of the primary remote is called `origin` (for original remote repository). So either of the following two commands would allow me to add the `berkeley-cs61b/skeleton` repository as a remote.

```
$ git remote add origin https://github.com/berkeley-cs61b/skeleton.git
$ git remote add origin git@github.com:berkeley-cs61b/skeleton.git
```

After adding a remote, all other commands use its associated short name.

#### Renaming, Deleting, & Listing Remotes

- You can rename your remote by using this command:

```
$ git remote rename [old-name] [new-name]
```

- You can also remove a remote if you are no longer using it:

```
$ git remote rm [remote-name]
```

- To see what remotes you have, you can list them. The `-v` flag tells you the URL of each remote (not just its name).

```
$ git remote -v
```

You can read more about [working with remotes](http://git-scm.com/book/en/Git-Basics-Working-with-Remotes) in the Pro Git book.

#### Cloning a Remote

There are often remote repos with code that you would like to copy to your own computer. In these cases, you can easily download the entire repo with its commit history by cloning the remote:

```
$ git clone [remote-url]
$ git clone [remote-url] [directory-name]
```

The top command will create a directory of the same name as the remote repo. The second command allows you to specify a different name for the copied repository.

When you clone a remote, the cloned remote become associated with your local repo by the name `origin`. This is by default because the cloned remote was the `origin` for your local repo.

#### Pushing Commits

You may wish to update the contents of a remote repo by adding some commits that you made locally. You can do this by `pushing` your commits:

```
$ git push [remote-name] [remote-branch]
```

Note that you will be pushing to the remote branch from the branch your `HEAD` pointer is currently referencing. For example, let’s say that I cloned a repo then made some changes on the `master` branch. I can give the remote my local changes with this command:

```
$ git push origin master
```

- `pull`: This is equivalent to a `fetch + merge`. Not only will `pull` fetch the most recent changes, it will also merge the changes into your `HEAD` branch.

```
$ git pull [remote-name] [remote-branch-name]
```

Let’s say that my boss partner has pushed some commits to the `master` branch of our shared remote that fix our AI heuristics. I happen to know that it won’t break my code, so I can just pull it and merge it into my own code right away.

```
$ git pull origin master
```

# Project 1





**这些都是没有更新的，有一些思考可能有点问题，于2022.2.1重新写了一遍Project1但是并没有更新笔记**

**关于Resize的部分的思考还是看源代码的注释比较好。**

## ArrayDeque

### Resize的问题

#### ResizeBigger()

- 从旧数组到新数组的数据迁移过程：
- ArrayCopy直接从旧数组的头开始，到尾结束，全部放入新数组即可

#### ResizeSmaller()

- 从旧数组到新数组的数据迁移过程：
- 分情况讨论：

### NextFirst和NextLast问题

- 在resize()之后，原来的Deque都是按逻辑上从头到尾的顺序放到了新的数组之中
- 也就是说，Deque的最前面一个元素在新数组的第0位
- Deque的最后一个元素在新数组的第size - 1位
- 所以resize之后NextFirst为`newArray.length - 1`
- NextLast为`size`

# Lecture 12 Command Line Programming, Git, Project 2 Preview

![image-20220207214522734](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072145953.png)



- **第0个参数并不是程序本身的名字，就是第0个命令行参数。**
- **每个命令行参数都是一个字符串，即便是阿拉伯数字字符(1,2,3..)，也当做字符串处理。**

![image-20220207214653555](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072146814.png)



![image-20220207215732040](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072157251.png)

![image-20220207221949507](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072219185.png)

![image-20220207222226481](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072222745.png)

![image-20220207222532275](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072225521.png)

![image-20220207222943567](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072229836.png)

![image-20220207231440898](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072314115.png)

![image-20220207230827693](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072308959.png)

![image-20220207231055512](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072310764.png)

![image-20220207231538016](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202072315213.png)

# Lecture 14 Disjoint Sets

并查集

并查集有两个运算：

- `connect(x,y)`：将x和y连接起来
- `isConnected(x,y)`：如果x和y相连，返回true。连接可以是传递性的，也即x和y不需要直接相连。下面举了个例子

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322215735915.png" alt="image-20230322215735915" style="zoom:67%;" />

为了简化问题，我们：

- 强制所有的元素都是整数，而不是任意的数据。
- 提前声明元素的个数，并且最开始的时候所有元素都是互相不连接的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322221046930.png" alt="image-20230322221046930" style="zoom:67%;" />

并查集的接口：

- 需要设计一个高效的并查集，元素的个数N和方法的调用次数M可能都会很大。
- 并且不能假设对方法的调用有什么特定的顺序，比如一定是先connect，不能这样假定。方法的调用是随意分布的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322221134019.png" alt="image-20230322221134019" style="zoom:67%;" />

最笨的实现方法：记录以某种方式每条连接的边，在检查连接性的时候，对边做某种迭代，看看一个东西能不能连上另外一个东西。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322221411552.png" alt="image-20230322221411552" style="zoom:67%;" />

更好一点的方法：只记录每个元素所属的集合，处于同一个集合的元素就是相互连接的。如果将两个处于互不相连的集合中的两个元素A和B调用connect(A,B)，那么合并A和B所在的集合。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322221549310.png" alt="image-20230322221549310" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322221748590.png" alt="image-20230322221748590" style="zoom:67%;" />

## Quick Find

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322221810774.png" alt="image-20230322221810774" style="zoom:67%;" />

接下来的挑战是找到一种合理的数据结构来支持我们对各个集合的跟踪：

第一种方法：

- 使用`Map<Integer,Integer>`：第一个数代表集合编号，第二个数代表某个item。

- 很慢，因为item是处于Value的位置，集合编号是Key的位置，我们一般是给Key找Value，而不是给Value找Key。这个情况下，要找到哪个数属于哪个集合，得遍历整个Map，找到Value等于给定值的KV对。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322222122533.png" alt="image-20230322222122533" style="zoom:67%;" />

第二种可能的方法：

- 还是使用`Map<Integer,Integer>`：只不过这次反过来，第二个数代表集合编号，第一个数代表某个item。
- 这种情况下item变成了Key，item所属的集合变成了Value，要找到item属于哪个集合，就是给Key找Value。
- 大差不差就是我们待会会用的方法，但是更加低效一点。为什么？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322223017998.png" alt="image-20230322223017998" style="zoom: 67%;" />

IDEA #1：使用一个`List<Set<Integer>>`，每个集合记录连接在一起的元素，整个List存储所有的集合。

- 为了找到某个东西，需要遍历整个List的每一个集合。比如你要看xy是否相连，那要遍历一遍List找到x和y所处的集合，再看看是不是同一个集合。
- 最坏的情况，如果什么都没连接上，那`isConnected(5,6)`，找到5需要遍历N-1个集合，找到6需要遍历N个集合，整体复杂度是O(N)



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322223956200.png" alt="image-20230322223956200" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322224125905.png" alt="image-20230322224125905" style="zoom:67%;" />

Idea #1的性能总结：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322224536167.png" alt="image-20230322224536167" style="zoom:67%;" />

IDEA #2：已经很接近实践上的方法了。

- 基本思想：
  - 使用一个int型的数组，叫id，数组的下标是某个元素（我们强制元素是数字了，别忘了）。
  - id[n]的意义是，元素n所属于的集合的编号，并且我们规定处于同一个集合的元素的id值相同。这个编号可以是n所属的集合中的任意一个数字。
  - 比如下面的图，0、1、2、4的id都是数字4，4是集合{0,1,2,4}中的数字，id值都为4表明它们属于同一个集合，对任意两个集合中的元素`isConnected`会返回`true`。
  - `connect(x,y)`就将所有与x的id值相同的数组元素，以及所有与y的id值相同的数组元素，都赋值为同一个新的id值（比如都赋为id[x]），这个值是合并后的集合中的某个元素。比如下图右侧

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230322224816209.png" alt="image-20230322224816209" style="zoom:67%;" />

按照这个思想，基本上可以把实现写出来了：

- 构造器就是构造一个数组id，每个id的元素的值就是下标本身（因为最开始所有元素都不相连）
- connect(x,y)就是遍历id数组，找到id和x或者y的id相同的元素，改变其id为id[x]（id[y]也可）
- isConnect(x,y)：直接看图吧，懒得写了。

把没写全的connect补一下：

```java
public void connect(int p, int q) {
    int pid = id[p];
    int qid = id[q];
    for (int i = 0; i < id.length; i ++) {
        if (id[i] == pid) {
            id[i] = qid;
        }
        else if (id[i] == qid){
            id[i] = qid //其实就是不变
        }
        else {
            //不相等，什么都不做
        }
    }
}
```

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323205700460.png" alt="image-20230323205700460" style="zoom:67%;" />

但是这个还是太慢了，因为connect需要线性的时间复杂度。所以我们试点更加激进的玩意。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323210312931.png" alt="image-20230323210312931" style="zoom:67%;" />

## Quick Union

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323210334931.png" alt="image-20230323210334931" style="zoom:67%;" />

接下来我们还是使用刚才的数组的方式来呈现一切，但是我们需要做一些改进。

HardQuestion：我们怎样改变我们集合的呈现方式，使得将两个集合合并的时候，**只需要改变一个值？**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323210551644.png" alt="image-20230323210551644" style="zoom:67%;" />

我们想到的办法：给每个项分配一个父母（而不是一个id）。这样会导致一个类似树的形状。

- 比如集合{0，1,2,4}，我们给0分配父母-1（表明它是根节点），给0分配1作为其父母，给2分配1作为其父母，给4分配0作为其父母

- 另外一个集合{3,5}，我们给5分配3作为父母，给3分配-1表明它是根节点。
- 然后我们在合并两个集合的时候，只要将其中一棵树作为子树插入另外一棵树即可，只需要改变被插入的树的根节点的parent值。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323210832974.png" alt="image-20230323210832974" style="zoom:67%;" />

但是这个插入操作带来了另外的问题，我们把子树插哪里？两棵树，哪棵才是子树？

- 比如下面connect(5,2)，是将左边作为子树，还是右边?
- 假如把右边插入左边的树，3插在哪里？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323211455030.png" alt="image-20230323211455030" style="zoom:67%;" />

第二个问题，我们的回答是，把3直接插在根节点下（我们又没说一定要是二叉树对吧，肯定插在根节点下，对树的高度的增长是最小的。）

- 就像下面这样：
  - 先找到5的根节点是谁，然后找到2的根节点是谁。
  - 然后把2的根节点的父母设置为5的根节点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323212048871.png" alt="image-20230323212048871" style="zoom: 67%;" />

但是这样还有一个潜在的性能问题：要找到某个元素x的根是谁，需要沿着树往上爬，但是树可能会变得太高。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323212450832.png" alt="image-20230323212450832" style="zoom:67%;" />

最坏的情况下，就像下面这样：树直接退化成一条链表，沿着树往上爬，`findRoot()`的复杂度直接退化到O(N)。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323212632805.png" alt="image-20230323212632805" style="zoom:67%;" />



但是基于我们目前的思路，基本上可以把实现写出来了。

- `isConnected(x,y)`：找x和y的根节点，跟节点相同就说明它们处于同一棵树，返回true。
- `connect(x,y)`：把x所在的树插入y所在的树的根节点下（或者反过来，无所谓的，后面要优化）
- 上述两个方法都需要用到`findRoot()`，由于它会退化到线性复杂度，所以上述两个方法的最坏情况复杂度也是O(N)。当然，最好情况是O(1)。
- 构造器就是构造出N棵只有一个根节点的树，它们的父母都是-1（因为最开始所有的元素都不相连）。
- 补一下connect的实现：

```java
public void connect(int x, int y) {
    int xRoot = find(x);
    int yRoot = find(y);
    parent[yRoot] = xRoot;
}
```

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323212841069.png" alt="image-20230323212841069" style="zoom:67%;" />



性能总结：QuickUnion缺点：树可能变的太高，导致潜在的相比QuickFind更加坏的性能。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323213754660.png" alt="image-20230323213754660" style="zoom:67%;" />

## Weighted Quick Union

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323214237043.png" alt="image-20230323214237043" style="zoom:67%;" />

以一个问题引入，也是我们刚才剩下的那个问题：假如我们要`connect(5,2)`，我们把谁作为子树？

- 判定哪个选择更好的标准显然是，新得到的树的高度，谁更矮，哪个选择就更好。
- 对于这个问题本身，答案显然是A，因为A得到的新的树的高度比B小1。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323214323869.png" alt="image-20230323214323869" style="zoom:67%;" />

对于这个问题，我们的答案是：

- 跟踪每棵树的尺寸（元素的个数）
- 然后每次都是将小的树连接到大的树上去。（因为这样，树的高度至少不会增长，但是反过来就不一定了）
- **注意我们跟踪的是尺寸，不是树的高度哦，要得到高度也比较麻烦**。
- 所以下面的那个问题显然是D了。因为3对应的树元素个数更多，所以它不做子树。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323214713405.png" alt="image-20230323214713405" style="zoom:67%;" />



所以下面就是我们做出的微小改变：

- 依旧使用Parent数组
- `isConnected()`不用改变
- `connect()`在连接的时候把小的树作为子树连接到大的树的根节点上。所以我们需要某种方法来跟踪每棵树的尺寸。
  - 两种比较常用的方法：
    - 使用树的size的负值（-size），而不是-1，作为根节点的父母。我个人比较喜欢这种，简单明了。
    - 单独创建一个size array。size Array的大小和元素的个数一样，元素N所在的树的size直接查询`size[N]`。这样可能会有重复信息，但是实现起来方便（但是更新起来麻烦）。
- **最后我们可以保证树的高度是对数的，所以`findRoot()`的时间复杂度也就是O(LogN)**的

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323215214127.png" alt="image-20230323215214127" style="zoom:67%;" />

性能总结：

- `isConnected`和`connect`由于都要用到`FindRoot`，而FindRoot的复杂度是对数，所以它们的复杂度也是对数的。
- 构造器依旧是线性的，因为你需要一开始建立N棵只有一个根节点的树。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323215613134.png" alt="image-20230323215613134" style="zoom:67%;" />

为什么选择树的权重（Weighted，也即树的尺寸），而不是树的高度，来作为判定谁是子树的因素？

- 因为选择高度，没有Performance Gain，但是代码更加复杂。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323220328149.png" alt="image-20230323220328149" style="zoom:67%;" />

## Path Compression

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230323220723307.png" alt="image-20230323220723307" style="zoom:67%;" />



我们目前已经达到的：将不可能变为可能

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324160904470.png" alt="image-20230324160904470" style="zoom:67%;" />

但是我们可以变得更好：

- 下面是使用WeightedQuickUnion时，拓扑结构的最坏情况。

- 当我们执行`isConnected(15,10)`的时候，我们会分别从15和10沿着树往上爬，找到15和10的根节点。

- 在这个过程中，我们可以找到一些和15属于同一棵树的节点（10也是），我们可以将我们在这个过程中看到的所有的节点，都直接插到15的根节点的下面。
- 额外的开销可以忽略不计，但是我们的收益还挺大的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324161018530.png" alt="image-20230324161018530" style="zoom:67%;" />

在执行路径压缩之后，上面的这棵树就变成了：

- 从15沿着树往上爬，可以看见15、11、5、1、0。把除了根节点0的其他节点，都直接变成根节点的直接子节点。
- 10那边也类似。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324161411156.png" alt="image-20230324161411156" style="zoom:67%;" />

再举个例子：

- 路径压缩前：执行isConnected(14,13)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324161555204.png" alt="image-20230324161555204" style="zoom:67%;" />

- 路径压缩后：1==>2==>3

1. <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324161651855.png" alt="image-20230324161651855" style="zoom:50%;" />

2. <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324161641813.png" alt="image-20230324161641813" style="zoom: 50%;" />

3. <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324161713754.png" alt="image-20230324161713754" style="zoom:50%;" />



路径压缩之后，并查集的性能表现：

- 均摊下来基本上是常数时间。
- 也很好理解，经过很多次的connect和isConnected之后，整棵树会被压缩成扁扁的只有一层子节点的形式，所有的非根节点都是根节点的直接子节点。整棵树的高度基本上就是固定的为1。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324161846167.png" alt="image-20230324161846167" style="zoom:67%;" />

## Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324162114066.png" alt="image-20230324162114066" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324162130267.png" alt="image-20230324162130267" style="zoom:67%;" />

# Lecture 16 ADTs, Sets, Maps, BSTs

前面的关于ADT、Set/BST的定义什么的就略过了，二刷就没必要再做笔记了

## BST



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324164432088.png" alt="image-20230324164432088" style="zoom:67%;" />

### Insert

```java
insert(BST T, Key ik) {
    if (T == null) {
        return new BST(ik); //如果T变成NULL了，那么说明我们到达插入新节点的地方了，new一个新节点返回给上层调用
    }
    else if (ik < T.key) {
        T.left = insert(T.left, ik); //如果ik往左边插，那么T的左子树T.left要变成对T.left插入ik之后的结果，所以返回值要赋给T.left
    }
    else {
        T.right = insert(T.right, ik); //同理
    }
    return T; //这里很重要，你插入完成之后还要把插入ik之后的T返回给调用者的。
}
```

![image-20211129152330533](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111291523746.png)

### Delete

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324164956739.png" alt="image-20230324164956739" style="zoom:67%;" />

关于删除节点：

- **不要采用将待删除节点的child的<Key,Value>覆盖到待删除节点的方法来删除节点，可能会出错。就老实一点。**
  - 非要采用的话记得将child的left和right指针也覆盖到待删除节点的对应项去。

- 要删除的节点只有一个child或没有child都好处理。

- 一个child的情况下，让待删除节点的parent指向其child。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324165029073.png" alt="image-20230324165029073" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324165117196.png" alt="image-20230324165117196" style="zoom:67%;" />

- 如果待删除的节点有两个child，那么选择以待删除的节点为根节点的树，选择这棵树的左子树最大节点，或者右子树的最小节点，作为新的根节点。
  - 待删除的节点有什么特点？是一棵树的根节点，它比左子树任意一个节点大，比右子树任意一个节点小。
  - 所以我们要选择出来的新的根节点，也必须要有这个特点。而左子树最右侧的节点，和右子树最左侧的节点，就有这个特点。


- 关于图里的why：如果不是只有一个或零个child，那么我们选中的这个节点(elf或cat)，就不是左子树最大(cat)或右子树最小的(elf)，假定选中cat作为新的根节点，那么cat的左child会比它更小（右子树本来就比它大，如果有右子树的话，选的就是右子树而不是cat了），同样的道理，elf的右子树会比它更大（左子树本来就比它小）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111291630070.png" alt="image-20211129163032866" style="zoom:67%;" />

总结一下就是：

- 删除具有两个子节点的节点的时候，就以这个节点为根节点，找它构成的树，左子树里最靠右侧的节点，或者右子树里最靠左的节点。
- 比如下面，以要删除的k为根节点，它的左子树最靠右侧的节点是g（**不是f**），右子树里最靠左的是m。任选一个作为新的子节点代替k（**只复制值过去，不复制left和right指针**），删除掉选中的那个节点就行了。而删除选中的节点，回归到case1（选m）或者case2（选g）
- **注意一下删除是先删除g，然后把g的值覆盖掉k，但是不改变k的left和right指针**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324165405479.png" alt="image-20230324165405479" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324165528466.png" alt="image-20230324165528466" style="zoom:67%;" />

- BST as Map
  - 给定一个Value值，没办法高效的找到每一个VALUE为这个给定值的KEY的集合，因为我们的查找是根据Key的大小来决定往左边还是右边查找的，只能遍历去找。
  - 但是实际上K-V也并不是用来干这个的。


![image-20211129164224093](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111291642277.png)

## Summary

![image-20211129164907210](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202111291649416.png)

## **BST Implementation Tips**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324170507200.png" alt="image-20230324170507200" style="zoom:67%;" />



# Lecture 17 B-Trees (2-3, 2-3-4 Trees)

- **注意，大O并不等于是最坏情况，某个操作的最差情况是O(LogN)的。那你说它是O(N^2)， O(N\^3)都是对的**。

![image-20230324172139822](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324172139822.png)

## Height, Depth, and Performance

- 深度：一个节点的深度是它离根节点有多远（以边数计）
- 树的高度：它的最深的叶子节点的深度。
- 平均深度：所有节点的深度之和除以节点总数。所有节点的深度的平均值

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324172416841.png" alt="image-20230324172416841" style="zoom:67%;" />

- 一棵树的高度决定了找到一个节点的最坏情况下的时间复杂度。
- 平均深度决定了找到一个节点的平均时间。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324172559456.png" alt="image-20230324172559456" style="zoom:67%;" />

不过好消息是，现实世界的BST，是对数的。可以通过对一棵树进行随机插入和删除来模拟。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324173917758.png" alt="image-20230324173917758" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324173936867.png" alt="image-20230324173936867" style="zoom:67%;" />

但是坏消息是，我们并不是总可以随机插入，万一我们要插入的东西是随着时间的顺序生成的呢？

所以还是需要一些更好的东西。

## B-trees / 2-3 trees /2-3-4 trees

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324174053177.png" alt="image-20230324174053177" style="zoom:67%;" />

改进的办法是，让叶子节点超量存储东西：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324174157591.png" alt="image-20230324174157591" style="zoom:67%;" />

但是这个办法还是有一个问题：如果我们一直往同一个叶子节点里插入值，某个叶子节点可能变的太长太长，到最后那叶子节点直接退化成一条长长的链表。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324174236981.png" alt="image-20230324174236981" style="zoom:67%;" />

解决办法是：

- 对一个叶子节点上最多能存储的项目个数做出限制，比如L = 3，限制3个。
- 如果任何一个节点拥有超过L个项目，给一个给其父节点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324185356059.png" alt="image-20230324185356059" style="zoom:67%;" />

但是这又带来了另外一个问题，叶子节点上那么多的项，选哪一个传递给父节点？如果像上图那样，选择17丢上去，那问题来了，16小于17，但是却被丢到了右子树。

解决办法：对我们的方法进行一些微调：

- 除了将选定的项传递给父节点以外，选定的这个项将原本的节点一分为二，分裂得到的两个节点作为父节点的新的子节点。
- 也就是所谓的Split。
- 比如下面，17将原来的叶子节点一分为二，16丢到15和17中间，18和19丢到17的右边。 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324192217206.png" alt="image-20230324192217206" style="zoom:67%;" />

基本的B树我们就大概发明出来了：

- 检测一个节点是否包含预期值需要遍历这个节点，但是这是常数时间，因为L是常数。
- 如果一个非叶子节点也变得太满，怎么办？我们也将其分裂开，就跟处理满了的叶子节点一样。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324192645618.png" alt="image-20230324192645618" style="zoom:67%;" />



接下来举了一个例子：

- 红框都是节点分裂前临时存在的一个形态，我们随即就进行Split。
- 加入20和21：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324192917133.png" alt="image-20230324192917133" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324192936146.png" alt="image-20230324192936146" style="zoom:67%;" />

- 然后加入25和26：
  - 注意这里不仅分裂了叶子结点`[20,21,25,26]`
  - 在将21传递给其父节点之后，父节点也超过了3个项，还分裂了这个父节点`[15,17,19,21]`

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324193034598.png" alt="image-20230324193034598" style="zoom:67%;" />

**如果根节点也满了怎么办？那就分裂根节点啊，得到新的根节点，树的每个节点深度都加1**：

- 像下面这样，根节点`[13,17,21,23]`，选17将其分裂成两部分，17上移一层成为新的根节点，两部分成为17的子节点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324193328855.png" alt="image-20230324193328855" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324193347623.png" alt="image-20230324193347623" style="zoom:67%;" />



至此我们就得到了完美的B树：

- b树有完美的平衡。
- 如果我们分裂根节点，每个节点都被往下推一层
- 如果我们分裂叶子节点或者内部节点，树的高度根本不改变。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324193530137.png" alt="image-20230324193530137" style="zoom:67%;" />

所谓的Spliting-Tree其实就是B树：

- L=3的B树，也叫作2-3-4树，或者2-4树。因为L=3，一个节点可以有2或3或4个子节点。
  - 这种情况下，节点有4个项的情况下，选择从左往右数第二个项往上传递。

- L=2的B树，叫做2-3树。
  - 这种情况下，分裂节点的时候，节点有3个项，选择中间那个项往上传递。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324193734332.png" alt="image-20230324193734332" style="zoom:67%;" />

B树在两个特定的语境下非常受欢迎：

- 小L，就是今天讲的B树
- 大L，索引啦，B+树嘛。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324193906762.png" alt="image-20230324193906762" style="zoom:67%;" />



## B-Tree Bushiness Invariants

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324194035118.png" alt="image-20230324194035118" style="zoom:67%;" />

由于我们建立B树的方式，使得B树有如下的不变性：

- 所有的叶子节点离根节点的距离一定相同
- 一个非叶子节点的节点，如果它有k个项目，那么它一定有恰好k+1个子节点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324200526093.png" alt="image-20230324200526093" style="zoom:67%;" />

## B-Tree Runtime Analysis

B树的高度的分析：

- B树的高度介于$\log_{L+1}N$到$\log_2N$之间。
- 最好的情况：每个节点都填满了L个项。
- 最坏的情况：所有的非叶子节点都只有一个项，这个时候除去叶子层，看起来就是一棵二叉树。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324200844993.png" alt="image-20230324200844993" style="zoom:67%;" />

所以最后的时间复杂度的分析：肯定是对数的，因为高度是对数的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324201041424.png" alt="image-20230324201041424" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324201105027.png" alt="image-20230324201105027" style="zoom:67%;" />

## Summary

总结什么的就直接看图吧

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324201225793.png" alt="image-20230324201225793" style="zoom:67%;" />



## 2-3 Tree Deletion (Extra)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324201345508.png" alt="image-20230324201345508" style="zoom:67%;" />

额外的内容，2-3树的删除。

就像BST的删除一样，B树的删除也比较复杂。但是在这里我们将一起开发一种删除算法



首先回顾一下BST在删除具有两个子节点的节点的时候的方法：看红框

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324201708558.png" alt="image-20230324201708558" style="zoom:67%;" />

2-3树的删除方法和BST的类似：

- 如果删除的Successor处于一个有多个Key的叶子节点，那很简单，直接删掉选中的那个就行了。
- 但是如果叶子节点里只有一个Key呢？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324202558821.png" alt="image-20230324202558821" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324202801764.png" alt="image-20230324202801764" style="zoom:67%;" />

**对于只有一个项的叶子节点，我们不能简单的就直接将这个节点整个删掉，为什么？**

- 因为如果删掉的话，就不符合B树的不变性质了。如下图红色箭头的位置本来应该有一个子节点的，但是被直接删掉了。
- 所以我们不能把整个节点直接移除，而应该留着这个空节点，然后用值去填充它。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324202912461.png" alt="image-20230324202912461" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230324203009642.png" alt="image-20230324203009642" style="zoom:67%;" />

后面先不补了。先接着往下看吧

- [Slides](https://docs.google.com/presentation/d/1NgaMi7IWs94sC_fhF7_UWx2O4LyPicvVJ9xkru9m2dU/edit#slide=id.g508ece10b0_1_1546)









# Lecture 18 Red Black Trees

## The Bad News

- Bad news About B-Tree：
  - 需要维护不同的节点类型（一个节点里可能有1、2、3）个值。
  - 不同节点类型直接的转换，如L等于2时，临的3值节点到2值节点的转换。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325142557095.png" alt="image-20230325142557095" style="zoom:67%;" />

## BST Structure and Tree Rotation

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325142650407.png" alt="image-20230325142650407" style="zoom:67%;" />

- 因此引出红黑树，更加简单且更加快的数据结构。
- 通过树的旋转，可以在具有相同节点个数的树的不同形态之间转换。如下图可以通过旋转在这5种状态间转换。
  - 还有一个描述具有N个节点的二叉树具有的可能的形态的个数的`Catalan(N)`
  - 可以在`2n - 6`次旋转之内从一个形态转移到任何其他可能的形态。
    - 因此如果给定一棵极度不平衡的树，存在一个旋转的序列将它变成一棵平衡的树。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325142827599.png" alt="image-20230325142827599" style="zoom:67%;" />

### Tree Rotation Definition

树的旋转：
- 以左旋为例：记X为G的右子节点（此时X即P），旋转使得G成为X（也就是节点P）的新左子节点。
- 下图示例，可以看作G和P暂时合并，然后再分裂，将G往下送，往左走。
- **需要注意的是，P原先的左子节点K现在变成了G的右子节点**。
- 仍然会保持BST树的属性，在这个示例中树的高度增加了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325143029415.png" alt="image-20230325143029415" style="zoom:67%;" />

- 右旋： 记X为P的左子节点，旋转使得P成为X的新右子节点。
- 示例降低了树的高度。
- 注意，K旋转之前是G的右子节点，旋转之后，K变成了P的左子节点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325143240460.png" alt="image-20230325143240460" style="zoom:67%;" />

- 一个示例，说明如何通过一系列旋转使得树变成平衡状态：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325143407085.png" alt="image-20230325143407085" style="zoom:67%;" />

### Rotation for Balance

- [Demo](https://docs.google.com/presentation/d/1pfkQENfIBwiThGGFVO5xvlVp7XAUONI2BwBqYxib0A4/edit#slide=id.g313f9934ba_0_267)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325143712448.png" alt="image-20230325143712448" style="zoom:67%;" />



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325143830337.png" alt="image-20230325143830337" style="zoom:67%;" />

## Red-Black Trees

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325144137525.png" alt="image-20230325144137525" style="zoom:67%;" />

- 红黑树的定义：
  - BST树：实现起来容易，会变得不平衡，但是可以使用旋转来平衡，但是目前我们还没有算法。
  - 2-3树：从构建它的过程上来说就是平衡的，也就是不需要旋转。
  - 试图结合起来二者的优点：建立一棵BST树，使得它在结构上与一棵2-3树完全对应。由于2-3树是平衡的，我们这棵奇怪的BST树也会是。
    - 因此引出，如何用一棵BST树来表现一棵2-3树。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325144419352.png" alt="image-20230325144419352" style="zoom:67%;" />

### Representing a 2-3 Tree as a BST

Representing a 2-3 tree as a BST：
- 只有两个子节点的2-3树和BST是一模一样的。关键是具有3个子节点的2-3树怎么办。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325144831046.png" alt="image-20230325144831046" style="zoom:67%;" />

第一种想法：使用一个哑结点，dummy node：

- 不优雅，有一个Link被浪费了。d的Right指针（或者是f的Left指针，取决于e是谁的子节点）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230325145440596.png" alt="image-20230325145440596" style="zoom:67%;" />



- 使用**glue link**来呈现`3-node`，`3-node`的两个项中，较小的那个往左下推，变成子节点，将这个`glue-link`标记为红色。正常的`link`标记为黑色。
  - 红与黑
  - Java.utils.TreeSet就是红黑树


<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326105109936.png" alt="image-20230326105109936" style="zoom:67%;" />



- 关于红黑树的一些说明：
  - 在课程中我们永远选择把假象出来的`glue-link`放在靠左边，称作左倾红黑树，LLRB。
  - LLRB就是正常的BST树。
  - 在一棵LLRB和一棵等价的2-3树之间存在1-1映射。
  - 红色的`link`只是一个方便的假想，红色链接并不会起任何特殊的作用。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326105325598.png" alt="image-20230326105325598" style="zoom:67%;" />

练习1：画出对应的LLRB：

- 把每个3-node（也就是具有两个值的节点）拆开，较小的那个节点往左下放。
- 建议是自底向上拆可能看起来比较舒服。
- 搜索一棵LLRB，就直接当成一棵BST来搜索。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326105535081.png" alt="image-20230326105535081" style="zoom:67%;" />

练习2：下面的哪些是合法的LLRB?

从左往右：

1. 节点[A,B,C]是4-node，不会存在于2-3树。
2. LLRB是和B树一一对应的。因为B树永远是平衡树（叶子节点永远是一样深的），所以一棵合法的LLRB，其对应的B树应该是平衡的（即一棵合法的LLRB对应一棵合法的B树，一棵非法的LLRB，对应一棵非法的B树）。而2的LLRB画出来的对应的B树是不平衡的，C节点比其他叶子节点更深，所以2是不合法的LLRB
3. 同2。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326105831875.png" alt="image-20230326105831875" style="zoom:67%;" />

练习3：下面这棵B树，其对应的LLRB树的高度是？

- 画出B树对应的LLRB，数就完事了。
- **反正LLRB与其对应的B树的高度大概是2比1的关系**。两倍出现于B树每个非叶子节点都是3-node的情况。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326110554692.png" alt="image-20230326110554692" style="zoom:67%;" />

- 假如我们有一棵高度为H的2-3树，那么对应的LLRB的最大高度是？
  - 2-3树的每个节点（除了叶子节点）都是3-node，这种情况下，其对应的LLRB是最高的（用GlueLink画出LLRB来就一目了然）
  - 答案是2H + 1。**B树比LLRB更矮！！！！**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326110909637.png" alt="image-20230326110909637" style="zoom:67%;" />

- 左倾红黑树的一些属性：
  - 查找key值，把红黑树当成一棵BST来对待就好。
  - 不存在同时有两个红色Link的节点，否则对应的B树存在4-node。
  - 从根节点到每个（叶节点的）空引用的黑色链接的数目是一样的（因为你画出对应的B树，会保留全部BlackLink，消除全部RedLink，每个空引用都处于叶子结点，所有叶子结点在B树是一样深的）
  - **红黑树的高度的最大值大概就是与之相对应的2-3树的高度的2倍，因此红黑树的高度也是对数的。**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326111348348.png" alt="image-20230326111348348" style="zoom:67%;" />

- **红黑树的建造：**
  - 并不是先建立一棵2-3树然后再转换成一棵红黑树。
  - 而是插入一棵BST树然后通过旋转保持**左倾**红黑树属性。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326111751062.png" alt="image-20230326111751062" style="zoom:67%;" />

## Maintaining 1-1 Correspondence Through Rotations 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326112318709.png" alt="image-20230326112318709" style="zoom:67%;" />

建造红黑树的例子：插入的时候假装自己是那棵与自己对应的2-3树。

- 例子1：**无论何时，对一棵红黑树插入一个节点时，都是使用红色的链接（也许后面可能会进行翻转，但是插入的那一瞬间就是用红色的）。**
- 想象一下，如果你不是LLRB，而是B树，你插入一个值的时候，总是往叶子结点放（即便里面已经有一个值）。所以是用红色的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326112444071.png" alt="image-20230326112444071" style="zoom:67%;" />

- 例子2：当插入的值变成了右子节点。
  - 因为我们插入总是使用红色的Link，这个时候RedLink就变成了右倾。右倾是不允许的，LLRB我们要的是左倾。
  - 插入S的时候用`红色link`（假装部分，如果你是2-3树你会怎么做），**由于我们要得到左倾红黑树，所以对第二种形态进行旋转**（旋转部分）。


<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326112703056.png" alt="image-20230326112703056" style="zoom:67%;" />

- **例子3**：由于B树在插入时会出现那种暂时的4-node（也就是2-3树的某个节点会暂时性的出现有3个值在里面，然后随后进行节点的分裂）
  - 这个例子就是在相应的红黑树中也允许临时性的出现这种不合法的情况。 
  - 使用具有两个`暂时性的红色link`的节点来表现这种情况。
  - **并且两个红色的link一个左倾一个右倾，也就是说允许暂时性的破坏左倾要求。**
  - **并且只允许同一个节点的两个红色link一个左倾一个右倾，不能出现连续两个左倾redlink。**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326112942363.png" alt="image-20230326112942363" style="zoom:67%;" />

- **例子4**：连续插入两个值都是左子节点，造成连续的两个左倾红色`link`怎么处理。
  - 这个例子第一个关键点：按照BST的规则插入E之后是第二个状态，属于是暂时性的4-node，但是这个状态和上个例子所提到的长得不一样。
  - 所以进行旋转。但是还是没有到B树分裂的那一步。旋转之后回归到了例子3。
  - **关于为什么要进行这一步的旋转：因为我们的最终目的是得到稳定形态的2-3树，而不是现在这个具有临时4-node的B树。旋转之后得到的第三个状态是可以通过`flip()`得到最终目的的，这一步的旋转就是为了将形态二变成可以翻转的形态3**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326113225836.png" alt="image-20230326113225836" style="zoom:67%;" />

- 例子5：在2-3树（B树）中，暂时性的`4-node`在随后是需要进行`split()`的，也就是节点分裂，所以红黑树也需要去表现相应的过程。
  - 第二个关键点：如何去处理临时性非法的红黑树。也就是如何在得到第三个状态之后，去模仿2-3树的节点满了之后的分裂节点的过程。
  - 进行颜色的翻转。将所有与节点B接触的`link`的颜色翻转，黑变红，红变黑。
  - 如何想到的？
  - 很简单，画出分裂后的2-3树的形态，然后画出它对应的左倾红黑树的形态，观察一下就会发现节点B的各个`link`的颜色刚好翻转了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326113351912.png" alt="image-20230326113351912" style="zoom:67%;" />

- 至此，我们就发明了红黑树。一些关于上述例子的总结。
  1. 插入的节点变成了右子节点，造成红色链接右倾：左旋。
  2. 连续两个左倾红色链接：两个红色链接连接三个节点，右旋这三个中最上面的父节点，把同时连接了两个RedLink的那个节点变成新的父节点，得到3。
  3. 红色链接一左一右：颜色翻转。
  4. 此外还有最后一个细节：级联操作，有可能一次旋转或者翻转操作会导致一个额外的需要修复的错误
    - 比如修复2，得到3，修复3。
    - 比如如果翻转后得到的`redlink`恰好是右倾而不是左倾的。(例子5翻转后的`redlink`恰好是左倾的。)。见下面的例子6。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326113526818.png" alt="image-20230326113526818" style="zoom:67%;" />

- 例子6。
  - 图一：翻转模仿B树分裂过程，修复临时性4-node问题。但是出现了新的问题，新得到的`redlink`是右倾而不是左倾的。
  - 图二：所以左旋B。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326114249172.png" alt="image-20230326114249172" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326114350160.png" alt="image-20230326114350160" style="zoom:67%;" />



### **Optional Exercise**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326114630875.png" alt="image-20230326114630875" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326114641191.png" alt="image-20230326114641191" style="zoom:67%;" />

- [LLRB/2-3TreeInsertionDemo](https://docs.google.com/presentation/d/1jgOgvx8tyu_LQ5Y21k4wYLffwp84putW8iD7_EerQmI/edit#slide=id.g463de7561_042)

## LLRB Runtime and Implementation



- 总结：
  - 运行时间基本上都是`logN`。
  - 删除不讲。
  - Java的内置TreeMap是红黑树，只不过是对2-3-4树的映射。也不是一一对应的，更复杂的工作，更快的速度。
  - 还有其他很多种类型的树，实现Map也还有其他很多种方法，比如Hash。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326115825169.png" alt="image-20230326115825169" style="zoom:67%;" />

实现：[RedBlackBST.java](https://algs4.cs.princeton.edu/33balanced/RedBlackBST.java.html)

红黑树的实现还是简单的，只要稍微修改一下put方法：

- 不管怎么样，先插入，使用RedLink
- 然后判断：
  - 如果RedLink右倾，并且左子树不是红色Link，那么就是例子2（往上翻），左旋。
  - 如果连续h有两个左倾RedLink的左子节点，那么右旋h
  - 如果h的左右子节点都是RedLink，那么翻转h的颜色。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326115922677.png" alt="image-20230326115922677" style="zoom:67%;" />

## Search Tree Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326120444126.png" alt="image-20230326120444126" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326120505040.png" alt="image-20230326120505040" style="zoom:67%;" />

# Lecture 19 Hashing

BST、BT、LLRBT用作Set的缺点：为了解决这些缺点我们引出hashtable

- 要求插入的东西是可比较的，能够针对他定义什么是大与什么是小于

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326134536469.png" alt="image-20230326134536469" style="zoom:67%;" />

## Using Data as an Index

第一种方法：**DataIndexedIntegerSet** 

- 使用一个Boolean数组，这个数组的索引项就是我们加入的数据
- 一开始数组所有的值都为false。
- 加入某个数据的N时候，数组的第N项设置为true。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326134909094.png" alt="image-20230326134909094" style="zoom:67%;" />

实现：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326134946382.png" alt="image-20230326134946382" style="zoom:67%;" />

缺点：

- 极度浪费空间。为了支持检查所有正整数是否存在，需要上亿个布尔值。
- 只能支持插入整数，普适性不好

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326135342801.png" alt="image-20230326135342801" style="zoom:67%;" />

## DataIndexedEnglishWordSet

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326135656753.png" alt="image-20230326135656753" style="zoom:67%;" />

理想状态下，我们想要的是一个能够存储任意类型数据的、数据作为索引的集合。但是我们之前的想法只支持整数。我们现在先考虑存储字符串

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326135725987.png" alt="image-20230326135725987" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326135918302.png" alt="image-20230326135918302" style="zoom:67%;" />

后面的进化过程就略过了。总之，我们为了能够存储任意类型，需要一个方法将任意类型的数据转换为数字的方法，而这个方法，就是计算数据的哈希值。

## Integer Overflow and Hash Codes

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326140138056.png" alt="image-20230326140138056" style="zoom:67%;" />

- 关于由于整数溢出导致的理论上的哈希值达不到且与其他对象的哈希值恰好冲突的可能

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326140201712.png" alt="image-20230326140201712" style="zoom:67%;" />

- 下面的例子里的俩字符串的哈希值由于整数溢出，变成一样的，导致哈希碰撞的问题。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326140232962.png" alt="image-20230326140232962" style="zoom:67%;" />

- hash要解决的两个问题

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326140706421.png" alt="image-20230326140706421" style="zoom:67%;" />

## Hash Tables:Handling Collisions

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326140745229.png" alt="image-20230326140745229" style="zoom:67%;" />

- final idea,about the bucket,could be anything we can iterate.ANY collenction.

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326140802301.png" alt="image-20230326140802301" style="zoom:67%;" />

- work flow:Use LinkedList as bucket：
  - how to search for existed items
  - how to add items.

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326141440158.png" alt="image-20230326141440158" style="zoom:67%;" />

- add（insert）的最坏复杂度也是O(Q)，因为我们必须在插入前遍历表来确定要插入的元素是否事先存在。复杂度与最长的那条List的长度成大O相关。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326141550818.png" alt="image-20230326141550818" style="zoom:67%;" />

改进：**Saving Memory Using Separate Chaining and Modulus**

- 使用取余的方法的downside:
  - 表变长，当然，可以通过resize改进

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326141634903.png" alt="image-20230326141634903" style="zoom:67%;" />

- 哈希表的工作流程：
  - 稍后说明如何处理负的hash code

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326141659079.png" alt="image-20230326141659079" style="zoom:67%;" />

## Hash Table Performance

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326141735903.png" alt="image-20230326141735903" style="zoom:67%;" />

- Performing Analysis：
- 好消息：我们用了更少的内存，并且可以处理任意类型的数据了。
- 坏消息：最坏情况的运行时间变成了$\Theta(Q)$，Q是最长的链的长度。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142132368.png" alt="image-20230326142132368" style="zoom:67%;" />

- 一个小小的问题 反映出哈希表的一个弊端
  - bucket could get very long
  - 一千万个item，5个Bucket，最好的情况下也是每个链表两百万个item，插入与查询变得很慢

- 提高：由于复杂度与Q有关，而Q与item的个数N成线性关系，假设我们有M个bucket，最好的情况下Q = N / M
  - 所以可不可以设法保证Q是O(1)的？如果可以，那么线性优化成了常数

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142340313.png" alt="image-20230326142340313" style="zoom:67%;" />

- 解决上述问题的方法就是，我们不采用固定个数的bucket，而是让bucket数也跟着涨
  - 当N / M大于某一个装载因子的时候，我们增加更多的bucket数(也就是让`ArrayList<LinkdedList>`的size变大)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142416454.png" alt="image-20230326142416454" style="zoom:67%;" />

- 一个`resize()`的例子，值得注意的是增加bucket数目后各个item的位置变化
  - 使得items move around ，lists get shorter
  - resize前后各个item的位置取决于新的M和各自的hashcode，原来不在一个bucket的可能变得在一个bucket，而原来处于同一个bucket的item可能会分开。
  - **也就是说bucket长度变化之后，需要对哈希表中的每个item都重新确定其正确的Bucket**。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142521515.png" alt="image-20230326142521515" style="zoom:67%;" />

- 运行时间分析
  - 总体来说变成了O(1)，对于触发resize的情况我们另做分析，但是均摊下来就是常数时间

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142555078.png" alt="image-20230326142555078" style="zoom:67%;" />

- 触发Resize的时候，由于需要对所有的item进行重新分布，所以是$O(N)$的复杂度，N是item个数。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142638577.png" alt="image-20230326142638577" style="zoom:67%;" />

- Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142740015.png" alt="image-20230326142740015" style="zoom:67%;" />

- item的平均分布对哈希表的运行时间保持常数时间是至关重要的。下图右侧比左侧好，但二者的装载因子是一样的
- 接下来将会讨论如何确保items平均分布，但首先讨论一下java的哈希表实践 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142805134.png" alt="image-20230326142805134" style="zoom:67%;" />

## Hash Tables in Java

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142826554.png" alt="image-20230326142826554" style="zoom:67%;" />

hashtable in java

- hashCode()的默认方法返回对象的内存地址

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326142915162.png" alt="image-20230326142915162" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326143604026.png" alt="image-20230326143604026" style="zoom:67%;" />

- 实现java中的对hashcode取模的运算，由于`%`的语义问题，处理负数的时候会出错，使用另外一个函数：
  - `Math.floorMod()`

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326143633449.png" alt="image-20230326143633449" style="zoom:67%;" />

- java  Hashtable workflow：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326143653213.png" alt="image-20230326143653213" style="zoom:67%;" />

使用hashmap和hashset的一些警告：

- 不要在HashSet或者HashMap存储能够改变的对象(指放进去之后还能改变)
  - 因为一改变，哈希值就变，就找不到它了。
- 永远不要在没有重写hashCode的情况下重写equals。
  - 因为要同时保证下面的两点： 
  - equals返回true，哈希值一定相等。
  - 哈希值相同，equals不一定返回true。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326143827488.png" alt="image-20230326143827488" style="zoom:67%;" />

## Good HashCodes (Extra)

- 关于如何写出一个好的hashCode函数：使得items分布均匀的关键

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326145554279.png" alt="image-20230326145554279" style="zoom:67%;" />

- String hashCode()

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326145700225.png" alt="image-20230326145700225" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326145719019.png" alt="image-20230326145719019" style="zoom:67%;" />

- 关于选择126的弊端

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326145746204.png" alt="image-20230326145746204" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326145830448.png" alt="image-20230326145830448" style="zoom:67%;" />

- hashCode() in LinkedList：
  - 一个链表的哈希值等于表中存储的对象的哈希值之和
  - 省事的情况下只看前几个对象

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326145913914.png" alt="image-20230326145913914" style="zoom:67%;" />

- BST hashcode：一个递归的数据结构，计算哈希值也是使用递归的

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326145939896.png" alt="image-20230326145939896" style="zoom:67%;" />

## Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326150109492.png" alt="image-20230326150109492" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326150128689.png" alt="image-20230326150128689" style="zoom:67%;" />



# Lecture 20 Heaps and PQs

- 优先队列接口。
  - 并不一定是要是最小，可以是各种的最，最好，最大。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326150240319.png" alt="image-20230326150240319" style="zoom:67%;" />

- 一些可能的实现：但都不太理想，各有缺点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326151701255.png" alt="image-20230326151701255" style="zoom:67%;" />

## Heaps

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326151743370.png" alt="image-20230326151743370" style="zoom: 67%;" />

- **引入堆：堆是一颗二叉树，但不是BST。**遵循最小堆的性质，并且是完备的。
  - 最小堆：每个节点都小于或等于它的两个子节点。
    - 图四节点5不满足。
  - 完备性：缺失的节点只会出现在最底层（如果有的话），并且所有的节点都尽可能的靠左放置（也就是缺口会尽量靠右）
    - 也就是说，**每个节点都应该有两个子节点，除非它的子节点刚好是处于最底层的**。
      - **除了处于最底层的子节点可以只有一个，其他地方的子节点都必须有两个。**
      - 下图的第二个就是不满足这个条件所以不是堆。
      - ![image-20221009160205394](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202210091602627.png)
    - 下图三缺口就应该是靠右的，也就是2那个节点应该在左子树。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326152254628.png" alt="image-20230326152254628" style="zoom:67%;" />

- 堆非常适合实现优先队列。
- 各个堆运算的实现：
  - `getSmallest()`：返回根节点的值。
  - `add(x)`：先把临时性的放在最底层(**最靠左的空位，这是为了遵循堆的完备性**)，然后把它一层层往上移，直到找到合适的位置（这是为了最小性）。
  - `removeSmallest()`：删除根节点，找到叶子结点最靠右的那一个（**之所以找这个位置的叶子结点，还是为了保证完备性**，移除这个叶子节点不破坏完备性），把它挪到根节点的位置，然后把新的根节点向下移动，直到找到合适的位置。
    - 在这里需要补充的是，向下挪的时候，选择和两个子节点中较小的那个交换。
  - [Demo:插入和删除](https://goo.gl/wBKdFQ)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326152443387.png" alt="image-20230326152443387" style="zoom:67%;" />

- Java中的堆的实现

## Tree Representations

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326152553893.png" alt="image-20230326152553893" style="zoom:67%;" />

- 首先是树的各种实现，并不只有BST的那种实现方法。
- 第一大类：直接保存从节点到子节点的引用。
  - 第一种：一个节点包含四个域：Key ，左、中、右子节点的引用。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326152649035.png" alt="image-20230326152649035" style="zoom:67%;" />
  - 第二种：两个域：Key和一个子节点的数组，这个实现方法可以用来实现多叉树，使得一个节点可以有任意多个子节点。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326152708083.png" alt="image-20230326152708083" style="zoom:67%;" />
  - 第三种：每个节点有三个域，一个Key，一个指向它的最左侧子节点，一个域指向它的亲兄弟节点。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326153204123.png" alt="image-20230326153204123" style="zoom:67%;" />
  - 对同一棵树有不同的实现方法。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326153220261.png" alt="image-20230326153220261" style="zoom:67%;" />
- 第二大类：不直接保存节点到子节点的引用，而是对节点进行层序编号。
  - 第一种：类似对并查集的实现。parents[i] 存储的就是keys[i]的父节点(在keys[]中的下标)。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202161502281.png" alt="image-20220216150214145" style="zoom:67%;" />
  - 第二种：只适用于**完全树**，因为直接省去了parents数组。由于parents数组永远是00011223344这样下去，所以对完全树可以直接省去。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202161511481.png" alt="image-20220216151147345" style="zoom:67%;" />

- 这最后一种（第二类第二种）就是Java中堆的实现方式了。因为堆是完备的（所有的节点都是尽可能靠左的，所以parents数组不会在中间出现缺口）
  - swim的实现方法。
  - parent的实现方法。
  - `Parent()`方法，照着图上随便找几个节点看看就知道了，当是归纳出来的吧，可以用数学证明，但是我比较懒。
  - 这种实现方法代码简单，使用的内存大概是指针映射（第一类）的三分之一。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326153554937.png" alt="image-20230326153554937" style="zoom:67%;" />

- 方法3B：对计算Parent做了一点小小的优化

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326154849860.png" alt="image-20230326154849860" style="zoom:67%;" />

- 总结：
  - 堆很适用于实现优先队列，因为堆天生就很适合处理插入多个等值节点的情况。而BST则不太适合，因为BST要求所有的插入的值必须是能够分个大小的，处理不了相等值的情况。
  - **堆的增加和删除是对数级别复杂度的，但是是均摊的，因为涉及数组的`resize()`**。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326154922901.png" alt="image-20230326154922901" style="zoom:67%;" />

- 关于优先队列还有一些小问题
  - 一个优先队列怎么知道如何去确定在队列中的哪个项更大？这个问题有两个解。
    - 一是限制我们的类型参数是`Comparable`的。`<Item extends Comparable<Item>>`这个是默认的行为。
    - 二是实现一个构造器，这个构造器有一个参数是一个`Comparator`。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326154946561.png" alt="image-20230326154946561" style="zoom:67%;" />



- 最后就是关于数据结构的一些总结了

![image-20220216165003471](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202161650615.png)



# Lecture 21 Tree and Graph Traversals

- 三种深度优先遍历、层序遍历

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202162215454.png" alt="image-20220216221515301" style="zoom:67%;" />

- 三种深度优先遍历的快速方法。
  - 前序：经过左边的时候。
  - 中序：经过底下的时候。
  - 后序：经过右边的时候。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202162218976.png" alt="image-20220216221854841" style="zoom:67%;" />

三种遍历的递归算法：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326162120265.png" alt="image-20230326162120265" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326162133130.png" alt="image-20230326162133130" style="zoom:67%;" />

- 三种深度优先遍历的使用例子
  - 先序遍历：打印目录，因为你要先打印父目录（或者说本目录），然后打印各个子目录。父目录先于所有的子目录被打印出来，所以是先序。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202162245058.png" alt="image-20220216224551915" style="zoom:67%;" />
  - 后序遍历：计算目录总大小，因为你要先知道所有子目录的大小，加上自己这个目录的大小，才能计算本目录的总大小
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202162246667.png" alt="image-20220216224621531" style="zoom:67%;" />

## Graphs

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326162240569.png" alt="image-20230326162240569" style="zoom:67%;" />

图的定义：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326162344561.png" alt="image-20230326162344561" style="zoom:67%;" />

简单图的定义：没有回环，任意两个节点之间最多只有一条边相连。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326162416527.png" alt="image-20230326162416527" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326162531911.png" alt="image-20230326162531911" style="zoom:67%;" />

图的术语：

- 顶点的集合
- 边的集合
- 一条边两端的点称作相邻
- 边是可以有权重的（有权图）
- 路径（Path）：
- 圈：起点和终点是同一个点的路径。
- 如果两个点之间存在一条路径，称这两个点是相通。如果所有点都是相通的，那称这个图是连通图。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326162559442.png" alt="image-20230326162559442" style="zoom:67%;" />

## Graph Problems

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326163348387.png" alt="image-20230326163348387" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326163406727.png" alt="image-20230326163406727" style="zoom:67%;" />

## Depth-First Traversal



- 图的深度优先遍历
  - 首先是一个s-t连通性问题。也就是给定起点和终点，求一条连通路径。[demo](https://docs.google.com/presentation/d/1OHRI7Q_f8hlwjRJc8NPBUc1cMu5KhINH1xGXWDfs_dA/edit?usp=sharing)
  - 递归算法的思想很简单：
    1. 如果s == t，那么肯定相连。
    2. 记V是s的某个未标记的邻居，如果connected(V,t)，那么V和t相连，而V又是s的邻居（V和s相连），所以s和t肯定相连。
    3. 对于V，递归的回到1和2
    4. 当然，为了避免出现无限循环，需要标记之前已经访问过的顶点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171126345.png" alt="image-20220217112609193" style="zoom:67%;" />

- 给定起点s，求从s到任意其可到达的定点的路径。[demo](![](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171229374.png))
  - 深度优先搜索。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171228915.png" alt="image-20220217122847777" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171229374.png" alt="image-20220217122933238" style="zoom:67%;" />

- 关于树和图的遍历的对比。
  - **图的遍历是不具有唯一性的，而树的遍历具有唯一性**。
  - 图的深度优先遍历同样有前序和后序。此外还有广度优先遍历。
  - 前序：操作先于调用。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171252505.png" alt="image-20220217125248352" style="zoom:67%;" />
  - 后序：操作后于调用
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171253696.png" alt="image-20220217125325360" style="zoom:67%;" />
  - 广度优先：按从起点到节点的距离的顺序来遍历，先遍历距离为1的所有节点，然后距离为2的..
    - 广度优先算法的实现，和树的层序遍历的算法实现很类似。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171254713.png" alt="image-20220217125420558" style="zoom:67%;" />

## Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326164641547.png" alt="image-20230326164641547" style="zoom:67%;" />

# Lecture 22 Graph Traversals and Implementations

无权图的最短路径：给定起点s和图G，找到从s到所有其他顶点的最短路径。

- 使用广度优先搜索。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326170208629.png" alt="image-20230326170208629" style="zoom:67%;" />

- 只适用于无权图，在这里的图都是没有带权的。
- 所以不能拿来给地图应用做导航算法捏，因为每条路的长度不一定一样。
- **广度优先算法求某点到任意点的最短路径。**这里的最短指的是**经过的边的数目最小**。
- 使用一个队列。队列里的元素要么是距原点等距K的，要么是K + 1 的
- 即使没有`edgeTo[]`和`distTo[]`也是广度优先。
- `edgtTo[]`是用来寻找路径的。
- [demo](https://docs.google.com/presentation/d/1JoYCelH4YE6IkSMq_LfTJMzJ00WxDj7rEa49gYmAtc4/edit?usp=sharing)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171829456.png" alt="image-20220217182916301" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326171011109.png" alt="image-20230326171011109" style="zoom:67%;" />



## Graph API

- 我们需要：
  - 图的底层表示
  - 图的合适的API

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261609812.png" alt="image-20220226160940546" style="zoom:67%;" />


- 第一个简化：
  - 对图的节点编号，不考虑图的标签。实现图的时候也是使用相应的编号。
  - 将节点的标签和编号用Map<Label,Integer>映射。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261612049.png" alt="image-20220226161201804" style="zoom:67%;" />
- Graph API：
  - 构造器必须提前给定节点的个数，并且节点的个数是固定不变的。
  - 可以在v号节点和w号节点之间添加边，但是不允许向图中添加节点。
  - `adj(int v)`返回的是`v`号节点的所有相邻节点，这些邻居保存在一个可迭代的容器中，如`ArrayList<Integer>`。
    - Iterable的类型参数之所以是整数是因为所有节点都是用编号呈现的。
  - 不支持有权的节点或边。
  - 没有求度的方法，但是可以借助API实现。
    - ![image-20220226162450304](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261624531.png)
  - 可以得到图的节点数和边数。
  - 可以借助API实现打印图的所有边的方法：
    - ![image-20220226162504871](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261625079.png)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261616008.png" alt="image-20220226161649746" style="zoom:67%;" />

## **Graph Representation and Graph Algorithm Runtimes**

- 邻接矩阵
  - 使用一个二维`boolean`矩阵。
  - 有一个复杂度问题没看，第二张图，不难。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261630195.png" alt="image-20220226163036748" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326172442138.png" alt="image-20230326172442138" style="zoom:67%;" />
- HashSet< Edge >：边的HashSet，每条边是一对int

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326172225226.png" alt="image-20230326172225226" style="zoom:67%;" />



- Adjacency List：邻接表
  - Vertex Indexed Array

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326172537466.png" alt="image-20230326172537466" style="zoom:67%;" /> 

- 一个思考题：打印一幅图的时间复杂度。以及一些特殊情况下复杂度的简化。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261642243.png" alt="image-20220226164248812" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261643749.png" alt="image-20220226164318149" style="zoom:67%;" />

### Runtime Analysis

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261644659.png" alt="image-20220226164427183" style="zoom:67%;" />

### Implementation

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326172847382.png" alt="image-20230326172847382" style="zoom:67%;" />



## Graph Traversal Implementation And Runtime Analysis

- 图的算法的通常的设计模式不是在`Graph`类里面实现各种方法，而是`Graph`类里只有存粹的呈现`Graph`有关的东西。
  - 将类型与算法解耦。另起一个类，让这个类来负责图的有关算法的进行（这个类就是伟大的图中全能神）。
  - 创建一个图对象，并传给全能神，向全能神索引图的信息。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281525829.png" alt="image-20220228152548630" style="zoom:67%;" />

- 深度优先的实现：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281537316.png" alt="image-20220228153740160" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281537105.png" alt="image-20220228153754963" style="zoom:67%;" />

- Runtime Analysis
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281538023.png" alt="image-20220228153845881" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281539199.png" alt="image-20220228153904061" style="zoom:67%;" />



- 广度优先的实现：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20230326174137998.png" alt="image-20230326174137998" style="zoom:67%;" />

- 复杂度分析：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281540645.png" alt="image-20220228154028498" style="zoom:67%;" />

## Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281541727.png" alt="image-20220228154144560" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281542875.png" alt="image-20220228154215738" style="zoom:67%;" />

# Lecture 23 Shortest Paths

- [DFSDemo](https://docs.google.com/presentation/d/1lTo8LZUGi3XQ1VlOmBUF9KkJTW_JWsw_DOPq8VBiI3A/edit?usp=sharing)
- [BFSDemo](https://docs.google.com/presentation/d/1JoYCelH4YE6IkSMq_LfTJMzJ00WxDj7rEa49gYmAtc4/edit?usp=sharing)

![image-20220228155517766](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281555912.png)

- 深度优先和广度优先，哪一个更好？
  - 两种都适用于所有的无权图。
  - 使用广度优先搜索的情况下，不仅得到了路径，并且得到的边的数目是最短的。
  - 二者的时间效率相差不大。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281601658.png" style="zoom: 80%;" />
  - 空间效率上：
    - DFS针对细长的图时差一点。因为递归的原因，调用栈会非常的深。
    - DFS针对一个节点有巨大数量的邻接点时不太行。队列会变得非常大。
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281602740.png" alt="image-20220228160216597" style="zoom: 80%;" />

- **还是想特别提醒一下，BFS得到的最短路径的最短指的是路径（边）的数目最短。**
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281636460.png" alt="image-20220228163631294" style="zoom:67%;" />



## **Dijkstra’s Algorithm**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281956448.png" alt="image-20220228195643309" style="zoom:67%;" />

- 适用于有向和无向图。用于有权图的寻找起点到所有可达点的最短路径。
- **不考虑有环图，也不考虑某一条边的权值为负。**
- **从源点到其他任意可到达点的路径的集合在图上画出来总会是一棵树(Shortest path tree,SPT)。**
  - ![image-20220228195914575](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202281959736.png)
- **最短路径所构成的树永远有 `V - 1` 条边。这是因为除了源点，其他的点都有且仅有一条从其他地方指向该点的入路径。**
  - ![image-20220228200139548](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282001696.png)

- 算法主体：
  - [demo](https://docs.google.com/presentation/d/1_bw2z1ggUkquPdhl7gwdVBoTaoJmaZdpkV6MoAgxlJc/pub?start=false&loop=false&delayms=3000)

  - 访问节点的时候进行的是距离优先访问，最先访问距离最小的节点。使用优先队列。

  - 对于当前访问的节点V，调整所有从V指出的路径。

  - ```java
    /*
    1.将所有顶点入优先队列PQ，起点的优先级（距离）设置为0，其余设置为正无穷(Integer.MAX_VALUE)
    2.从PQ移除最近的节点V
    3.对于V，他能到达的所有的顶点，调整它们到起点S的距离（也就是它们在队列中的优先级，会导致PQ重新调整其结构）
    4.回到2,直到PQ变空
    当然，这个算法要求我们对优先队列做一些订制，比如那个改变优先队列中某个顶点的优先级的方法
    **/
    ```

    

  - ![image-20220228213339591](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282133760.png)

  - 伪代码：在不存在负数权值的路径的情况下是正确的。

  - 以前写的，凑合看吧[DijkstraPathFInder.java](https://github.com/CorneliaStreet1/DataStructureProject/blob/master/src/main/java/JYQ/DijkstraUtils/DijkstraPathFInder.java)

    

  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282300560.png" alt="image-20220228230022369" style="zoom:67%;" />

  - 复杂度：

  - add:加入V个节点到优先队列，每个是log。

  - removeSmallest：要把队列排空要移除V个节点，每个是Log复杂度

  - changePriority：每次沿着一条边到达某个顶点，都可能需要改变这个顶点的优先级，总共E条边，每次调整是Log。

  - 三个加起来

  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282301413.png" alt="image-20220228230152252" style="zoom:67%;" />

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282302576.png" alt="image-20220228230225437"  />





- 对于一个终点明确的最短路径问题来说，Dijkstra能给出正确的回答，但是它的效率不够高。

  - 可以这么想：Dijkstra是以起点为中心的圆周扩散寻找最近点的，而对于终点明确的最短路径来说，只需要往靠终点近的那边就可(A*）。

  - 或者说，Dijkstra是计算出了从起点**到所有其他点的最短路径**，而对于给出明确终点的问题来说，这样太过于浪费了。

  - 这是Dijkstra：

    - ###### <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282313667.png" alt="image-20220228231312512" style="zoom: 50%;" />

- 引出A*：

  - 在访问节点时仍然是最近优先访问，只不过这个最近的定义是：**从起点到点V的距离加上点V到终点的距离最近。**(Dijkstra是起点到点V的距离)
  - 这个从点V到终点的距离由估算函数h给出：根据已有经验来估算。
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282309164.png" alt="image-20220228230950023" style="zoom:67%;" />
  - ![image-20220228230627398](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282306582.png)
  - 这是A*：http://qiao.github.io/PathFinding.js/visual/
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202282315566.png" alt="image-20220228231506427" style="zoom: 67%;" />



[不同路径算法的在线可视化网站](http://qiao.github.io/PathFinding.js/visual/)

# Lecture 24 Minimum Spanning Trees

- **最开头有一个warmup。关于如何确定图中是否有环。对第二个算法来说很重要。**

- 最小生成树：将无向有权图的所有节点都连接在一起，并且使得总权值最小的树。
  - 应用：将各个家庭使用电线连接在一起使得每一家都能通电，这时就要选择总电线最短的连接方式，这种方式就是最小生成树。
  - 无环。
  - 连通。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203012115594.png" alt="image-20220301211504422" style="zoom:67%;" />

- 最小生成树和最短路径树的对比：
  - 最小生成树没有起点一说，是一个全局的属性，而最短路径树则是与起点相关。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203012332886.png" alt="image-20220301233247743" style="zoom:67%;" />
  - 某个起点的最短路径树也可能恰恰是整个图的最小生成树。
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203012333411.png" alt="image-20220301233353288" style="zoom:67%;" />
    - 所以这提示我们一种求最小生成树的方法：我们找到这个特殊的节点，然后以这个节点作为起点求它的最短路径树，得到的就是最小生成树。**但是这种方法实际上是不可行的，反例：**
    - ![image-20220301234022037](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203012356445.png)

- Cut Property：
  - Cut：将图的节点分成两个非空集合。
  - Crossing Edge：两个断点分别在两个集合的边。
  - Cut Property：给定任意一个分割，Crossing Edge中权值最小的那一条一定是属于最小生成树。
    - 证明：![image-20220302152816988](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203021528135.png)
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203021526312.png" alt="image-20220302152616097" style="zoom:80%;" />

- 根据Cut Property引出最基本的最小生成树算法：
  - 最开始最小生成树中没有边。
  - 找到一个分割，这个分割的crossing edge都不在最小生成树中。
  - 将这个分割里的权值最小的边加入最小生成树中。
  - 重复直到最小生成树中有V - 1条边。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203021529889.png" alt="image-20220302152944755" style="zoom:80%;" />
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022108088.png" alt="image-20220302210828947" style="zoom:80%;" />
  - 其实是Cut Property的特殊情况的应用。
    - 任意取一个点作为起点（将它单独成集合，其他点成另外一个集合），选取与之相连的（也就是crossing edge）权值最小的边，这就是MST的第一条边。
    - MST的第一条边连接起来的两个节点构成一个集合，其他点构成另外一个集合，选取第二个集合的crossing edge中权值最小的，得到第二条边。
    - 本质上就是，在构造的过程中，不完整的MST的边将所有的点分成了两个集合，一个集合是已经在MST中的点，另外一个就是其他点。
    - 重复这个过程直到得到V - 1条MST边。
  - [demo](https://docs.google.com/presentation/d/1NFLbVeCuhhaZAM1z3s9zIYGGnhT4M4PWwAc-TLmCJjc/edit#slide=id.g9a60b2f52_0_0)<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022116661.png" alt="image-20220302211648499" style="zoom:80%;" />
  - 实际的实现：[demo](https://docs.google.com/presentation/d/1GPizbySYMsUhnXSXKvbqV4UhPCvrt750MiqPPgU-eCY/edit#slide=id.g9a60b2f52_0_0)
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022137899.png" alt="image-20220302213719741" style="zoom:80%;" />
  - 与最短路径算法对比：<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022143631.png" alt="image-20220302214301474" style="zoom:80%;" />
  - 伪代码：
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022143048.png" alt="image-20220302214339903" style="zoom:67%;" />
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022143034.png" alt="image-20220302214352876" style="zoom:67%;" />
  - Runtime：<img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022144305.png" alt="image-20220302214453148" style="zoom: 67%;" />



- <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022145499.png" alt="image-20220302214541372" style="zoom:67%;" />
  - 其实是Cut Property的伪装。
    - [kruskals conceptual demo](https://docs.google.com/presentation/d/1RhRSYs9Jbc335P24p7vR-6PLXZUl-1EmeDtqieL9ad8/edit?usp=sharing)
    - [kruskals realistic implementation demo](https://docs.google.com/presentation/d/1KpNiR7aLIEG9sm7HgX29nvf3yLD8_vdQEPa0ktQfuYc/edit?usp=sharing)
  - 将所有的边按权重排好序，从小到大考虑每条边。
  - 对于正在考虑的边E，如果加入E到最小生成树中不会导致树中成环，就加入E，否则抛弃E。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022155467.png" alt="image-20220302215525308" style="zoom:80%;" />
  - Runtime![image-20220302224109048](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202203022241213.png)









# Lecture 25 Range Searching and Multi-Dimensional Data



# Lecture 26 Prefix Operations and Tries

- Tries的基本思想：
  - **Tries可以是一个<String(key),Integer>的Map，Value部分帮助我们确定字符串出现了几次。也可以是一棵Tree**
  - 将字符串中的每一个字符都保存为树中的一个节点。
  - 每个节点只保存一个字符。
  - 同一个节点的字符可以被多个不同的字符串共享。

![image-20220225165052362](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251650557.png)

- 一个Tries的树的示例：
  - **将每个字符串的结尾的字符节点标位了蓝色来表示是字符的结尾。解决了下图底部提出的问题**

![image-20220225170038011](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251700154.png)

![image-20220225170156889](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251701033.png)

- Trie Map :[demo](http://www.cs.princeton.edu/courses/archive/spring15/cos226/demo/52DemoTrie.mov)

![image-20220225175347676](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251753826.png)

- Trie Implemention

  - DataIndexedCharMap< V >作为记录节点的子节点的一个Map。

  - ![image-20220225175600046](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251756182.png)
  - `isKey`，就是节点是蓝色还是白色。
  - `next`是节点的子节点的合集，包含128个单元，大部分会是`null`，这也是我们后续要解决的问题。
  - ![image-20220225180309674](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251803815.png)
  - 下图是一个关于`a、aw`两个字符串的节点的图。`next.items[w]` 保存着保存`w`的那个节点。
  - ![image-20220225180448143](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251804279.png)
  - 对上图的实现做一些改进，由于`items[]`的下标本身就是那个字符，所以可以把`ch`域去掉。
  - ![image-20220225180910076](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251809218.png)
- RunTime Analysis：
  - 都是常数时间，复杂度与关键词的个数无关。因为你永远都只需要去走那包含特定几个字符的一条路径。
    - 例如contains("Potato"):P --> o-->t--->a-->t-->o。
  - ![image-20220225181539123](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251815270.png)
- 接下来是对`next`的一些改进：
  - 使用BSTMap：内存上更有效率，时间上可能会差一点，毕竟BST是log的
  - ![image-20220225184107384](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251841529.png)
  - 使用HashMap：时间上可能稍微差一点点，毕竟是均摊的O(1)
  - ![image-20220225184120667](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202251841806.png)
- Tries的真正的特殊用处在于一些字符串的特殊运算：
  - 前缀匹配


- 一个练习：如何将tries里的所有关键字都取出：

![image-20220226150918503](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261509853.png)

- 搜索引擎中的自动补全是如何完成的：

  - 建立一个`TrieMap< Integer >`：权值决定了字符串的重要程度。
  - 调用前缀匹配函数，并返回权值最高的几个字符串。

  - ![image-20220226151117037](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261511287.png)
  - 但是这样的方法有一个致命缺陷：当输入的字符很短时与之前缀匹配的字符串很多，我们可能需要从一个很大的数据集中寻找到最优的十条。
  - ![image-20220226152656535](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261526768.png)
  - 解决方法：每个节点保存它自身的值，以及它的最佳子串的值。
  - ![image-20220226152808738](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261528982.png)
  - 甚至跟高效的tries：对于那种无分支的节点，合并起来。
  - ![image-20220226152916601](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261529847.png)

- Summary：

![image-20220226152942961](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202261529218.png)

# Lecture 27 Software Engineering I

- 什么是复杂度
  - 复杂度的定义是以人为中心的。

![image-20220213150056728](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202131501033.png)

- 复杂度的表现：
  - 代码修改膨胀：为了实现软件的某个改变，你可能需要去修改许多处代码。
  - 认知负担：为了实现改变你需要知道的东西有多少。
  - 未知的未知：你甚至不知道为了实现改变你需要知道什么。

![image-20220213154426885](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202131544147.png)

- 复杂度是慢慢积累起来的。
  - 每次引入的复杂度都不算大问题，可能只是一个特殊情况的`if`，可能只是一个`workaround`。但是会积累。
  - 在屎山将现的时候重构代码。

![image-20220213155016928](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202131550183.png)

- 短平快的编程：

![image-20220213160216911](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202131602157.png)

- 从战略全局考虑的编程：

![](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202131605834.png)

![image-20220213160623566](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202131606811.png)

![image-20220213160835138](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202131608376.png)

# Lecture 28 Reductions and Decomposition





# Lecture 29 basic sort

# Lecture 30 QuickSort

![image-20211214194533990](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202112141946846.png)

![image-20211214200731637](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202112142007858.png)

- 一些快排不适用的情况：
  - 数组已经有序，或者已经几乎有序
  - 数组中有重复元素，全部是重复元。

![image-20211214200940939](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202112142009287.png)



# Lecture 31 Software Engineering II

- 一个短视性编程的例子，就是最后一个project。
  - 很多代码是相似的，完全可以抽象成一个`helper method`
    - 比如if括号内的判断是否达到边界的后一个条件，完全可以抽象成`private boolean hasReachedTheEdge(.....)`
    - 以及if正文内的相似代码，也可以抽象成一个函数。
  - 所有的行为都发生在一个抽象程度比较低的层级，不好理解。
  - 如果找到问题想要修改就很麻烦，因为要修改多处。
  - 条件很长并且很难读懂。完全可以抽象成`helper method`
  - 很多数值是硬编码的。
  - 没有注释

![image-20220217184618540](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171846676.png)

- 修改的一些例子

![image-20220217185004249](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202171850383.png)

- 复杂度的两个来源
  - 相互依赖性（耦合度）：代码不能被独立的读、理解和修改。
  - 晦涩程度：重要信息不明显。

![image-20220217200227343](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172002465.png)

- 第二个例子：处理不同的输入方式，字符串输入还是键盘输入。

![image-20220217201001276](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172010418.png)

- 接下来是关于模块化设计

![image-20220217201337052](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172013180.png)

- 在建造程序时一个非常重要的事情是隐藏复杂度。因为人没办法一次性理解所有的复杂度。所以需要封装复杂度来保证程序员一次只考虑一部分复杂度。
- 理想的情况：系统被分解成许多模块，模块与模块直接完全独立。
  - 模块可以是一个类，一个接口，一个包，一个代码单元。
  - 但是模块不可能完全独立，因为模块与模块之前是需要通信的，至少需要方法调用。
  - 所以我们的目标是最小化模块之间的依赖关系。
  - ![image-20220217201943605](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172019730.png)

![image-20220217202358671](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172023793.png)



![image-20220217202409071](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172024200.png)

![image-20220217202420994](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172024117.png)

- 尽可能的隐藏信息，避免信息泄漏。
  - 所谓信息泄漏就是类似的代码出现在许多模块中，于是修改时就要修改许多处。

![image-20220217202845788](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172028908.png)

![image-20220217202944602](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172029730.png)

- 避免**TemporalDecomposition**:
  - In temporal decomposition, the structure of your system reflects the order in which events occur.

![image-20220217203547154](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172035294.png)

![image-20220217203537154](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202172035285.png)

# Lecture 33 Software Engineering III

- 很强的人文关怀，我永远喜欢UCBerkeley

# Lecture 35 Radix Sort

- 一种非常离谱的排序方法
  - SleepSort()

![image-20211228134628928](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/image-20211228134628928.png)

- Runtime Analysis for CountingSort

![image-20211228140455908](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/image-20211228140455908.png)

# Lecture 37 Software Engineering IV

- 飘着听完的，讲了一些CS 61 B的进化史，一些课程评价。没什么知识性的东西

# Lecture 38 Compression

- 这堂课讲的是关于压缩的，比如文件压缩。
- 先建立了一个压缩模型：
  - 给定一串比特串，经过一个压缩算法得到压缩后的比特串。
  - 压缩后的比特串经过解压缩算法得到原始的比特串。
  - 我们假定这个过程信息是无损的。
    - 即便这种情况下文本文件也可以压缩百分之七十。

![image-20220218212212647](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202182122808.png)

- 优化编码的方式：
  - 使用更短的编码。
  - 当然，要编码和符号对应。

![image-20220218220236627](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202182202773.png)

- 摩斯码是一种编码的方式，但是它的问题是，某一个符号的编码可能是另外一个符号的编码的前缀。
  - 这样会导致我们收到的压缩后的信息解压后存在歧义。
  - 实践中不存在，因为可以进行人为停顿。
  - 但是电脑中没办法这样作弊，所以我们需要无前缀编码。
    - 也就是任意一个字符的编码都不可能是另外一个字符的前缀。
  - 摩斯码的编码树体现了这一点：

![image-20220218220627906](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202182206057.png)

- 如果把编码写成树的形式，所有的字符都是处于叶子节点上，那么这个编码应该就是一个无前缀编码。
- ![image-20220218220911475](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202182209619.png)
- ![image-20220218220849813](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202182208954.png)

- 对于无前缀编码又引入了一个设计问题:
  - 某些编码设计针对某些文本，可能比其他的编码设计要好。
    - 比如EEEAT，左边的编码方式比右边好。
    - 而对于JOSH，右边的编码方式比左边好。
  - 所以我们可能需要找到一个流程，这个流程能够找到给定字符串的最佳编码方式。

![image-20220218223943776](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202182239918.png)

- `shannon fano code`：
  - 三个节点分开时，权重最大的那一个节点单独作为一半。
  - **但是香农凡诺编码并不是最优的，它能用，但是可以找到更好的。**
  - 更好的是`huffman coding`。

![image-20220219121422416](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191214566.png)

![image-20220219121348483](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191213669.png)

![image-20220219121402912](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191214061.png)

- 霍夫曼编码

![image-20220219153911510](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191539673.png)

![image-20220219153929962](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191539101.png)

- 关于霍夫曼编码的效率与Unicode的对比。

  - ###### 使用霍夫曼编码大概是14倍的效率

![image-20220219154026399](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191540539.png)

- 关于霍夫曼的编码与解码：
  - 编码：使用从符号到编码的Map来映射，或者使用符号(的ASCII，etc)作为索引的编码的数组。
  - ![image-20220219154840490](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191548638.png)
  - 解码：使用前缀树，try。解码时寻找最长的匹配前缀。
  - ![image-20220219154937591](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191549727.png)

- 有关于霍夫曼编码的实践问题：
  - 对于霍夫曼编码，有两种使用霍夫曼压缩的方法。
    - 第一种，对不管什么类型的输入，不管什么文件，英文也好中文也好，每一类建立一个标准的编码，英语一类，中文一类。
    - 第二种，仅针对输入的文件，对于每一种可能的输入文件，创建一个只适用于它的独一无二的编码。将编码和已压缩的文件
  - 两种编码方法的优缺点：
    - 第一种：我们无法得到最佳编码，因为不同的文件可能相同的字符所占的权重并不是一样的，而这种情况可能才是最常见的。所以强行忽略不同输入文件中相同字符的权重不同来建立一个统一的编码库，无法得到最佳编码，也即无法极限压缩。
      - 举个极端一点的例子，可能某一个文件全是字符A，另外一个文件全是字符B，我们给这俩字符都恰好使用4位的编码。但是实际上只针对这一个文件，一位的压缩编码就是足够的。
    - 第二种：我们会需要额外的空间，因为要把编码表也发送过去。
    - **实践采用的往往是第二种，因为很多时候，包含编码表的花费对于整个文件来说是微不足道的。**
      - 举个例子，有一个很大的英文文本，我们针对它使用了第二种方法，压缩之后减少了10兆，但是这个编码表本身可能也就KB大小。

- 霍夫曼编码和解码实例：
  - 编码：[demo](https://docs.google.com/presentation/d/1DWuSkE9MxQPUTjbSJCMe54rCim4eAwM4aFRvhqq5_Hs/edit#slide=id.g2159afc5e6_0_1068)
    - 1.计算各个符号的权重。
    - 2.建立编码数组和阶码前缀树
    - 3.将阶码前缀树写入输出文件
    - 4.将编码也写入输出文件。
  - ![image-20220219170512286](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191705456.png)
  - 解码：[demo](https://docs.google.com/presentation/d/1DWuSkE9MxQPUTjbSJCMe54rCim4eAwM4aFRvhqq5_Hs/edit#slide=id.g2159afc5e6_0_1068)
    - 1.读入解码前缀树。
    - 2.根据前缀树使用最大匹配前缀得到相应符号。
  - ![image-20220219170731362](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191707526.png)

- Summary：[Huffman.java](http://algs4.cs.princeton.edu/55compression/Huffman.java)
- ![image-20220219171012200](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191710340.png)

- Compression Theory:

  - 世界上除了霍夫曼还有许多种其他的压缩方式。
  - Run Length Encoding：行程长度压缩法，即根据字符串的连续重复字符进行编码的一种方法。
    - 处理处理连续重复字符串的效果较佳，最差的情况就是没有连续的字符，这样的话除了没有压缩不算，而且还增加了字符串的长度

  ![image-20220219183017649](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191830817.png)

- 关于压缩模型的改进：将解压的代码也加入到最终的输出文件中去。

![image-20220219183901706](https://raw.githubusercontent.com/CorneliaStreet1/PictureBed/master/202202191839859.png)

# Lecture 39 Compression, Complexity, and P=NP?

- 太难啦！！！
