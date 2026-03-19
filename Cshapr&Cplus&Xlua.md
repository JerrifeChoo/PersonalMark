Unity本身包含有C#和C++代码，C#运行在Mono/IL2CPP运行时环境。
托管内存：简单理解为C#侧的内存，有GC。
托管堆（Managed Heap）：C# 脚本中通过 new 关键字创建的对象、数组、委托等都在托管堆上分配。
自动垃圾回收（GC）：运行时（Mono/IL2CPP）的垃圾回收器会定期检查并释放不再被引用的托管对象，开发者无需手动释放。
非托管内存：简单理解为C++侧内存，需要手动释放或由Unity管理等。
通过 System.Runtime.InteropServices.Marshal 分配的本地内存。
Unity 引擎内部（C++ 层）为纹理、网格、音频等资源分配的内存（这些通常由 Unity 引擎自身管理，不在 .NET 的 GC 控制范围内）。
第三方原生插件（如 C++ 库）分配的内存。

Xlua库：自身内存在C++，gc通过lua的垃圾回收器来收集。
[CSharpCallLua] 解决 C# → Lua 的调用。
[LuaCallCSharp] 解决 Lua → C# 的调用。
[GCOptimize] 解决值类型在两条路径上传递时的 GC 问题。

[CSharpCallLua] —— 让 C# 高效调用 Lua 避免使用 LuaFunction.Call 带来的 object[] 分配和值类型装箱
[LuaCallCSharp] —— 让 Lua 高效调用 C# 避免运行时反射查找成员，调用路径静态化。
[GCOptimize] —— 消除值类型传递的 GC
