To try out Knative Serving, let's use a simple app from the Knative samples, the source of which is below.

```go
package main

import (
  "fmt"
  "log"
  "net/http"
  "os"
)

func handler(w http.ResponseWriter, r *http.Request) {
  log.Print("helloworld: received a request")
  target := os.Getenv("TARGET")
  if target == "" {
    target = "World"
  }
  fmt.Fprintf(w, "Hello %s!\n", target)
}

func main() {
  log.Print("helloworld: starting server...")

  http.HandleFunc("/", handler)

  port := os.Getenv("PORT")
  if port == "" {
    port = "8080"
  }

  log.Printf("helloworld: listening on port %s", port)
  log.Fatal(http.ListenAndServe(fmt.Sprintf(":%s", port), nil))
}
```
This app has been compiled into a simple image that is available at 
`gcr.io/knative-samples/helloworld-go`.

Our cluster has the most recent version of Cloud Native Runtimes for Tanzu installed on it, and the "kn" CLI tool is available in the terminal to the right of this text. 

To deploy the app, execute the following command in the terminal.

```terminal:execute
command: kn service create helloworld-go --image gcr.io/knative-samples/helloworld-go --env TARGET="Go Sample v1"
```

To test out the app after the deployment is ready to serve traffic, execute the following command in the terminal.
```terminal:execute
command: curl -L $(kn service describe helloworld-go -o url)
```





One of the key highlights of the Knative Serving features is the scale-to-zero functionality where the application instances will be reduced to zero if there is no activity around on the app for a predefined amount of time. This is one of the core tenets of the serverless paradigm. We will surely witness this key functionality at this workshop.