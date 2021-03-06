## Overview

Whatever the app is, the bootstrapping phase is roughly composed by:

- Read the configuration from out of the binary. Namely, flags, environment
  variables, and/or configuration files.

- Initialize dependencies. Databases, message queues, service discoveries, etc.

- Define how to run the app. HTTP, RPC, command-lines, cronjobs, or more often mixed.

Package core abstracts those repeated steps, keeping them concise, portable yet explicit. 
Let's see the following snippet:

```go
package main

import (
  "context"
  "net/http"

  "github.com/DoNewsCode/core"
  "github.com/DoNewsCode/core/observability"
  "github.com/DoNewsCode/core/otgorm"
  "github.com/gorilla/mux"
)

func main() {
  // Phase One: create a core from a configuration file
  c := core.New(core.WithYamlFile("config.yaml"))

  // Phase two: bind dependencies
  c.Provide(otgorm.Providers())

  // Phase three: define service
  c.AddModule(core.HttpFunc(func(router *mux.Router) {
    router.HandleFunc("/", func(writer http.ResponseWriter, request *http.Request) {
      writer.Write([]byte("hello world"))
    })
  }))

  // Phase Four: run!
  c.Serve(context.Background())
}

```

In a few lines, an HTTP service is bootstrapped in the style outlined above.
It is simple, explicit, and to some extent, declarative.

The service demonstrated above uses an inline handler function to highlight the point.
Normally, for real projects, we will use modules instead. 
The "module" in package Core's glossary is not necessarily a go module (though it can be). It is simply a group of services.

You may note that the HTTP service doesn't really consume the dependency.
That's true.

Let's rewrite the HTTP service to consume the above dependencies.

```go
package main

import (
  "context"
  "net/http"

  "github.com/DoNewsCode/core"
  "github.com/DoNewsCode/core/otgorm"
  "github.com/DoNewsCode/core/srvhttp"
  "github.com/gorilla/mux"
  "gorm.io/gorm"
)

type User struct {
  Id   string
  Name string
}

type Repository struct {
  DB *gorm.DB
}

func (r Repository) Find(id string) (*User, error) {
  var user User
  if err := r.DB.First(&user, id).Error; err != nil {
    return nil, err
  }
  return &user, nil
}

type Handler struct {
  R Repository
}

func (h Handler) ServeHTTP(writer http.ResponseWriter, request *http.Request) {
  encoder := srvhttp.NewResponseEncoder(writer)
  encoder.Encode(h.R.Find(request.URL.Query().Get("id")))
}

type Module struct {
  H Handler
}

func New(db *gorm.DB) Module {
  return Module{Handler{Repository{db}}}
}

func (m Module) ProvideHTTP(router *mux.Router) {
  router.Handle("/", m.H)
}

func main() {
  // Phase One: create a core from a configuration file
  c := core.New(core.WithYamlFile("config.yaml"))

  // Phase two: bind dependencies
  c.Provide(otgorm.Providers())

  // Phase three: define service
  c.AddModuleFunc(New)

  // Phase four: run!
  c.Serve(context.Background())
}
```

Phase three has been replaced by the `c.AddModuleFunc(New)`. `AddModuleFunc` populates the arguments to `New` from dependency containers
and add the returned module instance to the internal module registry.

When c.Serve() is called, all registered modules will be scanned for implemented interfaces. 
The module in the example implements interface: 

```go
type HTTPProvider interface {
	ProvideHTTP(router *mux.Router)
}
```

Therefore, the core knows this module wants to expose HTTP service and subsequently invokes the `ProvideHttp` with a router. You can register multiple modules, and each module can implement one or more services.

Now we have a fully workable project, with layers of handler, repository, and entity. 
Had this been a DDD workshop, we would be expanding the example even further. 

That being said, let's redirect our attention to other goodies package core has offered:

- Package core natively supports multiplexing modules. 
  You could start you project as a monolith with multiple modules, and gradually migrate them into microservices.

- Package core doesn't lock in transport or framework.
  For instance, you can use go kit to construct your service, and leveraging gRPC, AMPQ, thrift, etc. Non-network services like CLI and Cron are also supported.

- Sub packages provide support around service coordination, including but not limited to distributed tracing, metrics exporting, error handling, event-dispatching, and leader election.

