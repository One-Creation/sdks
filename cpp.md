# One Creation C++ SDK

This is the first official SDK of the OC platform. Features included:
- User management
- Roles and permissioning
- Datasource management
- Policy management

## Rationale: Why Create an SDK? 

All of One Creation's functionality is available via REST API, so why would we need an SDK? The short answer is that
nobody likes having to write their own API client; SDKs are much easier to get started with, and have the following
additional benefits:

- Allows for sane defaults (e.g: application/json)
- Error handling built-in
- Validate required fields
- Only known methods/content
- Ability to easily expand on functionality based on client needs
- Enforce best practices and patterns (e.g: methods are self-documenting)
- Safe and optimized with good defaults and limits
- Fast and simple adoption (one-line access to API)
- Lean on language features (throwing exceptions, strong typing, optionals)
- Easy to consume knowledge without in-depth knowledge
- Adjust to use-cases (e.g: combining multiple calls into a single method)
- Like we said before, nobody likes implementing an HTTP client every time

## Usage

The primary interface in this SDK is the `ApiClient`. This class contains all methods required for interfacing with the 
One Creation user, policy, datasource, and other APIs. `ApiClient` objects are instantiated using an `ApiConfig` object,
which contains all details for connecting to either the (default) multi-tenant SaaS instance of OC or on-premise private
instances.

```c++
    oc::ApiClient* client = new oc::ApiClient();
    std::string authToken = client->user()->login("my-username", "my-password");
    client->setBearerToken(authToken);
    
    oc::UserAccount myAccount = client->user()->getUser("my-account-id");
    std::cout << "My name: " << myAccount.name << std::endl;
```

### Authenticating

Authentication is managed using Javascript Web Tokens (JWT). You can either supply your own valid JWT directly, or use
the `UserClient::login(std::string username, std::string password)` method to fetch a new one. Once a token is acquired,
pass it to the `ApiClient::setBearerToken(std::string token)` method, so it can be used on subsequent requests.

```c++
    oc::ApiClient* client = new oc::ApiClient();

    // Authenticate by getting a fresh token
    std::string authToken = client->user()->login("my-username", "my-password");
    client->setBearerToken(authToken);
    
    // Or use an existing one. In this example, we pull from the CLI arguments:
    std::string authToken = argv[1];
    client->setBearerToken(authToken);
```

### User Administration

All user profile and related functionality is performed by the User API. In this SDK, that is a member of the base
`ApiClient` class: `ApiClient::user`. Here, you can fetch user information (`UserClient::getUser`), confirm accounts
(`UserClient::confirmUser`), and other similar functionality. 

### Policy Management

All things relating to policy management are performed by the Policy API, which is accessible through
`ApiClient::policy`. Here, you can fetch policies (`PolicyClient::getPolicies`), create new policies
(`PolicyClient::createPolicy`), and manage the subscription, unsubscription, and approval workflows
(`PolicyClient::subscribePolicy`, `PolicyClient::unsubscribePolicy`, `PolicyClient::approveCreatedPolicy`).

### Data Interfacing and Policy Decisions

While the User and Policy APIs are useful for managing profile information and policy acceptance workflows, the
Datasource API is where you start interfacing with your data repositories.

### Full Examples

For code examples, see the CMake build target `oc-cpp-sdk-app`. Here are some common workflows, along with sample code:

#### Approve a Datasource
```c++
    std::cout << "Approving Datasource: " << datasourceId << std::endl;
    std::string resultId = client->datasource()->approveDatasource(datasourceId);
    std::cout << "Approved with resulting ID: " << resultId << std::endl;
```

#### Upload a CSV/Excel Document
```c++
    std::ifstream excelStream("excel-path", std::ios_base::binary);
    std::stringstream excelBuffer;
    excelBuffer << excelStream.rdbuf();

    Datasource ds = client->datasource()->createDatasources(excelBuffer, filename, "EXCEL", excelFields);
    std::cout << "Uploaded Excel: " << ds.name << " (id=" << ds.id << ")" << std::endl;
    std::cout << "Resulting datasource object: " << json(ds).dump(4) << std::endl;
    return ds;
```

#### Create a Datasource with a PDF File
```c++
    std::ifstream pdfStream("pdf-path", std::ios_base::binary);
    std::stringstream pdfBuffer;
    pdfBuffer << pdfStream.rdbuf();

    Datasource ds = client->datasource()->createDatasources(pdfBuffer, filename, "PDF", pdfFields);
    std::cout << "Uploaded PDF: " << ds.name << " (id=" << ds.id << ")" << std::endl;
    std::cout << "Resulting datasource object: " << json(ds).dump(4) << std::endl;
```

## Design

The C++ SDK was built to be lightweight, cross-platform, and easy to use. Internally, it makes use of the following
pieces of third party software:

- [cpp-httplib](https://github.com/yhirose/cpp-httplib): A lightweight, cross-platform HTTP/HTTPS library. This is a
header-only library, so it's built right into the source code for this SDK as a private header (not exposed).
- [nlohmann/json](https://github.com/nlohmann/json): JSON serialization/deserialization library that is
demonstrated to be faster than several competitors and easy to use.

## Building and Project Structure

**Note**: make sure you have SSL installed! `apt-get install libssl-dev`

This SDK has two build targets:
- `oc-cpp-sdk`: The library itself, which allows exporting and installing the SDK static library
- `oc-cpp-sdk-app`: A small wrapper executable that is linked to the SDK library. This contains several examples
showing how the SDK can be used in your own project.

Since we're using a CMake project, building is very simple. For example, to build just the SDK library itself:

```shell
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release .. # replace "Release" with "Debug" for the debug version
cmake --build . --target oc-cpp-sdk
```

The resulting library will be built as `./library/oc-cpp-sdk/liboc-cpp-sdk.a`

Or, you can build the wrapper executable, which also builds the library itself:

```shell
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release .. # replace "Release" with "Debug" for the debug version
cmake --build . --target oc-cpp-sdk-app
```

The resulting executing will be built as `./oc-cpp-sdk-app`