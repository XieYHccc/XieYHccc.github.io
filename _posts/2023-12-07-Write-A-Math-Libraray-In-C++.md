想写数学库也没别的原因，一是练练手，二是减少依赖，三是方便维护，毕竟是自己写的东西。

## std::array vs C-style array

我在思考选取何种数据结构作为向量和矩阵的底层容器时做了一些调查

* C风格数组没有类型，只是一块内存，并没有过多的语义。对C数组使用索引取值其实是在移动指针，该索引值可以是任意整数，包括负整数，因为移动指针我们既可以向后移动也可以向前移动。

* std::array是对C数组的封装，C数组是其唯一的数据，但std::array是一个类型，所以其可以作为函数的参数和返回值，这比C风格数组方便很多。std::array拥有了值的语义，且索引必须是正整数，比如说第一个值第二个值等等。std::array 有很多好用的接口，比如说size(), range-based for loop等等。

* 减少犯错的角度：直接操纵C数组其实就是在操纵内存，编译器不会帮助我们检查错误，很容易犯错。

* 性能：我在Mac M1 Max 做了测试。在Debug模式下C风格数组比std::array快很多，在Release模式下两者没差异，都快得离谱：）

* 结论：能用std::array就用std::array，不用过多担心c++性能问题，编译器的Release会将c++的东西优化的很快。

## size_t vs int vs unsigned int vs ptr_diff

之所以会出现这个问题，是我在选取索引类型是遇到了困难。

* size_t 和unsigned int 均为无符号整数，int是有符号整数。当设计需要索引值可以取负数时，size_t和unsigned int 可以被排除，int也不推荐，建议使用ptr_diff，因为其定义上更为合理，索引的本质其实是在移动指针。（本人对ptr_diff并无过多了解，我不打算让索引可以取负值)

* 在64位电脑上，int和unsigned int均为四个byte 大小，size_t为8个byte大小，这意味着size_t能表示比unsigned int 更大的整数。因此，想要设计一个大容量容器时，采用size_t更合理。

* size_t是stl容器大小的返回类型，当底层容器是stl容器时，为了保持统一性，选择size_t作为索引值更合理。



## std::copy vs memcopy

* 用C的函数直接操纵内存确实快，但用C++标准库的函数在Release模式下和C函数一样快，因为编译器会对标准库函数进行优化，有时候会把标准库函数编译为C函数。除此之外，编译器还有很多我不清楚的优化，因此可能比我直接用C函数还快（我是小白）。

* 结论：对我这种小白来说，能用C++标准库的函数就用C++标准库的函数。当然有时候可能也不想为了一个函数而include一个头文件。

## 从空间的角度设计数学库

本数学库清楚的定义了每种类型所处的空间和拥有的运算结构，如点，向量，法向量，颜色等。