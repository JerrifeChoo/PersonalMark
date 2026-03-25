优势：<br />
1、数据连续性，提升Cache命中率<br />
2、Burst，将C#代码优化为SIMD机器码，利用CPU向量指令，批处理<br />
3、Job，多线程并行处理，充分利用多核优势<br />

Archetype<br />
Archetype相同的组件组合会顺序存储，保存有Component的地址，Query查询也应遵循这种原则，提高Cache命中率<br />
每个Componet也是顺序存储，保存有Archetype ID<br />

Chunk<br />
每个Chunk内部连续，批处理会摊薄跳转开销<br />
ECS中Chunk大小： 16kb， CPU L1缓存一般为32kb，缓存行64byte，以缓存行为单位传递。<br />
ECS中Chunk容量：128个，继承IEnabled接口会占用掩码，为继承无掩码。<br />
区分两个Chunk的概念<br />

ComponentData<br />
内存对齐，需手动调整根据字节数手动调整类型顺序，节省内存<br />

SubScene<br />
SubScene.entity包含实体与组件的二进制数据，外部资源弱引用，元数据与序列化信息等。随主包发布，和AB包构建相互独立。<br />

System<br />
可动态创建和动态销毁，但Component会在TypeManager之前注册<br />

Job<br />
PC端 线程调度成本没那么高。Intel(P-Core E - Core) AMD(全大核 / 同构大小核)。大小核差距不明显，大概30%-40%<br />
移动端 线程调度成本高。ARM架构大小核。大小核差距明显，2-3倍可能，功耗限制，散热限制等<br />
IJob/IJobParallelFor/IJobParallelForBatch/IJobEntity<br />
通常以 实体（或元素）为单位被调用<br />
IJobChunk<br />
以 Chunk为单位执行，每个 Execute 调用处理一个完整 Chunk 中的所有实体<br />
