# Terraform Day Three (103)

## Overview - Terraform Interpolation.
Terraform templates are written in a language called Hashicorp Configuration Language (HCL), and currently it is declaritive.
The HCL language allows the use of variables, math, and a short list of other functions.
Currently only HCL version 1 has been released, but by September 2018 HCL2 should be released.

## Training Goals for day three
*  Introduction to several common interpolations
*  Introduction to the Terraform Console
*  Understanding of the three Variable types
*  Local vs External Variables
*  Working with Lists
*  Working with Maps

NOTE: We will be using the same user roles as trainingdayone had so all `terraform` commands should be run like this, to use the correct account and user.
```hcl
aws-vault exec terraformrole -- terraform init
aws-vault exec terraformrole -- terraform apply
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
## References
*  [Terraform Interpolation Document](https://www.terraform.io/docs/configuration/interpolation.html)
*  [HCL2 Spec Document](https://github.com/hashicorp/hcl2/blob/master/hcl/hclsyntax/spec.md)
*  [Terraform 0.12 Upgrade Guide](https://www.terraform.io/upgrade-guides/0-12.html)