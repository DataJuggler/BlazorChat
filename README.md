# BlazorChat

https://blazorchat.com is now live! Now that it is online, there are lots of things I need to do to improve it.
Please report any bugs by creating an issue here.

Blazor Chat was originally a sample project of mine DataJuggler.Blazor.Components, but I decided it will be easier to maintain in its own project.

<br>
<img src=https://github.com/DataJuggler/SharedRepo/blob/master/Shared/Images/BlazorChat.png>
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

<b>1. Periodic Dispose</b><br>
I added a dispose method, and in theory when the user closes their browser, this should unsubscribe.
A few days ago I had a power failure during a storm, and I am fairly sure during a hard close no code is going to
execute without power to the browser, so a "Remove If Idle" method is probably needed. Where each user
would be pinged periodically and if there ins't a response they are removed from the list of Subscribers.<br>

<b>2. The Remember Me checkbox position moves sometimes after a new user signs up</b><br>
The position is fine for a returning user, so I hate to dabble with it, but please if anyone feels capable of solving the why this happens, I would like to know because I have the same bug on my site https://pixeldatabase.net, because most of the code for this app
came from PixelDatabase.Net.

<b>3. A couple of times a login failed and I was not shown a message.</b><br>
I have added some code to try and fix this since, but not sure if it helped or not at this point yet</b>

<br>
<b>Geek Zone</b>
<img src=https://github.com/DataJuggler/SharedRepo/blob/master/Shared/Images/Codey.png>
<br>
<b>Under The Hood Areas Of Interest areas</b><br><br>
<b>1. Index.razor and Index.razor.cs.</b><br>
This is the main page of the app and the method SetupScreen(ScreenTypeEnum screenType) is called by the child components Login.razor or Join.razor.

ScreenType is used to either show the Login, Join or Chat components.

ScreenType enum has 3 options:

    public enum ScreenTypeEnum : int
    {
        Main = 0,
        Join = 1,
        Login = 2
    }

<b>Parent / Child Communication</b><br>

    @if (ScreenType == ScreenTypeEnum.Join)
    { 
        <Join Parent=this></Join>
    }
    else if (ScreenType == ScreenTypeEnum.Login)
    {
        <Login Parent=this></Login>
    }
    else
    {
        <Chat Parent=this></Chat>
        @if (TextHelper.Exists(Message))
        {  
            <div class="message">
                @Message
            </div>
        }
    } 
    
Notice the Login, Join and Chat components all have Parent=this.

This is possible because the Index page implements IBlazorComponentParent and the child components are IBlazorComponent objects.

The setter for each of the child components, call the Register method of the Index page, and as a result the child objects have a property Parent and ParentIndexPage, which is just the IBlazorComponentParent cast as an Index (page) object. 

Also, the Index page now has a reference to the Login, Join or Chat components. I come from a Windows Forms background originally, and being able to refer to the child objects like this makes me feel right at home.

I wrote a block post in January talking about parent child communication if you would like to read more:<br>
https://datajugglerblazor.blogspot.com/2020/01/how-to-use-interfaces-to-communicate.html

Or if you would like to look at the source code is available here:
DataJuggler.Blazor.Components:<br>
https://github.com/DataJuggler/DataJuggler.Blazor.Components

First If a user is not logged in, they are shown this message:<br>
<img src=https://github.com/DataJuggler/BlazorChat/blob/master/wwwroot/Images/SignUpToChat.png><br><br>



Once you have a LoggedInUser, the user must click the Connect button, which calls the RegisterWithServer method:
<br>
<img src=https://github.com/DataJuggler/SharedRepo/blob/master/Shared/Images/BlazorChatHouseRules.png><br>

<b>2. DataJuggler.Blazor.Components</b><br>
This is my Nuget package I created that led to this BlazorChat project being created.

    <b>ValidationComponent</b><br>
    The ValidationComponent has properties for Required, and also a property can be set for 'IsUniqueCallbackRequired'.
    
    <b>IsUniqueCallbackRequired</b>If this parameter is set to true:
    
    <ValidationComponent Name="UserNameComponent" Parent=this
        Caption="User Name" IsRequired="true" MinimumLength="5"
        MaximumLength="20" IsUniqueCallbackRequired="true"             
        ValidationMessage="User Name Must Be Between 5 And 20 Characters">
    </ValidationComponent>
    
When the Validate method is called, if IsUniqueCallbackRequired = true, then the component makes a call to its parent and the parent executges a method a database look up to see if a UserName or EmailAddress already exists.

<br>
<img src=https://github.com/DataJuggler/SharedRepo/blob/master/Shared/Images/ValidationComponent.png>
The User Name field had passed validation, including the call to the database to check uniqueness, but the email address is not filled in.
<br>

    <b>Progress Bar</b><br>
    It took me 3 attempts to get a progress bar I really like, and I owe the credit to the CSS Percentage Circle:     
    
    CSS Percentage Circle
    Author: Andre Firchow
    
    http://circle.firchow.net/

    And this app links to this file included with DataJuggler.Blazor.Components Nuget package:

    <link href="~/_content/DataJuggler.Blazor.Components/css/circle.css" rel="stylesheet" />
    
    <b>Sprite Component</b><br>
    I am not using the Sprite component to display an image like it was designed for, but I created a property called <b>InvisibleSprite</b> on the Join and the Login components that I use for the Timer. I set the Interval of the Sprite like this:
    
     <Sprite Subscriber=this Visible="true" Interval="50" Position="fixed" 
         XPosition="-200" YPosition="-200" Width="100" Height="100">
     </Sprite>
     
 By setting the position off screen, the Sprite still registers, but the users doesn't see anything. The Interval of 50 means 20 times per seconds the component should be called. I use the Refresh method of the progress bar to increment the progress, because I really don't know how long it is going to take for the call to ValidateUser to execute.
 
 <b>3. Subscriber Service</b><br>
 The SubscriberService is a class that keeps track of the messages. This class is a Singleton available to all users thanks to this line in Startup.cs:
 
     services.AddSingleton<SubscriberService>();
     
 And this service is injected into the Chat component like this:
 
     @inject Services.SubscriberService SubscriberService
     
 <b>4. SpeechBubble Component</b><br>
 This component is used to display the messages typed by a user. One cool think I just learned how to use was HtmlSanitizer.
 
 I am using the Nuget package HtmlSanitizer and I found this class that uses it from one of their examples:
 
     <b>class MarkupStringExtension</b><br>
     
This class allows a user to type HTML, and it removes harmful links and formats a string so you type <b>Bold a word or phrase</b> like this. You can also type in a link, and I wrote some code to convert the link to an anchor tag and set the href so it is clickable.

The call to use it is like this in the Razor markup:

    <div class="@TextStyle">
        <span>@(((MarkupString)HtmlText).Sanitize())</span>
    </div>
    
I have two properites on the SpeechBubble. Text and HtmlText. It wasn't really needed, but when you set the value for Text, the value for HtmlText is set to Text, so this keeps them separated (kind of sounds like a song by OffSpring).

For now, I randomly choose which bubble color to show for each message. It probably should let a user pick a color, or even show an avatar next to a message post, but I have to start with baby steps.

To do that I use my Nuget package: DataJuggler.RandomShuffler.Core


<b>5. RandomShuffler</b><br>

This was one of my earliest Nuget packages and I still use it because it provides for even distribution compared to true random numbers which could take thousands or millions of years to evenly distribute randomness. I use this compnent to pick which bubble color to show.

There are 6 Bubble colors available, and the images can be found in the wwwroot/Images/Bubbles folder.

using DataJuggler.RandomShuffler.Core;

    // Create a Shuffler
    Shuffler = new RandomShuffler(1, 6, 1, 10);
    
What this does is create a shuffler that fills in the items from min to max (1 to 6) and creates only 1 set of numbers, and shuffles 10 times. The Shuffler automatically recreates itself if you pull all the numbers in the PullNextItem method.

I prefer this type of shuffling, as it truly shuffles and pulls items. There are also methods for Cards and I have a program called Card Counter to teach you how to learn to count cards in Black Jack, but that project is coming this summer.

https://github.com/DataJuggler/RandomShuffler.Core
Nuget: uDataJuggler.RandomShuffler.Core;

Chat.razor has a Send message, and here I pick a random bubble color:

message.BubbleColor = (BubbleColorEnum) Shuffler.PullNextItem();

<b>BubbleColorEnum</b><br>


    public enum BubbleColorEnum : int
    {
        NotSet = 0,
        BlueGreen = 1,        
        BlueRed = 2,
        GreenBlue = 3,
        GreenRed = 4,
        RedBlue = 5,
        RedGreen = 6,
    }

So the integer value pulled above converts to the image shown, and the url for I

The SpeechBubble uses BlazorStyled, where I have a CSS property set like this:

@using BlazorStyled

<Styled @bind-Classname="@BubbleStyle">
    background-image: url('@ImageUrl');
    background-repeat: no-repeat;
    background-size: 100% 100%;
    position: relative;
    left: 0%;
    top: 0vh;
    width: 84%;
    height: auto;
    margin-bottom: 1.2vh;
</Styled>

And the @ImageUrl propperty is set by the Chat component when it sets the BubbleColorEnum of the message:

Here in the SubscriberMessage class, the BubbleColor property looks like this and sets the ImageUrl whenever the BubbleColor is set.

    public BubbleColorEnum BubbleColor
    {
        get { return bubbleColor; }
        set 
        {
            // set the bubbleColor
            bubbleColor = value;

            // Set the ImageUrl
            SetImageUrl();
        }
    }
    
    <b>6. Listen method</b><br>
    The Listen method is the delegate callback that a SubscriberCallback sets when it registers with the Server.
    
        public void Listen(SubscriberMessage message)
        {
            // if the message exists
            if (NullHelper.Exists(message))
            {
                // if the message is a SystemMessage
                if (message.IsSystemMessage)
                {   
                    // Get the Names again
                    this.Names = SubscriberService.GetSubscriberNames();

                    // Update the UI
                    Refresh();
                }
                else
                {  
                    // Get the Messages
                    this.Messages = SubscriberService.GetBroadcastMessages(this.Id);

                    // Update the UI
                    Refresh();                   
                }
            }
        }
    
        For now I have it hard coded to get the last 10 messages, but with more users I am thinking that should be a little higher.
   


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

This was all I needed to send messages to all users. 

Let me know your thoughts, comments, suggestions etc. by creating an issue here or in the comments on the BlazorChat
video, which I am making now and my time machine is broken so I have to post it first.

To Do: Publish url to cool video.





