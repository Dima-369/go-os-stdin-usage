# Access `os.Stdin` in bash for piping

Running `go build main.go && echo "Look!" | ./main` outputs:

```
There is something in os.Stdin:
Look!
```

```go
package main

import (
	"fmt"
	"io/ioutil"
	"os"
)

func main() {
	fi, err := os.Stdin.Stat()
	if err != nil {
		panic(err)
	}
	if fi.Size() > 0 {
		out, err := ioutil.ReadAll(os.Stdin)
		if err != nil {
			panic(err)
		}
		fmt.Println("There is something in os.Stdin:")
		fmt.Println(string(out))
	} else {
		fmt.Println("os.Stdin is empty")
	}
}
```
