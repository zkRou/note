
- [场景]:

    com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `com.kris.program.webapi.Test$Book` (no Creators, like default construct, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
 at [Source: (String)"{"id":"1","name":"book"}"; line: 1, column: 2]

    [原因]:

    JVM不会添加默认的无参构造函数，而jackson的反序列化需要无参构造函数，因此报错。

    [解决]:

    在实体类中补上一个无参构造器即可。