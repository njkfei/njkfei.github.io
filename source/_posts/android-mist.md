title: android杂项
date: 2016.02.17
categories: 服务 #分类
tags: [android ]
---
  

### CONSUMING REST API WITH RETROFIT 2.0 IN ANDROID
[参考](http://www.iayon.com/consuming-rest-api-with-retrofit-2-0-in-android/)
[类库](http://square.github.io/)
[主页](http://square.github.io/retrofit/)
Retrofit is a well-known open source networking library for Android platform from Square. It's one of their very popular open source libraries in different platform and widely used by Android developers for it's simplicity of use and faster performance than other available libraries used in network operations within Android apps.

Current version of Retrofit is 2.0 . It's still in Beta and published recently. So, in this moment there's not too many resources available in internet for this latest version. There have been a number of big changes in this latest version comparing to version 1.9.0 which has been being in play for a while. So, this post is both for those who are thinking to use Retrofit in their next Android project and also those who wants to migrate to latest version of Retrofit from existing older versions. So, here we go...

ADDING DEPENDENCIES

Anyway, let's begin with creating a new project in Android studio and open it's build.gradle file. We need to add 3 dependencies in our build.gradle file. It needs some explanation because we didn't need all those in previous versions.

retrofit : It's our main required library that will do our networking jobs.

okhttp : okhttp is another http client library from Square. It does the http requests, sets headers and process responses etc under the hood. It was not essential in the previous versions. In fact, we were able to use some other http clients instead of okhttp within Retrofit. But in latest 2.0 , it has been added as a dependency.

converter-gson : We know Gson is a library that can convert JSON objects to Java Objects and vice-versa. This converter-gson is used in Retrofit to convert http Responses into Java Objects. So, we get some hint that we'll get API responses as our pre-defined Java Objects.

So, finally after adding the above dependencies, our build.gradle dependencies will look like this

compile 'com.squareup.retrofit:retrofit:2.0.0-beta1'  
compile 'com.squareup.okhttp:okhttp:2.4.0'  
compile 'com.squareup.retrofit:converter-gson:2.0.0-beta1'  
CREATE RESPONSE EQUIVALENT POJO CLASS

For this example, we'll use a simple Github API endpoint that searches a Name (say, tom) in Github. This api returns JSON response like following:

{
  "total_count": 29409,
  "incomplete_results": false,
  "items": [
    {
      "login": "tom",
      "id": 748,
      "avatar_url": "https://avatars.githubusercontent.com/u/748?v=3",
      "gravatar_id": "",
      "url": "https://api.github.com/users/tom",
      "html_url": "https://github.com/tom",
      "followers_url": "https://api.github.com/users/tom/followers",
      "following_url": "https://api.github.com/users/tom/following{/other_user}",
      "gists_url": "https://api.github.com/users/tom/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/tom/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/tom/subscriptions",
      "organizations_url": "https://api.github.com/users/tom/orgs",
      "repos_url": "https://api.github.com/users/tom/repos",
      "events_url": "https://api.github.com/users/tom/events{/privacy}",
      "received_events_url": "https://api.github.com/users/tom/received_events",
      "type": "User",
      "site_admin": false,
      "score": 56.1691
    }]
}
Now we have to write Java POJO Class equivalent to the above JSON Object. We can either write it ourself or there are many ways to generate it. I often use jsonschema2pojo to do such POJO class generation. It can generate POJO classes from JSON schema or JSON object. As it's an easy operation, so we can skip this here.

CONFIGURE RETROFIT

Here comes the most important part that have some difference from the previous versions of Retrofit.

DEFINE ENDPOINTS

The most amazing feature of Retrofit is, We can define all the Request parameters, headers, Request Methods etc with special Retrofit annotations. And of course, with minimal amount of code. So, let's see how to do it.

We have to define all our API endpoints in an interface like the following.

public interface GitApiInterface {

        @GET("/search/users")
        Call<GitResult> getUsersNamedTom(@Query("q") String name);

        @POST("/user/create")
        Call<Item> createUser(@Body String name, @Body String email);

        @PUT("/user/{id}/update")
        Call<Item> updateUser(@Path("id") String id , @Body Item user);
    }
Here, each endpoint specifies an annotation of the HTTP method (GET, POST, PUT, DELETE etc.). And each of these methods will be used to initiate the network call. In each method, we see some annotations about the parameters. The annotation @Query specifies a query parameter in the url (like, http://base_url?sort=desc&limit=20).

When @Path is used, that means there's a variable parameter in the Url that will be replaced by the Argument supplied with the method call. In the above example, We see it in the PUT request where we call the updateUser method with userId which will be replaced here /user/{id}/update at the place of {id} at runtime. @Body annotation also about parameters that are sent with Request body in POST/PUT requests. More details will be found in Retrofit Documentation.

Call instances in the above methods are new in Retrofit. In this latest version, it's required to initiate the request. If it's a synchronous request, then it's fired by execute() method, for asynchronous calls enqueue method is used. We'll see a bit later.

SETUP RETROFIT

Retrofit is the Class that makes our API interfaces into Callable objects. Remember, in the previous versions, RestAdapter Class was to used do this similar task. But in the version 2.0, there is no RestAdapter and many of it's methods. We'll use Retrofit Class to do the same.

We can simply initiate Retrofit like this.

private String baseUrl = "https://api.github.com" ;  
Retrofit client = new Retrofit.Builder()  
                    .baseUrl(baseUrl)
                    .build()
But in our application, we need to configure some more things for some reason.

CONVERTERS

By default, Retrofit can only deserialize http Response into okhttp ResponseBody, so we need some way to get this Response as serialized Java Object. So, we'll require Converters. Currently Retrofit supports 6 type of converters, we can use any of the following.

Gson: com.squareup.retrofit:converter-gson
Jackson: com.squareup.retrofit:converter-jackson
Moshi: com.squareup.retrofit:converter-moshi
Protobuf: com.squareup.retrofit:converter-protobuf
Wire: com.squareup.retrofit:converter-wire
Simple XML: com.squareup.retrofit:converter-simplexml
In this example, we are using converter-gson. So, let's add .addConverterFactory(GsonConverterFactory.create()) before .build() method in our Retrofit builder. Now, we'll get Converted and serialized Response.

USING REQUEST INTERCEPTORS

Retrofit used to provide a Class called RequestInterceptor to intercept a request. But It's not available in version 2.0 as all it's HTTP connection layer has been moved to okhttp. Here's an example how to use a RequestInterceptor.

OkHttpClient okClient = new OkHttpClient();  
okClient.interceptors().add(new Interceptor() {  
            @Override
            public Response intercept(Chain chain) throws IOException {
                Response response = chain.proceed(chain.request());
                return response;
            }
        });

Retrofit client = new Retrofit.Builder()  
                .baseUrl(baseUrl)
                .client(okClient)
.addConverterFactory(GsonConverterFactory.create())
                .build();
So, in this stage, we are all done with configuring Retrofit for our application.

MAKING NETWORK REQUESTS

Now, we'll move to our application logic. Let's add necessary permission to our AndroidManifest.xml

<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>  
Open our MainActivity.java file. We'll call the methods from GitApiInterface from onCreate method of our Activity.

GitApiInterface service = client.create(GitApiInterface.class);  
Call<GitResult> call = service.getUsersNamedTom("tom");  
call.enqueue(new Callback<GitResult>() {  
     @Override
     public void onResponse(Response<GitResult> response) {
         if (response.isSuccess()) {
             // request successful (status code 200, 201)
             GitResult result = response.body();
         } else {                   
              //request not successful (like 400,401,403 etc)
              //Handle errors
         }
     }

     @Override
     public void onFailure(Throwable t) {

     }
});
SAMPLE CODE

I have written a simple Android app that demonstrates everything I have described above.It can be found in this Github Repository.

CONCLUSION

Retrofit 2.0 definitely has come with some big changes. For more detail on changes, please see their ChangeLog on Github.