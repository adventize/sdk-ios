
# Adventize iOS SDK integration

There are two types of SDK: AdvertiserSDK and PublisherSDK. The difference is that AdvertiserSDK contains only Advertizer's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertizer.


## Advertiser integration

Download AdvertiserSDK — [adventize-advertiser-ios-sdk.tar.gz](https://github.com/adventize/sdk-ios/raw/master/adventize-advertiser-ios-sdk.tar.gz).

Archive with AdvertiserSDK contains `ExampleAdvertizer` project and `SDK_adv` directory with two libraries for both ARM and x86 (simulator) architectures, `deps` — dependencies directory and `include` — skd headers directory.

To integrate AdvertiserSDK in your app make following steps:

1. Add all `SDK_adv` directory (containing libraries and other sources) to your project;
2. Link these frameworks to your project: `CoreData`, `SystemConfiguration`, `AdSupport`;
3. Include Adventize header to class where you plan to report your app starting:

        #import "Advertiser.h";

4. Start Adventize session in your class, when your application starts:
	
		[Advertiser
			startSession: @"Your appId"
			secret: @"Your secret"];

5. Stop adventize session before your application will terminate. It is recommended to use `applicationDidEnterBackground` function.

	    [Advertiser stopSession];

To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.


## Publisher integration

Download PublisherSDK — [adventize-publisher-ios-sdk.tar.gz](https://github.com/adventize/sdk-ios/raw/master/adventize-publisher-ios-sdk.tar.gz).

Archive with PublisherSDK also contains `ExamplePublisher` project and `SDK_pub` directory. SDK directory contains two libraries for ARM and x86 architectures, `deps` — dependencies directory, `media` — files needed for publisher's offerwall and `include` - directory with library headers.

To integrate PublisherSDK in your app follow these steps:

1. Add all `SDK_pub` directory (containing libraries, `deps`, `media` and `include` directories) to your project;
2. Link these frameworks to your project: `CoreData`, `SystemConfiguration`, `AdSupport`;
3. Include Publisher class in class, where you plan to use sdk:

	    #import "Publisher.h";

4. Use this to initialise sdk with your `appId` on start (before using offerwall!):

	    [PublisherManager startSession: @"Your appId"];

5. Include AdventizeSDK class to place, where you plan to call offerwall. Also include callback interface, which you will want to implement.

	    #import "AdventizeSDK.h" 
        #import "AdventizeCallback.h" (optional)

6. You have two ways of calling the offerwall window. First one is just to call it. Then it will appear and will download offers

	    [AdventizeSDK showOfferWallWindow:(UINavigationController *)controller];
        
7. The second way is to ask offerWall for downloading and open it only when (if) it is ready. 
First you should implement AdventizeCallback protocol in your class.

        @interface YourClass : UIVewController<AdventizeCallback>
And ask it for fetching:
    
        [AdventizeSDK askForFetch:self];
In callback method you should call offerWall window if it is ok, passing Navigation controller to it:
        
        - (void)offerWallDownloaded:(BOOL)result
        {
            if(result)
                [AdventizeSDK showOfferWallWindow:self.navigationController];
        }

8. Stop publisher's session before your application will terminate. It is recommended to use `applicationDidEnterBackground` function.

	    [Publisher stopSession];

To pass the integration you should make 3 ad fetches. Just ask for fetch 3 times. Do not warry if the fetch will be empty. It is so because your app is not integrated and under moderation.

## Both publisher and advertizer

To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK library. Follow publisher's instructions to integrate publisher, but for starting and stopping sessions use Advertiser class, as it is done in Advertiser's instructions.
