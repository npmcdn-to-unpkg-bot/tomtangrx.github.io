---

layout: post

title:  C# 可选参数原理解析

category: 技术

tags: C# 可选参数

keywords: C# 可选参数

---



# C# 可选参数原理解析


[TOC]


## 问题

给公共类库的函数添加了一个可选参数了以后， 更新公共类库， 出现了找不到方法的错误， 调试居然没有错误。 定位到原因可能是可选参数的原因

## 上代码

*CSHARP*代码

```CSHARP
    // 类库
    public class OptionalMethodTest
    {
        /// <summary>
        /// 有默认值的函数方法
        /// </summary>
        /// <param name="a">普通参数</param>
        /// <param name="op">有默认值的参数</param>
        /// <returns></returns>
        public static string TestOptionalMethod(string a, string op = "default value")
        {
            return a + op;
        }

        /// <summary>
        /// 没有默认值的函数
        /// </summary>
        /// <param name="a"></param>
        /// <param name="op"></param>
        /// <returns></returns>
        public static string TestMethod(string a, string op)
        {
            return a + op;
        }
    }

    // 控制台
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(OptionalMethodTest.TestOptionalMethod("p1"));
            Console.WriteLine(OptionalMethodTest.TestOptionalMethod("p1", "p2"));
            Console.WriteLine(OptionalMethodTest.TestMethod("p1", "p2"));
        }
    }

```

编译后的 *IL* 

```IL
.method private hidebysig static void  Main(string[] args) cil managed
{
  .entrypoint
  // 代码大小       65 (0x41)
  .maxstack  2
  IL_0000:  nop
  IL_0001:  ldstr      "p1"
  IL_0006:  ldstr      "default value"  // 默认值
  IL_000b:  call       string [TestLib]TestLib.OptionalMethodTest::TestOptionalMethod(string,
                                                                                      string)
  IL_0010:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_0015:  nop
  IL_0016:  ldstr      "p1"
  IL_001b:  ldstr      "p2"
  IL_0020:  call       string [TestLib]TestLib.OptionalMethodTest::TestOptionalMethod(string,
                                                                                      string)
  IL_0025:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_002a:  nop
  IL_002b:  ldstr      "p1"
  IL_0030:  ldstr      "p2"
  IL_0035:  call       string [TestLib]TestLib.OptionalMethodTest::TestMethod(string,
                                                                              string)
  IL_003a:  call       void [mscorlib]System.Console::WriteLine(string)
  IL_003f:  nop
  IL_0040:  ret
} // end of method Program::Main
```

## 解析
看上面的IL代码就会发现  CSHARP的 可选参数其实就是把参数默认值编译到IL里面

```CSHARP
Console.WriteLine(OptionalMethodTest.TestOptionalMethod("p1"));

Console.WriteLine(OptionalMethodTest.TestOptionalMethod("p1", "default value"));
```
上面两句代码其实是一样的

## 问题的解决
所以开头的问题原因其实就是添加了一个可选参数后，那么调用参数的地方需要把所有调用这个函数的类库都需要重新编译下， 不然就识别不了。

## 启示
公共类库的参数添加要谨慎，如果要添加参数， 尽量使用重载的方式添加，当然新加函数没有关系。