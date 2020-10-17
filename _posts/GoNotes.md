# Go Notes

## AWS Secret

// Use this code snippet in your app.
// If you need more information about configurations or implementing the sample code, visit the AWS docs:   
// https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/setting-up.html

// Use this code snippet in your app.
// If you need more information about configurations or implementing the sample code, visit the AWS docs:   
// https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/setting-up.html

~~~go
import (
	"github.com/aws/aws-sdk-go/service/secretsmanager"
	"github.com/aws/aws-sdk-go/aws"
	"github.com/aws/aws-sdk-go/aws/awserr"
	"github.com/aws/aws-sdk-go/aws/session"
	"encoding/base64"
	"fmt"
)

func getSecret() {
	secretName := "lab/webapp/mysql-34569208"
	region := "us-west-2"
//Create a Secrets Manager client
svc := secretsmanager.New(session.New(),
                              aws.NewConfig().WithRegion(region))
input := &secretsmanager.GetSecretValueInput{
	SecretId:     aws.String(secretName),
	VersionStage: aws.String("AWSCURRENT"), // VersionStage defaults to AWSCURRENT if unspecified
}

// In this sample we only handle the specific exceptions for the 'GetSecretValue' API.
// See https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html

result, err := svc.GetSecretValue(input)
if err != nil {
	if aerr, ok := err.(awserr.Error); ok {
		switch aerr.Code() {
			case secretsmanager.ErrCodeDecryptionFailure:
			// Secrets Manager can't decrypt the protected secret text using the provided KMS key.
			fmt.Println(secretsmanager.ErrCodeDecryptionFailure, aerr.Error())

			case secretsmanager.ErrCodeInternalServiceError:
			// An error occurred on the server side.
			fmt.Println(secretsmanager.ErrCodeInternalServiceError, aerr.Error())

			case secretsmanager.ErrCodeInvalidParameterException:
			// You provided an invalid value for a parameter.
			fmt.Println(secretsmanager.ErrCodeInvalidParameterException, aerr.Error())

			case secretsmanager.ErrCodeInvalidRequestException:
			// You provided a parameter value that is not valid for the current state of the resource.
			fmt.Println(secretsmanager.ErrCodeInvalidRequestException, aerr.Error())

			case secretsmanager.ErrCodeResourceNotFoundException:
			// We can't find the resource that you asked for.
			fmt.Println(secretsmanager.ErrCodeResourceNotFoundException, aerr.Error())
		}
	} else {
		// Print the error, cast err to awserr.Error to get the Code and
		// Message from an error.
		fmt.Println(err.Error())
	}
	return
}

// Decrypts secret using the associated KMS CMK.
// Depending on whether the secret is a string or binary, one of these fields will be populated.
var secretString, decodedBinarySecret string
if result.SecretString != nil {
	secretString = *result.SecretString
} else {
	decodedBinarySecretBytes := make([]byte, base64.StdEncoding.DecodedLen(len(result.SecretBinary)))
	len, err := base64.StdEncoding.Decode(decodedBinarySecretBytes, result.SecretBinary)
	if err != nil {
		fmt.Println("Base64 Decode Error:", err)
		return
	}
	decodedBinarySecret = string(decodedBinarySecretBytes[:len])
}

// Your code goes here.
```

}
~~~



With CodeGuru, we built the respo as below, git commit all files and create a new branch with trigger_branch. CodeGuru analyses are triggered by pull requests,

```
cd /cloudacademy/lab
git add .
git checkout -b trigger_branch
git commit -m "trigger a CodeGuru analysis by pushing Java code"
git push origin trigger_branch
```

![image-20200921102948156](https://tva1.sinaimg.cn/large/007S8ZIlgy1giy2dd247tj31pk09sq5d.jpg)

Amazon GuardDuty continuously monitors and identifies threats by analyzing several types of activity in your AWS account and any invited member accounts that you link to. GuardDuty can notify you of a wide variety of threats including unauthorized access, trojans, communication with Tor anonymizing, or cryptocurrency networks.

## StackOverflow

1. printout the string with mutiple lines.

![image-20200924103525930](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj1je6l6tnj30qc0aa750.jpg)

2. for range in Go, ( such as for each dialect)

   A "for" statement with a "range" clause iterates through all entries of an array, slice, string or map, or values received on a channel. For each entry it assigns iteration values to corresponding iteration variables and then executes the block.

![image-20200924103736084](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj1jgdtp2yj311q0f8ac8.jpg)

example with for range:

![image-20200924105839006](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj1k2ao4faj30zi0rin1v.jpg)

3. **how to append two slice together**

   Add dots after the second slice:

   ```golang
   //---------------------------vvv
   append([]int{1,2}, []int{3,4}...)
   ```

   This is just like any other variadic function.

   ```go
   func foo(is ...int) {
       for i := 0; i < len(is); i++ {
           fmt.Println(is[i])
       }
   }
   
   func main() {
       foo([]int{9,8,7,6,5}...)
   }
   ```

4. Golang doesn’t support optional parameters with function, which means it can’t support two functions of same name but with different parameters, just for simplicity.

5. 查看是否有文件存在

 ```go
   if _, err := os.Stat("/path/to/whatever"); err == nil {
     // path/to/whatever exists
   
   } else if os.IsNotExist(err) {
     // path/to/whatever does *not* exist
   
   } else {
     // : file may or may not exist. See err for details.
   
     // Therefore, do *NOT* use !os.IsNotExist(err) to test for file existence
   
   
   }
 ```

6. 转换数字成为字符串

```go
package main

import (
    "strconv"
    "fmt"
)

func main() {
    t := strconv.Itoa(123)
    //t:= strconv.FormatInt(int64(123), 10) -- it also works.
    fmt.Println(t)
}
```

7. you can use Sprintf() for format string. **fmt.Sprintf function returns a string and you can format the string the very same way you would have with **fmt.Printf

```go
s := fmt.Sprintf("Good Morning, This is %s and I'm living here from last %d years ", "John", 20)
```

Your output will be :

> Good Morning, This is John and I'm living here from last 20 years.

8. find the type of the elements, using relfect

   The Go reflection package has methods for inspecting the type of variables.

   The following snippet will print out the reflection type of a string, integer and float.

   ```go
   package main
   
   import (
       "fmt"
       "reflect"
   )
   
   func main() {
   
       tst := "string"
       tst2 := 10
       tst3 := 1.2
   
       fmt.Println(reflect.TypeOf(tst))
       fmt.Println(reflect.TypeOf(tst2))
       fmt.Println(reflect.TypeOf(tst3))
   
   }
   ```

   Output:

   ```golang
   Hello, playground
   string
   int
   float64
   ```

9. read files line by line

   Read file use bufio.Scanner,

   ```go
   func scanFile() {
       f, err := os.OpenFile(logfile, os.O_RDONLY, os.ModePerm)
       if err != nil {
           log.Fatalf("open file error: %v", err)
           return
       }
       defer f.Close()
   
       sc := bufio.NewScanner(f)
       for sc.Scan() {
           _ = sc.Text()  // GET the line string
       }
       if err := sc.Err(); err != nil {
           log.Fatalf("scan file error: %v", err)
           return
       }
   }
   ```

   10.  [Pointers vs. values in parameters and return values](https://stackoverflow.com/questions/23542989/pointers-vs-values-in-parameters-and-return-values)

- Methods using receiver pointers are common; [the rule of thumb for receivers is](https://github.com/golang/go/wiki/CodeReviewComments#receiver-type), "If in doubt, use a pointer."
- Slices, maps, channels, strings, function values, and interface values are implemented with pointers internally, and a pointer to them is often redundant.
- Elsewhere, use pointers for big structs or structs you'll have to change, and otherwise [pass values](https://github.com/golang/go/wiki/CodeReviewComments#pass-values), because getting things changed by surprise via a pointer is confusing.

One case where you should often use a pointer:

- **Receivers** are pointers more often than other arguments. It's not unusual for methods to modify the thing they're called on, or for named types to be large structs, so [the guidance is](https://github.com/golang/go/wiki/CodeReviewComments#receiver-type) to default to pointers except in rare cases.
- For **slices**, you don't need to pass a pointer to change elements of the array. `io.Reader.Read(p []byte)` changes the bytes of `p`, for instance. 
- For **slices you'll reslice** (change the start/length/capacity of), built-in functions like `append` accept a slice value and return a new one. I'd imitate that; it avoids aliasing, returning a new slice helps call attention to the fact that a new array might be allocated, and it's familiar to callers.
  
  - It's not always practical follow that pattern. Some tools like [database interfaces](https://cloud.google.com/appengine/docs/go/datastore/reference#Get) or [serializers](http://golang.org/pkg/encoding/json/) need to append to a slice whose type isn't known at compile time. They sometimes accept a pointer to a slice in an `interface{}` parameter.
- Maps, channels, strings, and function and interface values, like slices, are internally references or structures that contain references already, so if you're just trying to avoid getting the underlying data copied, you don't need to pass pointers to them. (rsc wrote a separate post on how interface values are stored).

  - You still may need to pass pointers in the rarer case that you want to modify the caller's struct: flag.StringVar takes a *string for that reason, for example.

11. Import, const, variable, init, main 执行顺序

    ![image-20200925171234249](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj30hpntzkj31230u0n7y.jpg)

12. `int32` and `time.Duration` are different types. You need to convert the `int32` to a `time.Duration`, such as `time.Sleep(time.Duration(rand.Int31n(1000)) * time.Millisecond)`.

13. one way to leverage configuration is to read json files:

    **conf.json**:

    ```json
    {
        "Users": ["UserA","UserB"],
        "Groups": ["GroupA"]
    }
    ```

    **Program to read the configuration**

    ```go
    import (
        "encoding/json"
        "os"
        "fmt"
    )
    
    type Configuration struct {
        Users    []string
        Groups   []string
    }
    
    file, _ := os.Open("conf.json")
    defer file.Close()
    decoder := json.NewDecoder(file)
    configuration := Configuration{}
    err := decoder.Decode(&configuration)
    if err != nil {
      fmt.Println("error:", err)
    }
    fmt.Println(configuration.Users) // output: [UserA, UserB]
    ```

14. Json Marshal-encoding example:

    ![image-20200929142441050](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7i47bx1cj31aj0u0n9p.jpg)

    Json decoding example with unmarshal:

    ![image-20200929142607808](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7i5vbn4tj31zj0u0134.jpg)

using tags in struct, then the json field name doesn’t need match the go struct name.

![image-20200929142738814](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7i7apnatj31600ledon.jpg)

![image-20200929143214062](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7ic2qs6zj314m0pqdvz.jpg)

15. Golang handle http request

    ![image-20200929144703681](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7jg28vm3j31s80u0tk3.jpg)

handler for middleware

![image-20200929152108091](/Users/i327087/Library/Application Support/typora-user-images/image-20200929152108091.png)

CORS, — add to the http.ResponseWriter

![image-20200929153306008](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7k3ed1lfj322s0cots2.jpg)

context usage:

![image-20200929170021506](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7mmkgkqrj31ik0pg11r.jpg)

QueryContext; QueryRowContext;ExecContext;

Upload files using multiform.

![image-20200929172951490](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7ngvvh78j31nq0u0wuk.jpg)

download file

![image-20200929173453098](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj7nm42xvvj31fb0u07hn.jpg)

16. string，修改操作是被禁止的：

    ```go
    s := "Hello Gopher!"
    s[1] = 'T'
    ```

    而string能支持这样的操作：

    ```go
    s := "Hello Gopher!"
    s = "Tello Gopher!"
    ```

原因：字符串的值不能被更改，但可以被替换。string在底层都是结构体`stringStruct{str: str_point, len: str_len}`，string结构体的str指针指向的是一个字符常量的地址， 这个地址里面的内容是不可以被改变的，因为它是只读的，但是这个指针可以指向不同的地址。

![image-20201007205926661](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjh2hl33q5j318o07ydj6.jpg)

因为string的指针指向的内容是不可以更改的，所以每更改一次字符串，就得重新分配一次内存，之前分配的空间还需要gc回收，这是导致string相较于[]byte操作低效的根本原因。

go中string与[]byte的互换：

![image-20201007211901904](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjh31sz8elj30e407qdgj.jpg)



17. unsafe.Pointer其实就是类似C的void *，在golang中是用于各种指针相互转换的桥梁。在go中，任何类型的指针*T都可以转换为unsafe.Pointer类型的指针，它可以存储任何变量的地址。同时，unsafe.Pointer类型的指针也可以转换回普通指针，而且可以不必和之前的类型*T相同。另外，unsafe.Pointer类型还可以转换为uintptr类型，该类型保存了指针所指向地址的数值，从而可以使我们对地址进行数值计算。uintptr是golang的内置类型，是能存储指针的整型，uintptr的底层类型是int，它和unsafe.Pointer可相互转换。uintptr和unsafe.Pointer的区别就是：unsafe.Pointer只是单纯的通用指针类型，用于转换不同类型指针，它不可以参与指针运算；而uintptr是用于指针运算的，GC 不把 uintptr 当指针，也就是说 uintptr 无法持有对象，uintptr类型的目标会被回收。

18. go mod 解释
![image-20201009162940544](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjj5xemdqmj31eg0esn9m.jpg)

19. ***Mac、Linux、Windows下分别进行交叉编译***

    Golang 支持交叉编译，在一个平台上生成另一个平台的可执行程序。

    Mac 下编译 Linux 和 Windows 64位可执行程序

    | 12   | CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build main.goCGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build main.go |
    | ---- | ------------------------------------------------------------ |
    |      |                                                              |

    Linux 下编译 Mac 和 Windows 64位可执行程序

    | 12   | CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build main.goCGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build main.go |
    | ---- | ------------------------------------------------------------ |
    |      |                                                              |

    Windows 下编译 Mac 和 Linux 64位可执行程序

    | 123456789 | SET CGO_ENABLED=0SET GOOS=darwinSET GOARCH=amd64go build main.go-------------------------------------------------SET CGO_ENABLED=0SET GOOS=linuxSET GOARCH=amd64go build main.go |
    | --------- | ------------------------------------------------------------ |
    |           |                                                              |

    GOOS：目标平台的操作系统（darwin、freebsd、linux、windows）。
    GOARCH：目标平台的体系架构（386、amd64、arm）。
    CGO_ENABLED：交叉编译不支持 CGO 所以要禁用它。

20. Pull request in github example:

    1. Fork it ( https://github.com/PagerDuty/go-pagerduty/fork )
    2. Create your feature branch (`git checkout -b my-new-feature`)
    3. Commit your changes (`git commit -am 'Add some feature'`)
    4. Push to the branch (`git push origin my-new-feature`)
    5. Create a new Pull Request

    