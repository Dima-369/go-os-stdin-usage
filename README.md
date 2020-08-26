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

# Send stdin to exec.Command()

Running the application below is equivalent to this piping: `echo "this is from StdinPipe" | cat`

```go
package main

import (
	"io"
	"os/exec"
)

func main() {
	cmd := exec.Command("cat")
	stdin, err := cmd.StdinPipe()
	if err != nil {
		panic(err)
	}

	go func() {
		defer stdin.Close()
		_, err = io.WriteString(stdin, "this is from StdinPipe")
		if err != nil {
			panic(err)
		}
	}()

	out, err := cmd.CombinedOutput()
	if err != nil {
		panic(err)
	}

	println(string(out))
}
```
