优势：
1、数据连续性，提升Cache命中率
2、Burst，将C#代码优化为SIMD机器码，利用CPU向量指令，批处理
3、Job，多线程并行处理，充分利用多核优势

Archetype/Chunk
Archetype相同的组件组合会顺序存储，保存有Component的地址，Query查询也应遵循这种原则，提高Cache命中率
每个Componet也是顺序存储，保存有Archetype ID
每个Chunk内部连续，批处理会摊薄跳转开销

ComponentData
内存对齐，需手动调整根据字节数手动调整类型顺序，节省内存

SubScene
SubScene.entity包含实体与组件的二进制数据，外部资源弱引用，元数据与序列化信息等。随主包发布，和AB包构建相互独立。

System
可动态创建和动态销毁，但Component会在TypeManager之前注册
