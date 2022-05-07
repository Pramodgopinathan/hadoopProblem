# Use Case Solution with Hadoop Map-Reduce Problem
Data set Description:

This data set is SAT (College Board) 2010 School Level Results which gives you the information about how the students perform in the tests from different schools.  It consists of the below fields.

DBN, School Name, Number of Test Takers, Critical Reading Mean, Mathematics Mean, Writing Mean

Here DBN will be the unique field for this dataset. The students were given a test. Based on the results from the test.

Problem Statement:

    Find the total number of test takers.

    Find the highest mean/average of the Critical Reading section and the school name.

    Find the highest mean/average of the Mathematics section and the school name

    Find the highest mean/average of the Writing section and the school name

 Note: Records with fewer than 5 students can be ignored
 
 
 # Dataset 
 Analyzing the SAT (College Board) 2010 School Level Results, it is copied in the repository.
 
 # Create a new directory with name 
 ![](https://github.com/Pramodgopinathan/hadoopProblem/blob/main/CreatingDirectory.png)
 
 # Give permission to folder which was created
 ![](https://github.com/Pramodgopinathan/hadoopProblem/blob/main/Permission.png)
 
 # Create Java Script to address above problem
 
 
```java
 
import java.io.IOException;
import java.util.Iterator;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;
 
// Mapreduce program to group and display sort/count the number of/ schools based on the number of test takers.
//If the number of test takers are <=100 the range is set to 1
//If the number of test takers are >100 and <=200, the range is set to 2 so forth etc.
 
public class CountSchoolsBasedOnTestTakers {
 
  public static class TokenizerMapper extends Mapper<Object, Text, IntWritable, Text>
  {
    
    //private final static IntWritable range = new IntWritable(1);
    private Text word = new Text();
      
    public void map(Object key, Text value, Context context) throws IOException, InterruptedException 
    {
        String[] split = value.toString().split(",");
    	word.set(split[1]); //school name
         
    	    if(split[3].matches("(.*)[a-z](.*)")|split[3].matches("(.*)[A-Z](.*)"))
		{
			split[3]="0";
		}
   
    	int testTakers = new Integer(split[3]).intValue();
    	int range = 0;
    	
        	if(testTakers <= 100)
			range = 1;
		if(testTakers > 100 && testTakers <= 200)
			range = 2;
		if(testTakers > 200 && testTakers <= 300)
			range = 3;
		if(testTakers > 300 && testTakers <= 400)
			range = 4;
		if(testTakers > 400 && testTakers <= 500)
			range = 5;
		if(testTakers > 500 && testTakers <= 600)
			range = 6;
		if(testTakers > 600 && testTakers <= 700)
			range = 7;
		if(testTakers > 700 && testTakers <= 800)
			range = 8;
		if(testTakers > 800 && testTakers <= 900)
			range = 9;
		if(testTakers > 900 && testTakers <= 1000)
			range = 10;
		if(testTakers > 1000 && testTakers <= 1100)
			range = 11;
		//count.set("" + text.length());
        context.write(new IntWritable(range), word);
 
      }
    }
  
  public static class IntSumReducer extends Reducer<IntWritable,Text,IntWritable, Text> 
  {
    private IntWritable result = new IntWritable();
    
    public void reduce(IntWritable key, Iterator<Text> values, Context context) throws IOException, InterruptedException 
    {      
     int count=0;
      String schoolNames =" ";
      
      while (values.hasNext()) 
      {
    	  String newschools=values.next().toString();
          schoolNames = schoolNames +"----->" +newschools;
          count++;
      }
      context.write(key, new Text(schoolNames));
    }
  }
 
  public static void main(String[] args) throws Exception {
	  
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: CountSchoolsBasedOnTestTakers <in> <out>");
      System.exit(2);
    }
    Job job = new Job(conf, "school count");
    job.setJarByClass(CountSchoolsBasedOnTestTakers.class);
    job.setMapperClass(TokenizerMapper.class);
    //job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(IntWritable.class);
    job.setOutputValueClass(Text.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}

```

# Export classpath

export CLASSPATH="/usr/local/hadoop/share/hadoop/common/hadoop-common-3.3.2.jar:/usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-core-3.3.2.jar"

Note: hadoop-core is now hadoop-common and mine is 3.3.2 you need to check which version you have. You could do it using 'find' command in terminal.

# Complie Java Files
