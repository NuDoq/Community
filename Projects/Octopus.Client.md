# The Octopus Deploy HTTP API

`Octopus.Client` makes it easy to write C# programs that manipulate the [Octopus Deploy](http://octopusdeploy.com) [HTTP API](http://g.octopushq.com/ApiDocs).

Because the Octopus application itself is built entirely on the API, C# programs using the API can do anything that could be done by a user of the application itself.

### Getting started

The complete details for the API itself - where to find it, how to authenticate, the available resources and so-on - are available at the [API documentation site](http://g.octopushq.com/ApiDocs).

To use the C# client, first install the package via NuGet:

```
Install-Package Octopus.Client
```

The easiest way to use the client is via the `OctopusRepository` helper:

```
var endpoint = new OctopusServerEndpoint("https://myoctopusserver/", "API-XXXXXXX");
var repository = new OctopusRepository(endpoint)
```

_(Use an "http://" URL if you haven't configured SSL for your server.)_

If you don't want to provide an API key for authentication, you can leave it out and authenticate with the `SignIn()` method instead:

```
repository.Users.SignIn(new LoginCommand { Username = "me", Password = "secret" });
```

### Working with resources

Resources can be loaded and saved with code like the following:

```
var machine = repository.Machines.Get("machines-1");
machine.Name = "Test Server 1";
repository.Machines.Modify(machine);
```

The repository methods all make direct HTTP requests, there's no 'session' abstraction or transaction support.

### Working directly with the Client

For some operations not available through repositories it will be necessary to use the `IOctopusClient` type:

```
var connection = repository.Client.Get(machine.Links["Connection"]);
```

The entire API is accessible by traversing links - each resource carries a collection of links, like the `Connection` link on `MachineResource` shown above.

To start traversing links, `IOctopusClient.RootDocument` is provided:

```
var me = repository.Client.Get(repository.Client.RootDocument.Links["CurrentUser"]);
```

_(This example is superfluous as `repository.Users.GetCurrent()` wraps this common operation.)_
