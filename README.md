<!--
## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/taoranli/taoranli.github.io-cs239/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/taoranli/taoranli.github.io-cs239/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact
Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
-->

## AssociationRules

### Spark Environment
Check out Spark [documentation](https://spark.apache.org/docs/latest/)



#### Step One - Download all necessary files

For Java, [http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

For Scala, [https://www.scala-lang.org/download/2.11.8.html](https://www.scala-lang.org/download/2.11.8.html ) 

For sbt, [http://www.scala-sbt.org/download.html](http://www.scala-sbt.org/download.html)(choose all platforms)

For Spark, [http://spark.apache.org/downloads.html](http://spark.apache.org/downloads.html)

For Java, install it by following the instruction. For other three, unzip them to a file. Notice that the version you choose sometimes depend on the need of the whole team. Here we choose latest spark, java 8 and scala 2.11.8.

#### Step Two - Edit environmental variables

Add Java and Scala’s home directory to system to let Spark use. Then add sbt’s directory to use it in terminal. In terminal, type 
```
vim ~/.bashrc
```

Then type:

```
export JAVA_HOME=$(/usr/libexec/java_home)

export SCALA_HOME=/Users/yuan/Desktop/course/scala-2.11.8

export SBT_HOME=/Users/yuan/Desktop/course/sbt-launcher-packaging-0.13.13

export PATH=$PATH:$SCALA_HOME/bin:$SBT_HOME/bin
```

Quit and type in terminal:
```
source ~/.bashrc
```

Now you can check whether you are in the right way by typing: echo $JAVA_HOME, and sbt version. If you have do right, it will appear some information. For example,

![Image of Spark build](https://github.com/taoranli/taoranli.github.io-cs239/raw/master/images/spark_build.png)

#### Step Three - Create a Spark application
Since Spark 1.5.0, spark.mllib has provided a parallel implementation of FP-growth, a popular algorithm to mining frequent itemsets. Thus we can use this API to do frequent itemsets mining and rule association generation.

In Spark, we can use a class called FPGrowth. This class has three method, they are:

```
def setMinSupport(minSupport: Double): FPGrowth.this.type   //Set min support

def setNumPartitions(numPartitions: Int): FPGrowth.this.type   //Set the number of partitions used to distribute the work by parallel FP-growth (default: same as input data)

def run[Item](data: RDD[Array[Item]]): FPGrowthModel[Item]    //Do training
```
After training, we get a class called FPGrowthModel, it has one instance and one method:
```
val freqItemsets: RDD[FreqItemset[Item]]   //To get frequent itemsets

def generateAssociationRules(confidence: Double): RDD[Rule[Item]]   //Generates association rules for the Items in freqItemsets
```
Use all API above we can write a simple program to use Spark to do data mining. Create a SimpleApp.scala file and type:
```
import org.apache.spark.SparkContext
import org.apache.spark.SparkContext._
import org.apache.spark.SparkConf
import org.apache.spark.mllib.fpm.FPGrowth
import org.apache.spark.rdd.RDD

object SimpleApp {
  def main(args: Array[String]) {

    //initialize spark
    val conf = new SparkConf().setAppName("Simple Application")
    val sc = new SparkContext(conf)
    
    //load input file
    val data = sc.textFile(args(0))
    val transactions: RDD[Array[String]] = data.map(s => s.trim.split(' '))

    //run FPGrowth to find freuqent itemset
    val fpg = new FPGrowth()
      .setMinSupport(0.5)
      .setNumPartitions(10)
    val model = fpg.run(transactions)

    model.freqItemsets.collect().foreach { itemset =>
      println(itemset.items.mkString("[", ",", "]") + ", " + itemset.freq)
    }

    //use frequent itemset to find association rules(only imple one more item)
    val minConfidence = 0.5
    model.generateAssociationRules(minConfidence).collect().foreach { rule =>
      println(
        rule.antecedent.mkString("[", ",", "]")
          + " => " + rule.consequent .mkString("[", ",", "]")
          + ", " + rule.confidence)
    }

    sc.stop()
  }
}
```

Notice that this API can only construct rules that have a **single** item as the consequent.

#### Step Four - Run application on terminal
Before running application, we need to use sbt to make the application into a .jar file. Then we can submit it to spark to run. To use sat, we need create a built.sbt file, and add:
```
name := "Simple Project"

version := "1.0"

scalaVersion := "2.11.7"

libraryDependencies ++= Seq("org.apache.spark" %% "spark-core" % "2.1.0", 
"org.apache.spark"  %% "spark-mllib" % "2.1.0")
```

Then organize your .sbt file and .scala file like this:

![Image of structure](https://github.com/taoranli/taoranli.github.io-cs239/raw/master/images/folder_structure.png)

Then in current folder, in terminal type:

```
sbt package
```
If everything goes right, you can see Success in terminal.
![Image of package](https://github.com/taoranli/taoranli.github.io-cs239/raw/master/images/package.png)

Finally, run the application jar file in terminal like this:

```
/Users/yuan/Desktop/course/spark-2.1.0-bin-hadoop2.7/bin/spark-submit \
--class "SimpleApp" \
target/scala-2.11/simple-project_2.11-1.0.jar input.txt
```

You can also use a script to avoid such a long command every time.

Our test dataset is:
```
  a c d
  b c e
  a b c e
  b e
 ```
 
Result of frequent items from Spark is:

![Image of structure](https://github.com/taoranli/taoranli.github.io-cs239/raw/master/images/frequent_item_result.png)

Result of association rule from Spark is:

![Image of structure](https://github.com/taoranli/taoranli.github.io-cs239/raw/master/images/association_rule_result.png)

#### Step Five - Run application on IDE

#### Step Six - More about Spark

In the previous part, we use only one single machine to run Spark application, which is called Standalone mode. If we want to learn the power of Spark and parallel algorithm, we need to set up a cluster. To learning how to set up a Spark cluster, you can visit:
[http://blog.leanote.com/post/gabriellelc/Guide_For_SparkHadoop](http://blog.leanote.com/post/gabriellelc/Guide_For_SparkHadoop)

#### References

[Spark documentation](https://spark.apache.org/docs/latest/)
[Association rule learning](https://en.wikipedia.org/wiki/Association_rule_learning#FP-growth_algorithm)

