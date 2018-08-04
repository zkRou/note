
- [场景]:

    com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `com.kris.program.webapi.Test$Book` (no Creators, like default construct, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
 at [Source: (String)"{"id":"1","name":"book"}"; line: 1, column: 2]

    [原因]:

    JVM不会添加默认的无参构造函数，而jackson的反序列化需要无参构造函数，因此报错。

    [解决]:

    在实体类中补上一个无参构造器即可。

 - [场景]：

    java.nio.file.InvalidPathException: Malformed input or input contains unmappable characters

    [原因]：

    java写文件时文件名编码和System.getProperty("sun.jnu.encoding")有关。

    `-Dfile.encoding=UTF-8`和`-Dsun.jnu.encoding=UTF-8`的区别：sun.jnu.encoding 影响文件名的创建，而 file.encoding 则影响到文件内容。

    [解决]：JVM_OPTION增加-Dsun.jnu.encoding=UTF-8   