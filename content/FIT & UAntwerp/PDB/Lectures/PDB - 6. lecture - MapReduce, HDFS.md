### Exam topics
- Programming models, paradigms and languages; parallel programming models, process interaction (shared memory, message passing, implicit interaction), problem decomposition (task parallelism, data parallelism, implicit parallelism)
- MapReduce: programming model (data parallelism, divide‐and‐conquer paradigm, map and reduce functions), cluster architecture (master, workers, message passing, data distribution), map and reduce functions (input arguments, emission and reduction of intermediate key-value pairs, final output), data flow phases (mapping, shuffling, reducing), input parsing (input file, split, record), execution steps (parsing, mapping, partitioning, combining, merging, reducing), combine function (commutativity, associativity), additional functions (input reader, partition, compare, output writer), implementation details (counters, fault tolerance, stragglers, task granularity), usage patterns (aggregation, grouping, querying, sorting, …)
- Apache Hadoop: modules (Common, HDFS, YARN, MapReduce), related projects (Cassandra, HBase, …); HDFS module: data model (hierarchical namespace, directories, files, blocks, permissions), architecture (NameNode and DataNode nodes, HeartBeat messages, failures), replica placement (rackaware strategy), FsImage (namespace, mapping of blocks, system properties) and EditLog structures, FS commands (ls, mkdir, …); MapReduce module: architecture (JobTracker and TaskTracker nodes), job implementation (Configuration; Mapper, Reducer, and Combiner classes; Context, write method; Writable and WritableComparable interfaces), job execution schema
***
### What's a programming model?
- programming model = abstraction of an underlying computer system
	- implementation details are hidden, public interface is exposed (= how the system expects us to behave and control it, we can create algorithms and data structures (within defined bounds))
- parallel programming models
	- there are different ways how to share data in parallel processes:
		- shared memory between parallel processes, but this applies only on single machine (cannot be in the cluster)
		- message passing (in clusters)
	- types of parallelisms in programming models:
		- task parallelism - different tasks are done in parallel over the same data
		- data parallelism - the same task over different data
- don't confuse with:
	- programming paradigms: procedural, logic, modular, functional, OOP, recursive, ...
	- programming languages: Java, C++, ...
### MapReduce
- [[MapReduce]]
	- process interaction: message passing
	- problem decomposition: data parallelism
- [[Hadoop]]
### Tutorial
- Combiner classes are for the "preprocessing" on the Mapper nodes
	- I don't have to use it, but if I use it, it has to have the correct properties (in the lecture)
	- why takes the Combiner class the Reduce part as an argument
	- do we have to use it?
```java
// for emitting pair from the Mapper to intermediate key-value pairs
context.write(  
        key,  
        new IntWritable(sum)  
);
```

- Examples of Map and Reduce functions in Java
	- `context.write(KEY, VALUE)`
```java
public class MapReduce {
    
    public static class MyMapper
         extends Mapper<Object, Text, Text, IntWritable>
    {
        
        @Override
        public void map(
            Object key,
            Text value,
            Context context
        ) throws IOException, InterruptedException {
            
            StringTokenizer i = new StringTokenizer(value.toString());
            while (i.hasMoreTokens()) {
                String word = i.nextToken();
                context.write(
                    new Text(word),
                    new IntWritable(1)
                );
            }
            
        }
        
    }
    
    public static class MyReducer
        extends Reducer<Text, IntWritable, Text, IntWritable>
    {
        
        @Override
        public void reduce(
            Text key,
            Iterable<IntWritable> values,
            Context context
        ) throws IOException, InterruptedException {
            
            int sum = 0;
            for (IntWritable v : values) {
                sum += v.get();
            }
            context.write(
                key,
                new IntWritable(sum)
            );
            
        }
        
    }
    
    public static void main(String[] args) throws Exception {
        
        Configuration conf = new Configuration();
        
        Job job = Job.getInstance(conf, "WordCount");
        
        job.setJarByClass(WordCount.class);
        job.setMapperClass(MyMapper.class);
        job.setCombinerClass(MyReducer.class);
        job.setReducerClass(MyReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        
    }
    
}
```
