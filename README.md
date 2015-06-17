# restful_api_design
A set of rules for restful api design that fits relatively small scale of complexity

Target audience: api designers, app developers

## Restful Design Rules:

Restful is an architecture style. Every organization has its own interpretation. Here is our understanding of it (with a simple conceptual model and a simple set of rules that fits a relatively small scale of complexity).

#### Overall goals of restful api design: 

* the endpoints and access methods (POST, PUT, GET, DELETE) available should reflect the domain logics. By looking at these restful data apis, developers can quickly grasp the kind of resources in this domain;
* with a simple set of rules, it should work with all kinds of data for the domain;
* and for app developers using the restful api, they should be able to come up with the endpoints quickly off the top of their heads without having to reference the api documentation, e.g. there is a strong sense of commonly understood one best way to represent that resource restfully according to our understanding of rest stated here;
* Consistence. The set of rules have to be applied consistent for all resources. We should not use plural sometimes and then singular sometimes, or add verbs arbitrarily.
 
Thus below are our set of rules to follow when coming to restful apis. Please understand them semantically since they represent our understanding of Restful Resource:

* Resource is a collection of things, and we denote it with plural. For example /users instead of /user to represent the users resource. So keep in mind, /users is a resource. When you need to create an item inside the collection, you always post to the collection. For example, make POST request to /users to create a user. To access a single item of the resource, you add the item id after the plural. For example, /users/:user_id for a user inside the users resource. For a single item of the resource, you can do the PUT, DELETE, GET. If you want to search for items of resource, you do GET with the resource, such as do GET request with /users, and you pass relevant parameters.
* There are situations that resource type B is only meaningful when it is associated with resource type A. For this kind of resource, we will have urls like /resourceA/:resourceA_id/resouceBs to represent resource B (let’s call this kind of url Relative Resource Url), instead of using /resourceBs (let’s call this kind of url Absolute Resource Url).  For example, one user’s tags, you use /user/:user_id/tags. For some situations that are very natural and of frequent usage, we will provide the Relative Resource Url. (Which resource is treated as resource A and which is treated as resource B, we will follow the people’s natural perspective. Generally the user perspective is used, e.g. user is often put as resource A.) Otherwise, developers should first think of Absolute Resource Url when trying to find resource.
* Unless really necessary and strongly desired (yeah, in rare cases, treating something as resource might be a little twisted), we try not to append verbs to resource urls, for example /users/register, instead we use the standard CRUD methods to call the resource url for certain actions.
* For some resource, we might provide some extra endpoints for the convenience of reading access. No creation, modification and deletion are allowed for such resource. For example, we can provide /user/:user_id/groups to find out a user’s groups. This is for convenience to find out a user’s group since this is a very common usage. But it is only for reading access. No POST, PUT, or DELETE is allowed for this endpoint. To create an item in such a resource, you make POST request to /group/:group_id/members/
* Resource doesn’t have to implement all CRUD methods. What to implement and what not to reflect the domain logics.
* We try to include parameters as part of the resource url instead of passing a lot of parameters in the http request.
* Except for resource, all nouns inside url need to be in singular form. For example, /user/:user_id/groups, user is in its singular form as groups is the actual resource here. So this is actually the same as the grammar of the English language. Another example, as shown above, is  /group/:group_id/members/, group is in singular form as members is the actual resource here.
* We might provide a special word after the resource, for example, /group/:group_id/members/count to make it easy to find out more properties of the resource.
* All resource endpoint ends with /.
