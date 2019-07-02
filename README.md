# Logging and Debugging ASP.NET Core with Application Insights pt.1

## Series Part 1 Setting up App Insights and Viewing Logs

App Insights gives us the ability to view information about our app like Requests, Log Traces, Exceptions, Performance metrics, etc. Its honestly pure magic to me and one easiest and best ways to help you debug your Web App.

We'll start with the basics here. Let's hook up a basic ASP.NET Core Web API with Application Insights.

## Create an Application Insights resource

We will start off by creating the App Insights resource in Azure.

In the Azure Portal, create the application insights resource. If you are a newbie to Azure or just need more information refer to this document https://docs.microsoft.com/en-us/azure/azure-monitor/app/create-new-resource

I usually create and name an App Insights Resource for each stage of the development process.
So I follow the convention of {app-name}-{environment}

For example I would create 4 individual instances

* AspNetCoreAppInsightsSeries-local
* AspNetCoreAppInsightsSeries-development
* AspNetCoreAppInsightsSeries-staging
* AspNetCoreAppInsightsSeries-production

For now, we really just need AspNetCoreAppInsightsSeries-local as we will just run the API locally.
Create the ASP.NET Core Web App.

We will keep it simple so simply create a new ASP.NET Web API in Visual Studio.
This give us a nice basic endpoint we can hit - https://localhost:44326/api/values 
(Your localhost port will, of course, be different)


## Hook up the Web App to App Insights

Add the NuGet package 

`Microsoft.ApplicationInsights.AspNetCore`

Go the Startup class and under ConfigureServices add the line 

`services.AddApplicationInsightsTelemetry();`

This will, as per the documentation - "Adds Application Insights services into service collection."
Next, add the App Insights Instrumentation Key to the app settings config file appsettings.json. This is the key that will determine as to which instance your telemetry data should be sent to.

And really that's all there is to it. Your app should now start sending telemetry data to Application Insights.

## Let's test it out.

Start your app and call a GET request to https://localhost:44326/api/values.

This should trigger our app to send some data to AppInsights

We can actually view the some of the data in our debug output window

Check how this looks in the App Insights Page

We can see that we are getting some data in!

Out of the overview page alone we can see the number of requests, response times, and any failed requests. (Notice the failed request as well, we will come back to that later)

Let view our Logs. On the left pane, click "Search" and hit "refresh". Here we can see the log of our Values/Get Request.

Clicking on that entry will actually display the End-to-end transaction details of that request.

So if you had downline dependencies, log traces, exceptions, etc within that request those will show up on this here. However since our api/values/Get call doesn't really have anything, this page is pretty empty at the moment.

Here is a real life example of how it might look in a production environment

## Wait, what was that failed request?

Lets actually go ahead and examine that. On the left side pane under Investigate click on Failure.

On this page you can see that the failed operation was a GET /favicon.ico.

This makes sense, when the browser hit api/values/Get it tried to download the favicon to display on the browser tab but since we never added one, the server returned a "404 - Not Found"

## Conclusion

It's rather easy to hook ASP.NET Core apps to application insights which provide invaluable traces and metrics as to how your app performs. So take advantage of this. App Insights has saved me countless of hours debugging my web applications, whereas in the past I would have had to hunt down the logstore and gruelingly figure out what went wrong.

Anyway, this concludes part 1 of this series. In the next part of the series, we will discuss how to send log traces to Application Insights.
