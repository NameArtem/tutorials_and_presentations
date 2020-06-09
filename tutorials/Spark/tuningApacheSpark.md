Расчет памяти на каждом слоев в SPARK:

- shuffle memory = spark.memory.fraction * (spark.executor.memory - 300 Mb)

- user memory = (1 - spark.memory.fraction) * (spark.exeutor.memory - 300 Mb)

- memoryBuffer = spark.yarn.executor.memoryOverhead = 0.1 * spark.executor.memory

- reserved memory ~= 300 mb


Расчет мапперов и редюсеров (евристика):

- mapper = max(256 mb, inputTableSize / 50000)

- reducers = max(200, min(10000, max(inputTableSize / 256 mb * 0.125, 200)))


1. Dynamic Executor allocation

Способствуют лучшему утилизации ресурсов

```scala
spark.dynamicAllocation.enabled = true
spark.dynamicAllocation.executorIdleTimeout = 2m
// настраиваемое количество
spark.dynamicAllocation.minExecutors = 1
spark.dynamicAllocation.maxExecutors = 2000
```


2. Multi-threaded event processor (find JIRA SPARK-18838)

3. Better fetch failure handling (find JIRA SPARK-19753, 20163, 13369, 20178)

```scala
spark.max.fetch.failure.per.stage = 10
```

4. Tune RPC Server threads

```scala
//увеличим количество RPC (fix OOM)
spark.rpc.io.serverThreads = 64
```

5. Tuning memory (don't forget use garbage collector)

```scala
//shuffle memory
spark.memory.offHeap.enabled = true
spark.memory.offHeap.size = 3g

// user memory
spark.executor.memory = 3g

// memory buffer
spark.yarn.executor.memroyOverhead = 0.1 * (spark.executor.memroy + spark.memory.offHeap.size)
```

Используем параллельный сборщик мусора, а не G1GC

```scala
//G1GC1 => Parallel GC
spark.executor.extraJavaOptions = -XX:ParallelGCThreads=4 -XX:+UseParallelGC
```


6. I/O bottelneck (find JIRA SPARK-20074)

```scala
spark.shullfe.file.buffer = 1 Mb
spark.usafe.sorter.split.reader.buffer.size = 1 Mb

// spark-20014
spark.file.transferTo = false
spark.shuffl.unsafe.file.output.buffer = 5 Mb

//compression
spark.io.compression.lz4.blockSize = 512 Kb
```

7. User shuffle server (find JIRA SPARK-15074, 20640)

```scala
spark.shullfe.service.index.cache.entries = 2048

// thread and backlog
spark.shuffle.io.serverThreads = 128
spark.shuffle.io.backlog = 8192

//timeout and retry
spark.shuffle.registration.timeout = 2m
spark.shuffle.registration.maxAttepts = 5
```
