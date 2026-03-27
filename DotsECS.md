优势：<br />
1、数据连续性，提升Cache命中率<br />
2、Burst，将C#代码优化为SIMD机器码，利用CPU向量指令，批处理<br />
3、Job，多线程并行处理，充分利用多核优势<br />
<br />
Cpu<br />
通常每个Cpu核心私有L1(分离指令和数据缓存)、L2，L3多核心共享缓存。逐级检查，缺失时从下级加载填回并返回。<br />
若L1命中，直接返回。<br />
若L1未命中，查L2，命中则填回L1并返回<br />
若L2未命中，查L3，命中则填回L2、L1并返回<br />
若L3未命中，查DRAM，命中则回填L3、L2、L1并返回<br />
<br />
Archetype<br />
Archetype相同的组件组合(Query查询也应遵循这种原则)会顺序存储，保存有Component的地址，提高Cache命中率<br />
每个Componet也是顺序存储，保存有Archetype ID<br />
<br />
Chunk<br />
每个Chunk内部连续，批处理会摊薄跳转开销<br />
ECS中Chunk大小： 16kb， CPU L1缓存一般为32kb，缓存行64byte，以缓存行为单位传递。<br />
ECS中Chunk容量：128个。<br />
区分两个Chunk的概念<br />
<br />
ComponentData<br />
内存对齐，手动调整类型顺序，节省内存<br />
<br />
SubScene<br />
SubScene.entity包含实体与组件的二进制数据，外部资源弱引用，元数据与序列化信息等。随主包发布，和AB包构建相互独立。<br />
<br />
System<br />
可动态创建和动态销毁，但Component会在TypeManager之前注册<br />
<br />
Job<br />
PC端 线程调度成本没那么高。Intel(P-Core E - Core) AMD(全大核 / 同构大小核)。大小核差距不明显，大概30%-40%<br />
移动端 线程调度成本高。ARM架构大小核。大小核差距明显，2-3倍可能，功耗限制，散热限制等<br />
IJob/IJobParallelFor/IJobParallelForBatch/IJobEntity<br />
通常以 实体（或元素）为单位被调用<br />
IJobChunk<br />
以 Chunk为单位执行，每个 Execute 调用处理一个完整 Chunk 中的所有实体<br />
chunkEnabledMask是否被使用取决于整个EntityQuery，而不是单个组件，未继承IEnableableComponent的组件不参与计算。

Update顺序<br />
1. InitializationSystemGroup (部分 ECS 初始化系统)<br />
2. MonoBehaviour.Awake / OnEnable / Start (标准 Unity 生命周期)<br />
3. 【关键阶段】MonoBehaviour.Update 阶段<br />
   ├── EventSystem.Update() ← 处理 UI 输入事件<br />
   ├── 所有其他 MonoBehaviour.Update() 脚本<br />
4. ECS SimulationSystemGroup 阶段 (你的问题中关心的“所有”)<br />
   ├── BeginSimulationEntityCommandBufferSystem <br />
   ├── 其他自定义 ECS 系统<br />
   └── EndSimulationEntityCommandBufferSystem<br />
5. MonoBehaviour.LateUpdate (如果你有的话)<br />
6. ECS PresentationSystemGroup (用于渲染)<br />
