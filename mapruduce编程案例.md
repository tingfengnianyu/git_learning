

# 1.在linux虚拟机中直接执行java代码



在Linux虚拟机中直接执行Java代码，你可以按照以下步骤进行操作：

### 1. 安装Java

首先，请确保你的Linux虚拟机上已经安装了Java。如果未安装，可以通过以下命令安装Java Development Kit (JDK)。对于大多数基于Debian的系统（如Ubuntu），可以使用以下命令：

```
sudo apt update  
sudo apt install default-jdk  
```

对于基于Red Hat的系统（如CentOS），可以使用：

```
sudo yum install java-11-openjdk-devel  
```

### 2. 编写Java代码

可以使用任何文本编辑器创建一个Java文件。例如，使用`nano`或`vim`：

```
nano HelloWorld.java  
```

在文件中输入以下Java代码：

```
public class HelloWorld {  
    public static void main(String[] args) {  
        System.out.println("Hello, World!");  
    }  
}  
```

保存并退出编辑器（在`nano`中，可以按`CTRL + X`，然后按`Y`确认保存）。

### 3. 编译Java代码

在终端中，使用`javac`命令编译Java代码：

```
javac HelloWorld.java  
```

如果没有错误，执行后会生成一个`HelloWorld.class`文件。

### 4. 执行Java程序

使用`java`命令来执行编译好的Java程序：

```
java HelloWorld  
```

如果一切正常，你会看到输出：

```
Hello, World!  
```

### 补充：直接执行Java代码

如果你想直接执行Java代码而不先创建文件，可以使用以下方法：

1. **使用`java -e`**（如果你的Java版本支持）：

```
java -e 'public class Main { public static void main(String[] args) { System.out.println("Hello, World!"); } }'  
```

1. **使用一个在线的Java REPL**，像是 `jshell`（Java 9及以上版本内置）：

```
jshell  
```

在 `jshell` 提示符下执行：

```
System.out.println("Hello, World!");  
```

这是一种更方便的方式来测试小段Java代码。

通过上述步骤，你就可以在Linux虚拟机中直接运行Java代码。













# 2. 在linux虚拟机里运行的mapreduce的java编程案例

下面是一个简单的MapReduce案例，用于计算文本文件中每个单词的出现次数。我们将使用Hadoop框架来实现这个MapReduce程序。

### 前提条件

1. **安装Hadoop**：请确保你已经在Linux虚拟机上安装并配置了Hadoop。
2. **设置Hadoop环境**：确保你已经配置了Hadoop的环境变量，并且能在命令行中运行`hadoop`命令。

### 1. 创建Java MapReduce程序

首先，使用文本编辑器创建一个Java文件。例如，名为`WordCount.java`：

```shell
vim WordCount.java  
```

在文件中输入以下代码：

```java
import org.apache.hadoop.conf.Configuration;  
import org.apache.hadoop.fs.Path;  
import org.apache.hadoop.io.IntWritable;  
import org.apache.hadoop.io.Text;  
import org.apache.hadoop.mapreduce.Job;  
import org.apache.hadoop.mapreduce.Mapper;  
import org.apache.hadoop.mapreduce.Reducer;  
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;  
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;  

import java.io.IOException;  

public class WordCount {  

    // Mapper 类，负责将输入数据转换为键值对  
    public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {  
        // 用于输出的中间值，1表示单词的出现次数  
        private final static IntWritable one = new IntWritable(1);  
        // 用于存储每个单词  
        private Text word = new Text();  

        // map 方法，处理输入的每一行数据  
        public void map(Object key, Text value, Context context) throws IOException, InterruptedException {  
            // 将输入的文本行拆分成单词  
            String[] words = value.toString().split("\\s+");  
            // 对每个单词进行处理  
            for (String w : words) {  
                word.set(w); // 设置当前单词  
                context.write(word, one); // 输出当前单词和出现次数1  
            }  
        }  
    }  

    // Reducer 类，负责将来自 Mapper 的输出进行聚合  
    public static class IntSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {  
        // 用于存储每个单词的总出现次数  
        private IntWritable result = new IntWritable();  

        // reduce 方法，处理每个单词的所有值  
        public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {  
            int sum = 0; // 初始化总和  
            // 对每个出现的值进行累加  
            for (IntWritable val : values) {  
                sum += val.get(); // 获取当前值并累加  
            }  
            result.set(sum); // 设置当前单词的总出现次数  
            context.write(key, result); // 输出单词及其总出现次数  
        }  
    }  

    // main 方法，程序的入口  
    public static void main(String[] args) throws Exception {  
        // 创建一个配置对象  
        Configuration conf = new Configuration();  
        // 创建一个作业对象，并设置作业的名称  
        Job job = Job.getInstance(conf, "word count");  
        // 指定主类  
        job.setJarByClass(WordCount.class);  
        // 设置Mapper类  
        job.setMapperClass(TokenizerMapper.class);  
        // 设置Combiner类（可选），通常和Reducer类一样  
        job.setCombinerClass(IntSumReducer.class);  
        // 设置Reducer类  
        job.setReducerClass(IntSumReducer.class);  
        // 设置输出的键和值的类型  
        job.setOutputKeyClass(Text.class);  
        job.setOutputValueClass(IntWritable.class);  
        // 添加输入路径（HDFS中的路径）  
        FileInputFormat.addInputPath(job, new Path(args[0]));  
        // 设置输出路径（HDFS中的路径）  
        FileOutputFormat.setOutputPath(job, new Path(args[1]));  
        // 提交作业并等待完成  
        System.exit(job.waitForCompletion(true) ? 0 : 1);  
    }  
}  
```

### 2. 编译Java代码

确保你的Hadoop环境已配置，并且`HADOOP_HOME`和`JAVA_HOME`环境变量已设定。然后编译代码：

```shell
javac -classpath "$(hadoop classpath)" -d . WordCount.java  
```

### 3. 创建JAR文件

创建一个JAR文件以便运行：

```shell
jar cvf WordCount.jar -C . .  
```

### 4. 准备输入数据

创建一个文本文件，作为输入数据。例如，创建一个名为`input.txt`的文件：

```shell
echo "Hello Hadoop Hello MapReduce" > input.txt  
```

然后将其放入Hadoop的输入目录。假设输入目录为`/user/hadoop/input`：

```shell
hdfs dfs -mkdir -p /user/hadoop/input  
hdfs dfs -put input.txt /user/hadoop/input/  
```

### 5. 启动yarn和hdfs

```shell
start-dfs.sh
start-yarn.sh
```

### 6. 运行MapReduce程序

使用Hadoop命令运行你的MapReduce程序：

```shell
hadoop jar WordCount.jar WordCount /user/hadoop/input /user/hadoop/output  
```

### 7. 查看输出结果

执行完成后，你可以检查输出结果：

```shell
hdfs dfs -ls /user/hadoop/output  
hdfs dfs -cat /user/hadoop/output/part-r-00000  
```

你应该看到单词及其出现次数的输出结果。

![2163e2ae4e7cbebb99279199bfcd867](C:\Users\lq\Documents\WeChat Files\wxid_zpoo8ma6nwmy22\FileStorage\Temp\2163e2ae4e7cbebb99279199bfcd867.png)

### 代码结构说明

- **TokenizerMapper**:
  - 这个类扩展了`Mapper`类，实现了文本到键值对的映射。它的主要任务是读取输入文本，将其分词并输出每个单词（键）及其出现次数（值）。
- **IntSumReducer**:
  - 这个类扩展了`Reducer`类，用于聚合来自多个Mapper输出的值。它将相同单词的出现次数累加，并输出每个单词及其总出现次数。
- **main 方法**:
  - 这是程序的入口点，设置Job配置，指定Mapper和Reducer，指定输入和输出路径，并提交作业。







# 报错解决

## bug1

1. ![b6e234f2bbaf6a22da89edd576e55fd](C:\Users\lq\Documents\WeChat Files\wxid_zpoo8ma6nwmy22\FileStorage\Temp\b6e234f2bbaf6a22da89edd576e55fd.png)

这些错误通常表明在编译时找不到所引用的类和方法。这通常是因为缺少必要的库或包。确保已正确配置Hadoop，并在编译时包含所有所需的Hadoop库。

### 解决步骤

1. **确认Hadoop环境变量**：
   确保已经正确设置了`HADOOP_HOME`环境变量，并且添加了Hadoop的`bin`目录到`PATH`中。你可以通过以下命令查看环境变量：

   ```shell
   echo $HADOOP_HOME  
   echo $PATH  
   ```

2. **检查Hadoop版本**：
   确保Hadoop已正确安装并且可以正常运行。可以通过以下命令检查Hadoop版本：

   ```shell
   hadoop version  
   ```

3. **编译时包含Hadoop类路径**：
   在编译Java代码时，你需要确保包含Hadoop的类路径。以下是典型的编译命令：

   ```shell
   javac -classpath "$(hadoop classpath)" -d . WordCount.java  
   ```

   这个命令会自动获取Hadoop的类路径，然后编译你的Java文件。

4. **检查Classpath**：
   确认`hadoop classpath`命令返回的类路径中是否包含了必需的类（如`Job`, `Text`, `IntWritable`, `FileInputFormat`, `FileOutputFormat`等）。如果没有，可能是Hadoop没有正确安装。

5. **检查Hadoop依赖**：
   在一些特定版本的Hadoop中，某些Jar包可能不在`hadoop classpath`中。你可以手动添加这些依赖，例如在IDE中，或者在编译时手动指定路径。

6. **使用IDE编译**：
   如果手动编译仍然存在问题，可以考虑使用IDE（如Eclipse或IntelliJ IDEA），它们能够更轻松地管理依赖项并提供更好的错误提示。



**hadoop classpath命令返回的类路径中没有包含了必需的类（如Job, Text, IntWritable, FileInputFormat, FileOutputFormat等）解决方法**

如果 `hdoop classpath` 命令返回的类路径中没有包含 `Job`, `Text`, `IntWritable`, `FileInputFormat`, `FileOutputFormat` 等必需的类，可能是由于以下几个原因。下面是一些解决方案：

### 解决步骤

1. **确认Hadoop安装**：
   确保你的Hadoop安装是完整且正确的。可以通过运行以下命令来检查Hadoop版本，以确保可以正常运行：

   ```
   hadoop version  
   ```

   如果运行该命令没有任何错误，说明Hadoop已经安装。

2. **检查Hadoop的依赖库**：
   如果 `hadoop classpath` 返回的类路径中缺少必需的类，首先要确认 `HADOOP_CLASSPATH` 和 `HADOOP_HOME` 环境变量是否设置正确：

   ```
   echo $HADOOP_HOME  
   echo $HADOOP_CLASSPATH  
   ```

   如果没有设置，添加以下行到你的 `~/.bashrc` 或 `~/.bash_profile` 文件中：

   ```
   export HADOOP_HOME=/path/to/hadoop  
   export PATH=$PATH:$HADOOP_HOME/bin  
   HADOOP_CLASSPATH=$HADOOP_HOME/share/hadoop/common/*:$HADOOP_HOME/share/hadoop/common/lib/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/hdfs/lib/*:$HADOOP_HOME/share/hadoop/mapreduce/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*  
   ```

   更改路径 `/path/to/hadoop` 为你的实际Hadoop安装路径。然后，运行 `source ~/.bashrc` 或 `source ~/.bash_profile` 来使改动生效。

3. **检查Hadoop安装的完整性**：
   确保你的Hadoop安装包含了所有的jar包。可以通过以下命令来检查Hadoop的安装路径下是否存在相应的jar文件：

   ```
   ls $HADOOP_HOME/share/hadoop/common/  
   ls $HADOOP_HOME/share/hadoop/hdfs/  
   ls $HADOOP_HOME/share/hadoop/mapreduce/  
   ```

   确保你能看到 `hadoop-common*.jar`, `hadoop-hdfs*.jar`, `hadoop-mapreduce-client-core*.jar` 等文件。

4. **重新下载和安装Hadoop**：
   如果确认缺少jar包，可以尝试重新下载并安装Hadoop。可以从Apache Hadoop的官方网站获取最新版本：
   [Apache Hadoop Releases](https://hadoop.apache.org/releases.html)

## bug2

![810b626c4581309d77bf1ef173dc793](C:\Users\lq\Documents\WeChat Files\wxid_zpoo8ma6nwmy22\FileStorage\Temp\810b626c4581309d77bf1ef173dc793.png)

从你提供的日志来看，Hadoop 在尝试连接到 ResourceManager 时发生了连接拒绝（Connection Refused）异常。这通常表示 ResourceManager 没有运行，或者网络连接存在问题。以下是一些可能的解决方案：

### 1. 检查 ResourceManager 进程是否在运行

首先，确保 ResourceManager 正在运行。可以使用以下命令查看资源管理器进程是否正在运行：

```shell
jps  
```

你应该能看到类似以下的输出，其中包含 `ResourceManager`：

```shell
1234 ResourceManager  
5678 NodeManager  
```

如果没有看到 `ResourceManager`，请尝试启动 HDFS 和 YARN：

```shell
start-dfs.sh  
start-yarn.sh  
```

![f6f2fab8e415b25579001ac481cc4a9](C:\Users\lq\Documents\WeChat Files\wxid_zpoo8ma6nwmy22\FileStorage\Temp\f6f2fab8e415b25579001ac481cc4a9.png)

**bug3**

3. ![7ad1e11b31848f16de8f298f9007499](C:\Users\lq\Documents\WeChat Files\wxid_zpoo8ma6nwmy22\FileStorage\Temp\7ad1e11b31848f16de8f298f9007499.png) 

从你提供的错误信息来看，问题是由于 YARN 队列的配置不当导致的。具体来说，错误提示“root is not a leaf queue”表明你试图将作业提交到一个非叶子队列（即父队列），而 YARN 只允许作业提交到叶子队列。**解决此问题的建议步骤：**

[【root is not a leaf queue】 org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit appli..-CSDN博客](https://blog.csdn.net/weixin_43412762/article/details/129994276?ops_request_misc=%7B%22request%5Fid%22%3A%22C44453D3-9F09-4D3B-AA06-9F73D48D3A4E%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=C44453D3-9F09-4D3B-AA06-9F73D48D3A4E&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-129994276-null-null.142^v100^pc_search_result_base9&utm_term=root is not a leaf queue&spm=1018.2226.3001.4187)