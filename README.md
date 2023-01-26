# Random Java Facts

Some interesting or rare facts which I did not know while reading JLS

- Unicode escape, and any amount of `u`s.
    ```java
    public class Test {
        public static void main(String[] args) {
            char \u4E2D = '\uuu4E2D';
            System.out.println('中');
            System.out.println(\uuuuuuuuu4E2D);
        }
    }
    ```

    ```bash
    中
    中
    ```

- `\u001A` (ctrl-z, or ascii sub characters) after a input element (whitespace / comment / token (identifier / keyword / literal / operator /  separator))

    ```java
    public\u001A class Test {
        public\u001A static void main(String[] args\u001A) {
            String text = "Hello world!";
            System\u001A.out.println(text);
        }
    }
    ```

    ```bash
    Hello world!
    ```

- Whitespace is also represented by form-feed, horizontal tab or carriage return (new line).

    ```java
    public class Test {
        // \u0020 space
        // \u000C formfeed
        // \u0009 horizontal tab
        // \u000D carriage return
        public\u0020static\u000Cvoid\u0009main(String[]\u000Dargs) {
            System.out.println("whitespace");
        }
    }
    ```

    ```
    whitespace
    ```

- Unexpected end of line comment.
    ```java
    // comment\u000Apublic class Test {
        public static void main(String[] args) {
            System.out.println("comment");
        }
    }
    ```

    ```bash
    comment
    ```

- `label` and `break`.

    ```java
    public class Test {
        public static void main(String[] args) {
            outerLoop:  // declare a label
            for (int i = 0; i < 10; i++) {
                for (int j = 0; j < 3 ; j++) {
                    System.out.println(j);
                    if (i == 1) {
                        break outerLoop;  // break nested loop
                    }
                }
            }
        }
    }
    ```

    ```bash
    0
    1
    2
    0
    ```

- `strictfp` keyword is used on class / non abstract method / interface to ensure calculations are only with IEEE single and double precision type instead of allowing to use extended exponent when available (note: it is now default in java 17).

    - This means, more consistent arithmetic errors across platform. (Also means some arithmetic that used to work will fail.)

- underscore in number (better readability?), no before and after the digit tho. Also, `X`, `B`, and float/double suffix `F` and `D` can be capitalized.
    ```java
    public class Test {
        public static void main(String[] args) {
            System.out.println(10_0000___0000L);
            System.out.println(0xff_ff);
            System.out.println(0Xff_ff);
            System.out.println(0b11_11);
            System.out.println(0B11_11);
            System.out.println(077_77);
        }
    }
    ```

    ```bash
    1000000000
    65535
    65535
    15
    15
    4095
    ```

- infinities are only valid if produced in the following ways:

    ```java
    public class Test {
        public static void main(String[] args) {
            System.out.println(1f/0f);
            System.out.println(-1d/0d);
            System.out.println(Float.POSITIVE_INFINITY);
            System.out.println(Float.NEGATIVE_INFINITY);
            System.out.println(Double.POSITIVE_INFINITY);
            System.out.println(Double.NEGATIVE_INFINITY);
        }
    }
    ```

    ```bash
    Infinity
    -Infinity
    Infinity
    -Infinity
    Infinity
    -Infinity
    ```

- String literals are same instance.

    - literal + literal = same instance
    - variable + literal = same intern instance
    - variable + variable = different instance

    ```java
    public class Test {
        public static void main(String[] args) {
            String hello = "Hello", hel = "hel", lo = "lo";
            System.out.println(hello == "Hello");
            System.out.println(hello == ("Hel"+"lo"));
            System.out.println(hello == ("Hel"+lo));
            System.out.println(hello == ("Hel"+lo).intern());
            System.out.println(hello == (hel+lo).intern());
        }
    }
    ```

    ```bash
    true
    true
    false
    true
    false
    false
    ```

- Applying annotation to inner array

    ```java
    import java.lang.annotation.ElementType;
    import java.lang.annotation.Retention;
    import java.lang.annotation.RetentionPolicy;
    import java.lang.annotation.Target;
    
    @Retention(RetentionPolicy.RUNTIME)
    @Target({ElementType.LOCAL_VARIABLE, ElementType.TYPE_USE})
    public @interface MyAnnotation {
    
    }
    ```

    ```java
    public class Test {
        public static void main(String[] args) {
            @MyAnnotation int[] arr1 = {1,2,3};  // apply to int
            int @MyAnnotation [][] arr2 = {{1},{2},{3}};  // apply to int[][]
            int [] @MyAnnotation [] arr3 = {{1},{2},{3}};  // apply to int[]
    
            System.out.println("Hello World!");
        }
    }
    ```

    ```
    Hello World
    ```

- Nested annotation

    ```java
    import java.lang.annotation.Repeatable; 
    // Foo: Repeatable annotation type 
    @Repeatable(FooContainer.class) 
    @interface 
    Foo { int value(); } 
    // FooContainer: Containing annotation type of Foo 
    //               Also a repeatable annotation type itself 
    @Repeatable(FooContainerContainer.class) 
    @interface FooContainer { 
        Foo[] value(); 
    } 
    // FooContainerContainer: Containing annotation type of FooContainer 
    @interface FooContainerContainer { 
        FooContainer[] value(); 
    }
    ```

    Then you can do:

    ```java
    @FooContainer({@Foo(1)}) @FooContainer({@Foo(2)}) 
    class Test {}
    ```

- A class variable in interface is always static, even if it does not have `static` modifier.

- Variables can be unnamed, for example, array components.

- Type is interface, class, primitives, and array. A variable does not guaranteed to always refer to a subtype of its declared type, because of `heap pollution`, e.g.

    ```java
    List l = new ArrayList<Number>(); 
    List<String> ls = l;  // Unchecked warning
    ```

    ```java
    static void m(List<String>... stringLists) {
        Object[] array = stringLists;    
        List<Integer> tmpList = Arrays.asList(42);    
        array[0] = tmpList;                // no unchecked warning   
        String s = stringLists[0].get(0);  // ClassCastException at run time
    }
    ```

### Naming Conventions

- `com.example.xxx` are called unique package name:

    > You form a unique package name by first having (or belonging to an organization that has) an Internet domain name, such as oracle.com. You then reverse this name, component by component, to obtain, in this example, com.oracle, and use this as a prefix for your package names, using a convention developed within your organization to further administer package names. Such a convention might specify that certain package name components be division, department, project, machine, or login names.

- Class names should be descriptive nouns or noun phrases, not overly long, in mixed case with the first letter of each word capitalized. e.g.:

    - `ClassLoader `
    - `SecurityManager `
    - `Thread `
    - `Dictionary `
    - `BufferedInputStream`

- Type variable names should be pithy, single character if possible, yet evocative, and no lowercase letter to make it distinguishable.

    - `E` for container element type.
    - `K` and `V` for map type.
    - `X` should be arbitrary types.
    - `T` for general type, for multi-type use neighbour of `T`, i.e. `S`, or numeric postfix like  `T1` and `T2`.
    - avoid same type name for generic method inside generic class

- One character local variable

    - `b` for a byte
    - `c` for a char
    - `d` for a double 
    - `e` for an Exception
    - `f` for a float
    - `i`, `j`, and `k` for ints 
    - `l` for a long 
    - `o` for an Object 
    - `s` for a String 
    - `v` for an arbitrary value of some type
