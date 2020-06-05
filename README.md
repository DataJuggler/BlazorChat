# BlazorChat
Blazor Chat was originally a sample project of mine DataJuggler.Blazor.Components, but I decided it will be easier to maintain in its own project.

<br>
<img src=https://github.com/DataJuggler/SharedRepo/blob/master/Shared/Images/BlazorChatOwnRepo.png>
<br>

To run this sample you will need SQL Server or SQL Server Express.

<b>Step 1:</b> In SQL Server Management Studio, create a database named BlazorChat.

<b>Step 2:</b> Execute the SQL File located in the SQLScripts folder of this repo. This will create the Tables and Stored Procedures
used by this project. If you want to learn how to create your own stored procedure powered data tiers, clone my project DataTier.Net:
https://github.com/DataJuggler/DataTier.Net 

Or here is a video on it, although I am overdue to make a new one.

https://youtu.be/sowTLLeAfm8

Please subscribe to my YouTube channel.

<b>Step 3:</b> Create a System Environment Variable with the following values:

Name: BlazorChat

Value: Paste in the connection string to your BlazorChat database created in step 1. 

My connection to SQL Server Express looks like this using Windows Authentication

Data Source=[ServerName]\SQLExpress;Initial Catalog=BlazorChat;Integrated Security=True

Replace ServerName with the name of your SQL Server Instance. The easiest way to get your server name is login
to SQL Server Management Studio, and copy the User Name it tries to log you in with, if you have it saved.

My open source project DataTier.Net mentioned in Step 2 comes with a Connection String Builder app, located in the Tools folder:

<br>
<img src=https://github.com/DataJuggler/DataTier.Net/blob/master/DataTier.Net/Class%20Room/Documents/Build%20Connection%20String.png>
The image here shows the database for DataTier.Net. Change the Server Name to your Server name and set the database name to BlazorChat.<br><br>
<br>
<b>Step 4:</b> Run the App. I use Chrome as my main debugging app for Visual Studio, then I launch an Edge
and a Fire Fox instance if I want 3 users to test on my local machine.<br>

<b>Known issues:</b>

<b>1. Periodic Dispose</b>
I added a dispose method, and in theory when the user closes their browser, this should unsubscribe.
A few days ago I had a power failure during a storm, and I am fairly sure during a hard close no code is going to
execute without power to the browser, so a "Remove If Idle" method is probably needed. Where each user
would be pinged periodically and if there ins't a response they are removed from the list of Subscribers.<br>

<b>2. The Remember Me checkbox position moves sometimes after a new user signs up</b>
The position is fine for a returning user, so I hate to dabble with it, but please if anyone feels capable of solving the why this happens, I would like to know because I have the same bug on my site https://pixeldatabase.net, because most of the code for this app
came from PixelDatabase.Net.

<b>3. Sometimes a login fails and I have not been shown a message. I have added some code to try and fix this, but not sure if it helped or not at this point yet</b>

<br>
<b>Geek Zone</b>
<img src=https://github.com/DataJuggler/SharedRepo/blob/master/Shared/Images/Codey.png>
<br>
<b>Under The Hood Areas Of Interest areas</b>
1. Index.razor and Index.razor.cs. This is the main page of the app and the method SetupScreen(ScreenTypeEnum screenType) is called by the child components Login.razor or Join.razor.

ScreenType enum has 3 options:

    public enum ScreenTypeEnum : int
    {
        Main = 0,
        Join = 1,
        Login = 2
    }

If a user is not logged in, they are shown this message:<br>
<img src=https://github.com/DataJuggler/BlazorChat/blob/master/wwwroot/Images/SignUpToChat.png>
<br>
    
2. 

<br>
<b>State Of the App</b>
I am a little past prototype at this stage, but a large amount of testing has not been done.
I am sure there are probably plenty of bugs once others start using this. 
I will publish it to my server soon with a live sample.<br><br>

<b>Suggestions for version 2.0:</b>
1. I added links and you can post HTML if you know it, but I think the next features are going to be post images and video
with maybe some like, Up / Down votes or Stars or something to rate posts.
2. The current version only shows the last 10 messages. I didn't do anything involving private messages and priority,
I just get the last 10 messages and show them if they are public, or to or from for private messages.
3. I might save the messages to the database if I ever wanted to expand it to be not just a demo.
4. I stubbed out a Blocked user list for users, but didn't do anything with it. Perhaps a 'Vote Off The Island' if
a user is rude, abusive or a spammer.
5. I will probably add a way to rate posts
6. Some type of Admin feature so users can be warned first and then booted for abusive behavior.

Mainly this was a demo. I strated to use GRPC, but every sample I tried with 2 way communication was overkill
considering all I needed was to talk to a client that is on the same server as another. 

Once I learned how to do a ConfigurationServices.AddSingleton():

services.AddSingleton<SubscriberService>();        

And then I inject this into my Chat component:

@inject Services.SubscriberService SubscriberService

This was all I needed to send messages to all users. 

If you inspect the project, one of the more interesting parts is the 'Listen' method, which is set 
in the RegisterWithServer method. The Listen method is a callback and that is the delegate the server
calls to tell the client it is time to get new messages.

Let me know your thoughts, comments, suggestions etc. by creating an issue here or in the comments on the BlazorChat
video, which I am making now and my time machine is broken so I have to post it first.

To Do: Publish url to cool video.





