# restful_api_design

Target audience: api designers, app developers

Restful is an architecture style. Every organization has its own interpretation. Here is our understanding of it (with a simple conceptual model and a simple set of rules that fits a relatively small scale of complexity).


## Overall goals of restful api design: 

* the endpoints and access methods (POST, PUT, GET, DELETE) available should reflect the domain logics. By looking at these restful data apis, developers can quickly grasp the kind of resources in this domain;
* with a simple set of rules, it should work with all kinds of data for the domain;
* and for app developers using the restful api, they should be able to come up with the endpoints quickly off the top of their heads without having to reference the api documentation, e.g. there is a strong sense of commonly understood one best way to represent that resource restfully according to our understanding of rest stated here;
* consistence. The set of rules have to be applied consistent for all resources. We should not use plural sometimes and then singular sometimes, or add verbs arbitrarily.
 

## A set of rules on restful urls:

Thus below are our set of rules to follow when coming to restful apis. Please understand them semantically since they represent our understanding of Restful Resource:
* Resource is a collection of things, and we denote it with plural. For example /users instead of /user to represent the users resource. So keep in mind, /users is a resource. When you need to create an item inside the collection, you always post to the collection. For example, make POST request to /users to create a user. To access a single item of the resource, you add the item id after the plural. For example, /users/:user_id for a user inside the users resource. For a single item of the resource, you can do the PUT, DELETE, GET. If you want to search for items of resource, you do GET with the resource, such as do GET request with /users, and you pass relevant parameters.
* There are situations that resource type B is only meaningful when it is associated with resource type A. For this kind of resource, we will have urls like /resourceA/:resourceA_id/resouceBs to represent resource B (let’s call this kind of url Relative Resource Url), instead of using /resourceBs (let’s call this kind of url Absolute Resource Url).  For example, one user’s tags, you use /user/:user_id/tags. For some situations that are very natural and of frequent usage, we will provide the Relative Resource Url. (Which resource is treated as resource A and which is treated as resource B, we will follow the people’s natural perspective. Generally the user perspective is used, e.g. user is often put as resource A.) Otherwise, developers should first think of Absolute Resource Url when trying to find resource.
* Unless really necessary and strongly desired (yeah, in rare cases, treating something as resource might be a little twisted), we try not to append verbs to resource urls, for example /users/register, instead we use the standard CRUD methods to call the resource url for certain actions.
* For some resource, we might provide some extra endpoints for the convenience of reading access. No creation, modification and deletion are allowed for such resource. For example, we can provide /user/:user_id/groups to find out a user’s groups. This is for convenience to find out a user’s group since this is a very common usage. But it is only for reading access. No POST, PUT, or DELETE is allowed for this endpoint. To create an item in such a resource, you make POST request to /group/:group_id/members/
* Resource doesn’t have to implement all CRUD methods. What to implement and what not to reflect the domain logics.
* We try to include parameters as part of the resource url instead of passing a lot of parameters in the http request.
* Except for resource, all nouns inside url need to be in singular form. For example, /user/:user_id/groups, user is in its singular form as groups is the actual resource here. So this is actually the same as the grammar of the English language. Another example, as shown above, is  /group/:group_id/members/, group is in singular form as members is the actual resource here.
* We might provide a special word after the resource, for example, /group/:group_id/members/count to make it easy to find out more properties of the resource, especially when there are very frequent requests on the count of the resource
* All resource endpoint ends with /.

## Other general restful design concerns:

* Support of PATCH: for now, to make things simple, No PATCH method is supported. Only PUT will be used for updating a resource
* What to return for each HTTP request method
* Embedded Resource Serialization
* Soft Delete
* Support of meta info, such as limit, offset, page, page_size, order, sort
* All apis use https
* Versioning
* Hypermedia support
* Token to oauth2

**What to return for each HTTP request method**

GET /collection  return a list of resource items 
GET /collection/:item_id    return a single item of resource 
POST /collection  return the newly created resource item
PUT /collection/:item_id  return the complete updated resource item
PATCH /collection/:item_id   return the complete updated resource item 
DELETE /collection/:item_id return empty document

Return above are for when the request is successfully executed. The http response status code for such cases is 200.  For other cases such as not found, or server error, each specific  http response status code will be returned, and the detailed error msg might be provided to help the client determine what went wrong.

For a reference, here is a list of specific http response status code to return:

200 OK - [GET]: successfully return the resource that client requested. The operation is idempotent.
201 CREATED - [POST/PUT/PATCH]: successfully created or modified resource.
202 Accepted - [*]: the request has entered the back end queue (asynchronous tasks).
204 NO CONTENT - [DELETE]: successfully deleted the resource.
400 INVALID REQUEST - [POST/PUT/PATCH]: the client sent a wrong request. The server didn't do creation or modification operation. The operation is idempotent.
401 Unauthorized - [*]: user has no permission (token, username, or passwd error).
403 Forbidden - [*]: user has permission, but no right on this resource.
404 NOT FOUND - [*]: No resource found. The operation is idempotent.
406 Not Acceptable - [GET]: no requested format for the client. For example, XML is requested, but only JSON is available.
409 Conflict - [POST/PUT/PATCH]: the request could not be processed because of conflict in the request, such as an edit conflict in the case of multiple updates, or insert that vioate unique key constraint.
500 INTERNAL SERVER ERROR - [*]: internal server error.

For a complete list of http status code, see https://en.wikipedia.org/wiki/List_of_HTTP_status_codes



**Embedded Resource Serialization**

If a document field is referencing a document in another resource, clients can request the referenced document to be embedded within the requested document. Clients have the power to activate document embedding on per-request basis by means of a query parameter. Embedded resource serialization is available at both resource and item endpoints.


**Soft Delete**

The framework should have support on Soft Delete.


**Support of meta info, such as limit, offset, page, page_size, order, sort**

For GET on a resource, a list of resource items will be returned. Together we will also return the meta info associated with the returned list, namely limit, offset, page, page_size, order, sort. And the client can pass these as parameters in the request to get the resource accordingly.


**All apis use https**

All apis should use https and only allow https access. 


**Versioning**

For simplicity, we now use the strategy of putting the version number into the api url. For example, https://api.example.com/v1/, instead of passing version number in http requst header.


**Hypermedia support**

Hypermedia support can be provided to allow client to explore the apis without looking up the apis documentation.


**Token to oauth2**

Initially, for simplicity token can be used for api request authentication and authorization. Later oauth2 can be used for better security.

## Reference:

Architectural Styles and the Design of Network-based Software Architectures, DISSERTATION by Roy Thomas Fielding   http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm

Python Eve:  http://python-eve.org/features.html

HATEOAS - Wikipedia, the free encyclopedia   https://en.wikipedia.org/wiki/HATEOAS

Flask-RESTful  https://flask-restful.readthedocs.org/

django-rest-framework  http://www.django-rest-framework.org/  

Below are Chinese only:

理解RESTful架构 - 阮一峰的网络日志  http://www.ruanyifeng.com/blog/2011/09/restful.html

RESTful API 设计指南 - 阮一峰的网络日志  http://www.ruanyifeng.com/blog/2014/05/restful_api.html
