## Non Empty Initializer List

Instantiating a class type with non-empty initializer list would call the constructor who accept a initilializer list if defined, otherwise, it would call the constructor whose number of parameters equals the size of initializer list.

## Empty Initializer List

Instantiating a class type with empty initializer list won't call the constuctors described above. Instead, it would initialize all Scalar type or C style array within the type to zero firstly, and then call class's default constructor. In other words, it initilize all instance's memory to zero first.
