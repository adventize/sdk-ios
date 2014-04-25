#Adventize iOS SDK integration

There are two types of SDK: `AdvertiserSDK` and `PublisherSDK`. The difference is that AdvertiserSDK contains only Advertizer's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertizer.

AdvertiserSDK helps us to understand when our client launches your application, so you pay only for the successful launch. Publishers SDK besides all above also allows you to integrate our offerwall into your application.

Each library splits on two - compiled with GoogleAnalyticsV2 and V3. You should select which one you prefer. It you have no GoogleAnalytics in your project - it is recommended to use our library with GAv3 support.
Also libraries splitted again - on one, compiled for simulator (with `X86` prefix) and one, compiled for device (with `ARM` prefix). You can import both libraries to one project, as Xcode simply ignores improper platform libraries. The advantige is - you can test our library in simulator.
##Advertiser integration

Download AdvertiserSDK — `adventize-advertiser-ios-sdk.tar.gz`.

Archive with AdvertiserSDK contains `ExampleAdvertizer` project and `SDK_adv` directory with two libraries for both ARM and x86 (simulator) architectures, `deps` — dependencies directory with arcives and `include` — sdk headers directory.

To integrate AdvertiserSDK in your app make following steps:
1. Add all `SDK_adv` directory (containing libs and includes) to your project. Also unzip and add dependencies from deps to your project.  
**Important!** Do not duplicate deps! So do not add deps, if you already have them.;
2. For dependencies, which contains only libs and headers - just add them to project and to your target. Thus, their heares became visible and their \*.a files will be added to your target's `Link Binary With Libraries`  
For dependencies, which contains source files - add them to your project and to your target. Thus, they will automatically be added to `Compile Sources` of your project's target.  
For dependencies, which contains full Xcode project - add them to your project **but not to your target!** In added subproject find Products and add library from there to your project's `Link Binary With Libraries`
3. Link these frameworks to your project: `CoreData`, `SystemConfiguration`, `AdSupport`, `libz.dylib`;
4. Include advertiser's header to class where you plan to report your app starting:

        #import "Advertiser.h";
5. Create Advertiser's object, as shown in ExampleAdvertizer's project:

        Advertiser sAdventize = [[Advertiser alloc] initWithAppId:yourAppId secret:yourSecret];
Pass to init method your appId and secret, which you get in Adventize manager dashboard.  
**Important!** Do not lose Advertiser object, as you need to release it later. It is recommended to create static variable and use singleton function, like this: 

        + (Advertiser *)getAdventize
        {
            if(!sAdventize)
            {   
                NSString *yourAppId = @"5968";
                NSString *yourSecret = @"bdfWhgCURNuKvPD02OZ0qA";
                sAdventize = [[Advertiser alloc] initWithAppId:yourAppId secret:yourSecret];
                sAdventize.getRequestQueue = [^NSOperationQueue* ()
                {
                    return [[NetLib newRequestQueue] autorelease];
                } copy];
            }
            return sAdventize;
        }
You could use define to simplify function call: `#define advertizer [YourClass getAdventize]`
6. Override advertiser's `getRequestQueue` method with your block. You should give NSOperationQueue to advertiser, where advertiser will run it's async task.  
If you have queue and want share it with Adventize - use this: 

        sAdventize.getRequestQueue = [^NSOperationQueue* ()
        {
            return [[NetLib createWithExiting:yourQueue] autorelease];
        } copy];
If you don't have your queue - let Adventize to create it: 
        
        sAdventize.getRequestQueue = [^NSOperationQueue* ()
        {
            return [[NetLib newRequestQueue] autorelease];
        } copy];
Queue will be retained on first run and will be released on last (stop session). 
7. Start advertiser's session with: 

        [advertizer startSession];
8. Stop advertizer's session before your application will terminate. It is recommended to use `applicationDidEnterBackground` function. 

        [advertizer stopSession];
9. To identify your user you can set your user's id and it will be reported in any Adventize action:
`[advertizer setUserID:@"Your user id"];` You can set it multiple times, it is recommended to use it in login methods.
10. To send actions your user done to server - use `[advertizer sendAction:@"action"]`;  
Where action is an action, which you set in Adventize dashboard.

To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.
##Publisher integration
###Basic integration

Download PublisherSDK — `adventize-publisher-ios-sdk.tar.gz`.

Archive with PublisherSDK also contains `ExamplePublisher` project and `SDK_pub` directory. SDK directory contains two libraries for ARM and x86 architectures, `deps` — dependencies directory with arcives and `include` — sdk headers directory.

To integrate PublisherSDK in your app follow these steps:

1. Add all `SDK_pub` directory (containing libs and includes) to your project. Also unzip and add dependencies from deps to your project.  
**Important!** Do not duplicate deps! So do not add deps, if you already have them.
2. For dependencies, which contains only libs and headers - just add them to project and to your target. Thus, their heares became visible and their \*.a files will be added to your target's `Link Binary With Libraries`  
For dependencies, which contains source files - add them to your project and to your target. Thus, they will automatically be added to `Compile Sources` of your project's target.  
For dependencies, which contains full Xcode project - add them to your project but not to your target! In added subproject find Products and add library from there to your project's `Link Binary With Libraries`
3. Link these frameworks to your project: `CoreData`, `SystemConfiguration`, `AdSupport`, `libz.dylib`;
4. Include main SDK header in class, where you plan to work with publisher class:

        #import "Publisher.h";
5. Create Publisher's object, as shown in ExamplePublisher's project: 

        Publisher sAdventize = [[Publisher alloc] initWithAppId:yourAppId secret:yourSecret];
Pass to init method your appId and secret, which you get in Adventize manager dashboard. 
**Important!** Do not lose Publisher object, as you need to release it later. It is recommended to create static variable and use singleton function, like this: 

        + (Publisher *)getAdventize
        {
            if(!sAdventize)
            {
                NSString *yourAppId = @"5968";
                NSString *yourSecret = @"bdfWhgCURNuKvPD02OZ0qA";
                sAdventize = [[Publisher alloc] initWithAppId:yourAppId secret:yourSecret];
                sAdventize.getRequestQueue = [^NSOperationQueue* ()
                {
                    return [[NetLib newRequestQueue] autorelease];
                } copy];
            }
        return sAdventize;
        }
You could use define to simplify function call: `#define publisher [YourClass getAdventize]`
6. Override publisher's `getRequestQueue` method with your block. You should give NSOperationQueue to publisher, where publisher will run it's async task.  
If you have queue and want share it with Adventize - use this: 

        sAdventize.getRequestQueue = [^NSOperationQueue* ()
        {
            return [[NetLib createWithExiting:yourQueue] autorelease];
        } copy];
If you don't have your queue - let Adventize to create it: 

        sAdventize.getRequestQueue = [^NSOperationQueue* ()
        {
            return [[NetLib newRequestQueue] autorelease];
        } copy];
Queue will be retained on first run and will be released on last (stop session). 
7. Start publisher's session with: 

        [publisher startSession];
8. To ask for simple offerwall you should call publisher's requestOffers, passing block as a parameter. Block will be called, when function finish asking. It will pass array of offerwall items to your block, or nill - if error occured: 

        [publisher requestOffers:^(NSArray *offerwall)
        {
            [self performSelectorOnMainThread:@selector(your_handle_offerwall_method:) withObject:offerwall waitUntilDone:NO];
        }
You should remember, that block will be called from NSOperationQueue't thread. So you could performSelectorOnMainThread from there, if you need. 
9. While showing offerwall items - collect all items, which user saw. Collect only campaign id's of items, if they are not null: 

        if ([item campaignId])
            [your_nsarray addObject:[item campaignId]];
When you are ready - send this impressions to Adventize: 

        [publisher sendImpressions:your_nsarray andCallback:self];
Second parameter - is `SendImpressionCallback`. Implement this protocol in your class and create method `- (void)onSendImpressionsResult:(BOOL)ok`, which will be called - when impressions will be sent.  
If there is a connection error - ok will be NO - you should save all impressions and try again later. 
10. To make click on item - just open it click url in browser: `[[UIApplication sharedApplication] openURL:[NSURL URLWithString:[item clickURL]]]`;
11. To identify your user you can set your user's id and it will be reported in any Adventize action:
`[publisher setUserID:@"Your user id"]`; You can set it multiple times, it is recommended to use it in login methods.
12. You can send to Adventize your user's rate to count reward for CPA with `[publisher setUserRate:(long)rate]`
13. Stop publisher's session before your application will terminate. It is recommended to use `applicationDidEnterBackground` function. 

        [publisher stopSession];

###Banner integration

1. Include `Banner.h` class header in class, where you want to operate with banners.
2. To ask for banners you should call publisher's `requestBanners`, passing block as a parameter. Block will be called, when function finish asking. It will pass dictionary with banner locations as keys and BannerEntitys as values to your block, or nill - if error occured: 

        [publisher requestBanners:^(NSDictionary *banners)
        {
            [self performSelectorOnMainThread:@selector(your_handle_banners_method:) withObject:banners waitUntilDone:NO];
        }
        andLocations: your_banner_locations];
You should remember, that block will be called from NSOperationQueue't thread. So you could performSelectorOnMainThread from there, if you need.  
Second parameter is NSArray of your banner locations, which are set in Adventize dashboard; 
3. Dictionary will contain all banners, got from server. But it can not contain banner - you needed. So always check banner for existence: 

        BannerEntity *bannerEntity = [banners objectForKey:@"needed banner location"];
        if (bannerEntity)
        {...}

4. To get full-featured banner from entity - use banner's `initWithEntity` method: 

        Banner *banner = [[[Banner alloc] initWithEntity:bannerEntity] autorelease];
Now you can operate with banner, as with normal UIWebView.
**Important!** You should check for stimulation before showing banner, as ingame stimulation is prohibited on ios: 

        if ([banner checkStimul])
        {
            [[UIApplication sharedApplication] openURL:[banner url]];
        }
        else
        {
            CGRect webFrame = CGRectMake(10.0, 10.0, 320.0, 460.0);
            [banner setFrame:webFrame];
            [self.window addSubview:banner];
        }
This means - if banner is stimulated - it will be opened outside the game - in browser. And if it is not stimulated - it will be opened as UIWebView - in webFrame (as an example). 
5. You could also add ErrorCallback's block to banner - which will be called - if Itunes can't be open: 

        [banner addErrorCallback:^()
            {
                [self errorCallback];
            }];
6. You could also add your custom callback to banner, which will be fired, when it got callback's key. You should set keys in manager dashboard. 

        [banner addCustomCallback:^(NSArray *array)
        {
            [self exampleCallback:array];
        } andKey:@"your callback key"];
To pass the integration you should make 3 ad requests. Just ask for banner 3 times. Do not worry if the banner will be empty. It is so because your app is not integrated and under moderation.

##Both publisher and advertizer

To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK and call Advertiser's methods on your publisher instance.
