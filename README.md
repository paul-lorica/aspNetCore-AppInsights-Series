# Logging and Debugging ASP.NET Core with Application Insights pt.1

## Series Part 1 Setting up App Insights and Viewing Logs

App Insights gives us the ability to view information about our app like Requests, Log Traces, Exceptions, Performance metrics, etc. Its honestly pure magic to me and one easiest and best ways to help you debug your Web App.

We'll start with the basics here. Let's hook up a basic ASP.NET Core Web API with Application Insights.

## Create an Application Insights resource

We will start off by creating the App Insights resource in Azure.

![2019-06-28_10-39-52](https://user-images.githubusercontent.com/27027488/60522937-7bb24000-9caf-11e9-937c-40be2bec374b.png)

In the Azure Portal, create the application insights resource. If you are a newbie to Azure or just need more information refer to this document https://docs.microsoft.com/en-us/azure/azure-monitor/app/create-new-resource

I usually create and name an App Insights Resource for each stage of the development process.
So I follow the convention of {app-name}-{environment}

For example I would create 4 individual instances

* AspNetCoreAppInsightsSeries-local
* AspNetCoreAppInsightsSeries-development
* AspNetCoreAppInsightsSeries-staging
* AspNetCoreAppInsightsSeries-production

For now, we really just need AspNetCoreAppInsightsSeries-local as we will just run the API locally.

![2019-06-28_10-03-28](https://user-images.githubusercontent.com/27027488/60522716-21b17a80-9caf-11e9-96e5-1a331b99dfa4.png)

Once the instance is created, copy the instrumentation key as we will need this later.

![2019-06-28_10-05-51](https://user-images.githubusercontent.com/27027488/60524026-6b9b6000-9cb1-11e9-96c4-51d2f99bd67e.png)

## Create the ASP.NET Core Web App.

![2019-06-28_9-45-47](https://user-images.githubusercontent.com/27027488/60522552-d5fed100-9cae-11e9-94d0-448b1eb50831.png)

We will keep it simple so simply create a new ASP.NET Web API in Visual Studio.
This give us a nice basic endpoint we can hit - https://localhost:44326/api/values 
(Your localhost port will, of course, be different)


## Hook up the Web App to App Insights

Add the NuGet package 

`Microsoft.ApplicationInsights.AspNetCore`

![2019-06-28_9-50-26](https://user-images.githubusercontent.com/27027488/60522662-0a728d00-9caf-11e9-949e-92526d891281.png)

Go the Startup class and under ConfigureServices add the line 

`services.AddApplicationInsightsTelemetry();`

![2019-06-28_9-53-01](https://user-images.githubusercontent.com/27027488/60522691-178f7c00-9caf-11e9-9c16-6e17b53398e0.png)

This will, as per the documentation - "Adds Application Insights services into service collection."

Next, add the App Insights Instrumentation Key to the app settings config file appsettings.json. This is the key that will determine as to which instance your telemetry data should be sent to.

![2019-06-28_10-06-31](https://user-images.githubusercontent.com/27027488/60522743-2e35d300-9caf-11e9-9b1e-4f955bbd86a8.png)

And really that's all there is to it. Your app should now start sending telemetry data to Application Insights.

## Let's test it out.

Start your app and call a GET request to https://localhost:44326/api/values.

This should trigger our app to send some data to AppInsights

We can actually view the some of the data in our debug output window

![2019-06-28_10-17-57](https://user-images.githubusercontent.com/27027488/60522888-6b9a6080-9caf-11e9-8e2c-df6bc950f346.png)

Check how this looks in the App Insights Page

We can see that we are getting some data in!

Out of the overview page alone we can see the number of requests, response times, and any failed requests. (Notice the failed request as well, we will come back to that later)

![2019-06-28_10-26-57](https://user-images.githubusercontent.com/27027488/60524053-79e97c00-9cb1-11e9-97fe-e6b9a2d8ccd6.png)


Let view our Logs. On the left pane, click "Search" and hit "refresh". Here we can see the log of our values/Get Request.

![2019-06-28_10-13-27](https://user-images.githubusercontent.com/27027488/60522834-54f40980-9caf-11e9-8b94-c8548b60aaa2.png)

Clicking on that entry will actually display the End-to-end transaction details of that request.

![2019-06-28_14-06-05](https://user-images.githubusercontent.com/27027488/60523014-997fa500-9caf-11e9-9906-17e79d97292c.png)

So if you had downline dependencies, log traces, exceptions, etc within that request those will show up on this here. However since our api/values/Get call doesn't really have anything, this page is pretty empty at the moment.

Here is a real life example of how it might look in a production environment

![2019-06-28_14-04-15](https://user-images.githubusercontent.com/27027488/60522971-8967c580-9caf-11e9-9aba-4f596cadd9a0.png)


## Wait, what was that failed request?

Lets actually go ahead and examine that. On the left side pane under Investigate click on Failure.

On this page you can see that the failed operation was a GET /favicon.ico.

![2019-07-02_9-42-01](https://user-images.githubusercontent.com/27027488/60523033-a3090d00-9caf-11e9-8582-0986a823889b.png)

This makes sense, when the browser hit api/values/Get it tried to download the favicon to display on the browser tab but since we never added one, the server returned a "404 - Not Found"

## Conclusion

It's rather easy to hook ASP.NET Core apps to application insights which provide invaluable traces and metrics as to how your app performs. So take advantage of this. App Insights has saved me countless of hours debugging my web applications, whereas in the past I would have had to hunt down the logstore and gruelingly figure out what went wrong.

Anyway, this concludes part 1 of this series. In the next part of the series, we will discuss how to send log traces to Application Insights.
