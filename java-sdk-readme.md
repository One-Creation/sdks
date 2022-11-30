# One Creation Java SDK
This is the first official SDK of the O.C. platform. It is a lightweight fully featured 
implementation of the API with custom error handling and examples provided.

## V 0.1.0
This version does do not include all features of the API, but rather just what is needed to get up 
and running with the application. The initial release will be to used get improvement points on the 
current subset of functionality which will be used to better the rest of the endpoints, before 
finally releasing the entire feature suite officially.

- user management
    - user registration
    - user confirmation
    - user invitation
    - user retrieval
- datasource management
    - file upload
    - database linking
      - postgres
      - mysql
      - oracle
      - snowflake
      - redshift
    - datasource creation approval
    - datasource creation rejection
    - datasource retrieval
    - datasource deletion
- policy management
  - policy CRUD
  - policy creation approval
  - policy creation rejection
  - policy update approval
  - policy update rejection
  - policy subscription
  - policy subscription cancellation
  - policy subscription approval
  - policy subscription rejection

Features not explicitly included:
- retrieving all users
- groups CRUD
- participant CRUD
- organization CRUD
- KX DB datasource usage
- AWS S3 datasource usage
- Databricks datasource usage
- Microsoft SharePoint datasource usage
- policy file downloading, decryption, de-listing, usage reporting, previewing

Some functionalities were low priority, while others were blocked due to timeline changes (e.g, 
policy payload enhancement), while others simply did not work as intended are lacked good design 
and thus would be troublesome for the end user. To follow this up with evidence: fetching all 
users does not filter based on organization and a user from one random organization has visibility 
into another without explicit consent. Grouping logic is not soundly tested and there's no limit 
to the amount of groups a user can be nested in, causing a potential JVM overhead for the platform. 
Organizations are created on user creation and the org id is available via user management methods. 
It's not worth the extra SDK work. KX is a low priority use-case and untested. AWS S3 does not have 
custom error handling on our backend; we are showing Amazon errors instead to our API users. The 
payload provided from the UI is specific to AWS and not One Creation, not sure if this is decision 
we want to force on our SDK users: making them depend on AWS libraries? This needs to be vetted a 
bit more. Databricks is low priority. SharePoint (among most of these datasources) is being handled 
by new contribution service, and I do not want to do double the work since it is a relatively new 
and refined group of endpoints. Policy controller has many endpoints which could belong to a 
different domain altogether (such as Zohar's suggestion for time-bombing). Additionally, policy 
payload was supposed to be updated before SDK completion, but now I'm left supporting two 
incomplete versions. Speaking of disjointness, datasource usage is still a mess that could've been 
designed better. We currently have 3 different datasource request types and all of them filter 
back into one massive confusing response that contains many null values from irrelevant fields. 

## Why create an SDK?
- allows for sane defaults (e.g: application/json)
- error handling built-in
- validate required fields
- only known methods/content
- ability to easily expand on functionality based on client needs
- enforce best practices and patterns (e.g: methods are self-documenting)
- safe and optimized with good defaults and limits
- fast and simple adoption (one-line access to API)
- lean on language features (throwing exceptions, strong typing, optionals)
- easy to consume knowledge without in-depth knowledge
- adjust to use-cases (e.g: combining multiple calls into a single method)
- nobody likes implementing an HTTP client every time

## Tips on writing an SDK
- start with the API's language
- avoid unnecessary dependencies
- add other languages to meet popularity/demand
- backwards compatibility

We wrote our backend in Java, it makes sense to develop an SDK in the same language.

We use bloated dependencies such as Spring for our controllers, but we can use the built-in HTTP 
clients from the standard Java library. This is just one example to keep the SDK light. However, 
that same native library did not support multipart data, so instead of downloading a large MIME 
type library, a custom class was written to handle the circumstances around One Creation's usage; 
thus still keeping the application as small as possible.

Python and JS/TS are popular languages, we should accommodate them. This will encourage growth.

We are never sure what version the client is using, and we can't force an upgrade on them. It would 
be terrible if we updated the SDK and suddenly half our users weren't able to perform basic ops.
