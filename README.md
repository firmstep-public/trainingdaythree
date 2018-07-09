# Terraform Day Three (103)

## Overview - Terraform Interpolation.
Terraform templates are written in a language called Hashicorp Configuration Language (HCL), and currently it is declaritive.
The HCL language allows the use of variables, math, and a short list of other functions.
Currently only HCL version 1 has been released, but by September 2018 HCL2 should be released.

## Training Goals for day three
*  Understanding of the three core data types
*  Introduction to the Terraform Console
*  Introduction to several common interpolations
*  Local vs External Variables
*  Working with Lists
*  Working with Maps

## The Three Core Data Types
Terraform allows you to work with three variable types: maps, lists, and strings
When creating a Terraform variable you specify its type.
```hcl
variable "name" {
    type = "string"
    description = "The name of the resource"
}
variable "remote_ip_addresses" {
    type = "list"
    description = "A list of IP addresses that will be allowed through the firewall"
}
variable "tags" {
    type = "map"
    description = "A map of tags to add to the resources"
}
```
These data types are used for inputs and outputs from Terraform Templates.

Additionally, it is possible to convert strings into maps or lists, and visa versa.


1.  `map(key1, value1, key2, value2, ...)` - Returns a map consisting of the key/value pairs specified as arguments. Every odd argument must be a string key, and every even argument must have the same type as the other values specified
    ```hcl
    > map("apple", "tree", "zebra", "animal")
    {
        "apple" = "tree"
        "zebra" = "animal"
    }
    ```
2.  `list(items, ...)` - Returns a list consisting of the arguments to the function. This function provides a way of representing list literals in interpolation.
    ```hcl
    > list("apple", "orange", "banana", "peach")
    [
        "apple",
        "orange",
        "banana",
        "peach",
    ]
    ```

## Common Interpolations
1.  `basename(path)` - Returns the last element of a path.
    ```hcl
    > basename("/this/is/a/path")
    path
    ```
2.  `base64decode(string)` - Given a base64-encoded string, decodes it and returns the original string.
    ```hcl
    > base64encode("hello")
    aGVsbG8=
    ```
3.  `base64encode(string)` - Returns a base64-encoded representation of the given string.
    ```hcl
    > base64decode("aGVsbG8=")
    hello
    ```
4.  `cidrhost(iprange, hostnum)` - Takes an IP address range in CIDR notation and creates an IP address with the given host number.
    ```hcl
    > cidrhost("10.0.0.0/8", 2)
    10.0.0.2
    ```
5.  `cidrnetmask(iprange)` - Takes an IP address range in CIDR notation and returns the address-formatted subnet mask format that some systems expect for IPv4 interfaces.
    ```hcl
    > cidrnetmask("10.0.0.0/8")
    255.0.0.0
    ```
6.  `cidrsubnet(iprange, newbits, netnum)` - Takes an IP address range in CIDR notation (like 10.0.0.0/8) and extends its prefix to include an additional subnet number.
    ```hcl
    > cidrsubnet("10.0.0.0/8", 8, 2)
    10.2.0.0/16
    > cidrsubnet("2607:f298:6051:516c::/64", 8, 2)
    2607:f298:6051:516c:200::/72
    ```
7.  `compact(list)` - Removes empty string elements from a list. This can be useful in some cases, for example when passing joined lists as module variables or when parsing module outputs.
    ```hcl
    > compact(list("", "hello", "this might have", "", "", "some", "empty", "", "strings"))
    [
      "hello",
      "this might have",
      "some",
      "empty",
      "strings",
    ]
8.  `concat(list1, list2, ...)` - Combines two or more lists into a single list.
    ```hcl
    > concat(list("this list", "of things"), list("joins", "this", "list"))
    [
      "this list",
      "of things",
      "joins",
      "this",
      "list",
    ]
    ```
9.  `distinct(list)` - Removes duplicate items from a list. Keeps the first occurrence of each element, and removes subsequent occurrences. This function is only valid for flat lists.
    ```hcl
    > distinct(list("example", "hello", "example", "hello", "hello"))
    [
      "example",
      "hello",
    ]
    ```
10. `element(list, index)` - Returns a single element from a list at the given index. If the index is greater than the number of elements, this function will wrap using a standard mod algorithm. 
    ```hcl
    > element(list("one", "two", "three", "four", "apple"), 2)
    three
    > element(list("one", "two", "three", "four", "apple"), 3)
    four
    > element(list("one", "two", "three", "four", "apple"), 0)
    one
    ```
11. `file(path)` - Reads the contents of a file into the string. Variables in this file are not interpolated. The contents of the file are read as-is. The path is interpreted relative to the working directory. Path variables can be used to reference paths relative to other base locations. For example, when using `file()` from inside a module, you generally want to make the path relative to the module base, like this: `file("${path.module}/file")`.
    ```hcl
    > file("example.txt")
     _..--""````""--.._
    (_                _;..--""````""--.._
    \ ```"""----"""```(_                _)
     '-.            .-\ ```"""----"""``` /
        `\        /`   '-.            .-'
          '-.__.-'        `\        /`
             ||             '-.__.-'
             ||                ||
             ||                ||
             ||                ||
    jgs _..--||--.._           ||
       (_          _)     _..--||--.._
         ```""""```      (_          _)
                           ```""""```
    ```
12. `format(format, args, ...)` - Formats a string according to the given format. The syntax for the format is standard sprintf syntax. Good documentation for the syntax [can be found here](https://golang.org/pkg/fmt/). Example to zero-prefix a count, used commonly for naming servers: `format("web-%03d", count.index + 1)`
    ```hcl
    > format("%.10d-%.5s-%s %q %v %v", 273676, "elephant", "kingdom", "farmyard", "some value", 50)
    0000273676-eleph-kingdom "farmyard" some value 50
    ```
13. `formatlist(format, args, ...)` - Formats each element of a list according to the given format, similarly to format, and returns a list. Non-list arguments are repeated for each list element.
    ```hcl
    > formatlist("https://%s:%s/", list("google.com", "google.co.uk"), "4433")
    [
      "https://google.com:4433/",
      "https://google.co.uk:4433/",
    ]
    ```
14. `join(delim, list)` - Joins the list with the delimiter for a resultant string. This function works only on flat lists. 
    ```hcl
    > join("::", list("this", "list", "needs", "to", "be", "joined", "into", "a", "string"))
    this::list::needs::to::be::joined::into::a::string
    > join("//", list("this", "list", "needs", "to", "be", "joined", "into", "a", "string"))
    this//list//needs//to//be//joined//into//a//string
    ```
15. `keys(map)` - Returns a lexically sorted list of the map keys.
    ```hcl
    > map("apple", "tree", "zebra", "animal")
    {
        "apple" = "tree"
        "zebra" = "animal"
    }
    > keys(map("apple", "tree", "zebra", "animal"))
    [
        "apple",
        "zebra",
    ]
    > values(map("apple", "tree", "zebra", "animal"))
    [
        "tree",
        "animal",
    ]
    ```
16. `length(list)` - Returns the number of members in a given list or map, or the number of characters in a given string.
    ```hcl
    > length("this is a sentence")
    18
    > length(list("this", "is", "a", "list"))
    4
    ```


## References
*  [Terraform Interpolation Document](https://www.terraform.io/docs/configuration/interpolation.html)
*  [HCL2 Spec Document](https://github.com/hashicorp/hcl2/blob/master/hcl/hclsyntax/spec.md)
*  [Terraform 0.12 Upgrade Guide](https://www.terraform.io/upgrade-guides/0-12.html)