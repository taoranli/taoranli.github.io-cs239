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

### Library Import
```scala
import org.apache.spark.rdd.RDD
import org.apache.spark.mllib.fpm.AssociationRules
import org.apache.spark.mllib.fpm.FPGrowth.FreqItemset

val freqItemsets = sc.parallelize(Seq(
  new FreqItemset(Array("a"), 15L),
  new FreqItemset(Array("b"), 35L),
  new FreqItemset(Array("a", "b"), 12L)
));

val ar = new AssociationRules()
  .setMinConfidence(0.8)
val results = ar.run(freqItemsets)

results.collect().foreach { rule =>
  println("[" + rule.antecedent.mkString(",")
    + "=>"
    + rule.consequent.mkString(",") + "]," + rule.confidence)
}
```

### Demo results

![Image of Yaktocat](https://octodex.github.com/images/yaktocat.png) 


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

Now you can check whether you are in the right way by typing: echo $JAVA_HOME, and sbt version. If you have do right, it will appear some information. For example,![Image of Spark build](https://github.com/taoranli/taoranli.github.io-cs239/raw/master/images/spark_build.png)

test!

![Image of Yaktocat](../images/spark_build.png) 


