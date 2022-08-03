## Navigating user permissions with Cerbos.

User permissions (authorizations and authentication) are an indisputable aspect of most systems’ essential operation, and they demand careful consideration in terms of design, structure, and construction.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1659531978505/JDwYLZSAf.png)

Image Source ([Actitime](https://www.actitime.com/wp-content/uploads/2021/06/use-permissions.png))

As software engineers, products like Okta and Auth0 come to mind as tools for navigating user authentications, and then you investigate different authorization policies and models depending on the Infrastructure you want to use, of which ABAC, RABC, LDAP, or SAML (single sign-on authentication) are just a few examples.

#### **Understanding Authorizations and Authentications and why you should care**

Authorization is the process of giving someone the ability to access a piece of information.

Authorization rules are part of the Identity and Access Management group in computing, and we can simply define it as the identification of system managers to control and validate users who have access to system resources and set client privileges.

While User authentication is a security process that prevents unauthorized users from accessing your device or network, It’s a login procedure where an application requests personalized passwords to give you authorized access to it. If a user lacks the proper login rights to the network, their authentication fails.

There is almost no doubt that authorization, as overlooked as it is, is one of the most complicated to get right and a possible future threat if not done correctly since minor errors or misconfiguration might result in millions of user records being exposed.

Sign Language Permission GIF By ISL Connect

While researching the best options for properly navigating user permissions without having to worry about the problems that come with it, one product stood out as the central focus of this article.

[**Cerbos**](http://cerbos.dev) is an open-source decoupled access control for your software making user permissions and authorization simple to implement and manage.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1659531979744/ilKGXcseN.jpeg)

https://cerbos.dev

With cerbos, user permissions become a plug-in solution and you do not have to build from scratch or worry about exposing user details as cerbos provides you with a Low-code, human-readable configuration and goes way beyond basic role-based-access-control with context-aware role definitions and attributes and you can get it implemented and running in minutes.

A typical code without Cerbos:

<iframe src="https://carbon.now.sh/embed?bg=rgba%28213%2C143%2C30%2C1%29&amp;t=blackboard&amp;wt=none&amp;l=auto&amp;width=680&amp;ds=true&amp;dsyoff=20px&amp;dsblur=68px&amp;wc=true&amp;wa=true&amp;pv=56px&amp;ph=56px&amp;ln=false&amp;fl=1&amp;fm=Hack&amp;fs=14px&amp;lh=133%25&amp;si=false&amp;es=2x&amp;wm=false&amp;code=if%2520user.email.endswitch%28%2522%2540samuelakinosho.com%2522%29%2520or%2520%28%250A%2520%2520%2520%2520%2522developers%2522%2520in%2520user.groups%2520and%2520user.company.package%2520%253D%253D%2520%2522allowance%2522%250A%29%253A%250A%2520%2520%2520%2520if%2520user.region%2520%253D%253D%2520resource.region%253A%250A%2520%2520%2520%2520%2520%2520%2520%2520audit_log.record%28Audit.ALLOWED%252C%2520%2522edit%2522%252C%2520user%252C%2520resource%29%250A%2520%2520%2520%2520else%253A%250A%2520%2520%2520%2520%2520%2520%2520%2520audit_log.record%28Audit.DENIED%252C%2520%2522edit%2522%252C%2520user%252C%2520resource%29%250A%2520%2520%2520%2520else%253A%250A%2520%2520%2520%2520%2520%2520%2520%2520audit_log.record%28Audit.DENIED%252C%2520%2522edit%2522%252C%2520user%252C%2520resource%29" width="700" height="328" frameborder="0" scrolling="no"></iframe>

With Cerbos:

<iframe src="https://carbon.now.sh/embed?bg=rgba%28213%2C143%2C30%2C1%29&amp;t=blackboard&amp;wt=none&amp;l=python&amp;width=680&amp;ds=true&amp;dsyoff=20px&amp;dsblur=68px&amp;wc=true&amp;wa=true&amp;pv=56px&amp;ph=56px&amp;ln=false&amp;fl=1&amp;fm=Hack&amp;fs=14px&amp;lh=133%25&amp;si=false&amp;es=2x&amp;wm=false&amp;code=if%2520cerbos_client.is_allowed%28%2522edit%2522%252C%2520user%252C%2520resource%29%250A%2523Access%2520granted" width="700" height="328" frameborder="0" scrolling="no"></iframe>

#### How Cerbos Works

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1659531981076/FJ7jqDz-Vv.jpeg)

So let’s start with the basics of this diagram, which shows how your application would interact with Cerbos.

When a user performs an action, the application layer unbundles that request to determine who is attempting to perform what action and on which resource or object. The application layer then unbundles these two data points further.

The first is the user’s identity, which could include things like the groups the user is a part of or the departments and or geographies it belongs to; the second is the resource or object that is being accessed or mutated; the application knows what state that object is in and any additional information about its attributes at this point in order to make a decision whether or not that activity should be permitted.

Traditionally, you would construct an if then else block that implements a conditional branch depending on all of the preceding bits of information we discussed. Cerbos decouples and centralizes that logic out of your application code, as seen in this figure. The call to the SDK would provide information about the user action and the resource, and it would ask Cerbos to decide whether or not that action should be allowed. The SDK interacts with the servos instance running within your environment via the rest API or GRPC based on the policies defined.

The application that uses the Cerbos SDK would implement a very simple API for two different courses one for action allowed and another for action denied. This way, you would not have to change your application code when the rules of what should be allowed change.

#### Installing and using the Cerbos API

To install cerbos using Docker (run from a container):

docker run --rm --name cerbos -p 3592:3592 ghcr.io/cerbos/cerbos:0.17.0

You should see something like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1659531982595/o7pPhbdG0.png)

Screenshot from running Cerbos with a container

Creating a directory for the policies and config files

mkdir -p cerbos-quickstart/policies

Creating a config file

policies  
cat > cerbos-quickstart/conf.yaml <<EOF  
server:  
 httpListenAddr: “:3592”  
 storage:  
 driver: “disk”  
 disk:  
 directory: /quickstart/policies  
 watchForChanges: true  
EOF

Launching the container with the config files

docker run — rm — name cerbos -d -v $(pwd)/cerbos-quickstart:/quickstart -p 3592:3592 ghcr.io/cerbos/cerbos:0.17.0 server — config=/quickstart/conf.yaml

A screenshot of what you would have running on your Docker desktop client

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1659531984040/RaLrLSZsR.png)

You can view the latest API documentation from a running Cerbos instance by running:

```
docker run --rm --name cerbos -p 3592:3592 -p 3593:3593 ghcr.io/cerbos/cerbos:0.17.0
```

or you can easily navigate to [http://localhost:3592/](http://localhost:3592/) using your browser.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1659531985416/NnlCkOyJ8.png)

Screenshot of the Cerbos API docs on localhost

#### Demo Implementation

To test out the Cerbos practical Demo, you can begin by cloning any of these examples depending on your preferred languages:

*   [Application (Python)](https://github.com/cerbos/demo-python)
*   [GraphQL Service (NodeJS)](https://github.com/cerbos/demo-graphql)
*   [REST Service (Go)](https://github.com/cerbos/demo-rest)

We would use the [Application (Python)](https://github.com/cerbos/demo-python) and the following calls in the application to query the Cerbos PDP and make policy decisions. To get started, you can use the Cerbos PDP hosted demo instance.  
*Note: If you’re deploying for production use cases, a self-hosted instance should be deployed for security and latency reasons.*

Cerbos PDP Instance: [https://demo-pdp.cerbos.cloud](https://demo-pdp.cerbos.cloud)

To install the Cerbos NPM package

```
npm i @cerbos/sdk  
// or  
yarn add @cerbos/sdk
```

Configuring the Cerbos Client

<iframe src="https://carbon.now.sh/embed?bg=rgba%28213%2C143%2C30%2C1%29&amp;t=blackboard&amp;wt=none&amp;l=python&amp;width=680&amp;ds=true&amp;dsyoff=20px&amp;dsblur=68px&amp;wc=true&amp;wa=true&amp;pv=56px&amp;ph=56px&amp;ln=false&amp;fl=1&amp;fm=Hack&amp;fs=14px&amp;lh=133%25&amp;si=false&amp;es=2x&amp;wm=false&amp;code=import%2520%257B%2520Cerbos%2520%257D%2520from%2520%2522%2540cerbos%252Fsdk%2522%253B%250A%250Aconst%2520cerbos%2520%253D%2520new%2520Cerbos%28%257B%250A%2520%2520hostname%253A%2520%2522https%253A%252F%252Fdemo-pdp.cerbos.cloud%2522%252C%2520%252F%252F%2520The%2520Cerbos%2520PDP%2520instance%250A%2520%2520playgroundInstance%253A%2520%2522UWG3inHjwrFhqkv60dec623G9PoYlgJf%2522%2520%250A%257D%29%253B" width="700" height="328" frameborder="0" scrolling="no"></iframe>

Then wherever is needed in the business logic, you can call Cerbos to check the authorization.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1659531986851/PThl8hPrC.png)

Alternatively, you can watch this tutorial to see Cerbos policies in action.

<iframe src="https://www.loom.com/embed/0425d8a075804d528185ad2ba30817b3" width="700" height="525" frameborder="0" scrolling="no"></iframe>

#### Conclusion:

In this article, I discussed the issues with building user permissions from scratch and how Cerbos can assist developers to replace complicated, hardcoded permissions logic with a simple API request. I also gave code snippets for running Cerbos, integrating the APIs, and seeing Cerbos policies in action.