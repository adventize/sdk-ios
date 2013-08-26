#Adventize iOS SDK integration

There are two types of SDK: `AdvertiserSDK` and `PublisherSDK`. The difference is that AdvertiserSDK contains only Advertizer's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertizer.

AdvertiserSDK helps us to understand when our client launches your application, so you pay only for the successful launch. Publishers SDK besides all above also allows you to integrate our offerwall into your application.
##Advertiser integration

Download AdvertiserSDK — `adventize-advertiser-ios-sdk.tar.gz`.

Archive with AdvertiserSDK contains `ExampleAdvertizer` project and `SDK_adv` directory with two libraries for both ARM and x86 (simulator) architectures, `deps` — dependencies directory and `include` — sdk headers directory.

To integrate AdvertiserSDK in your app make following steps:
1. Add all `SDK_adv` directory (containing libs, deps and others) to your project. Remove libs and deps, which you have already had (do not forget to remove all files, including headers!);
2. Link these frameworks to your project: `CoreData`, `SystemConfiguration`, `AdSupport`;
3. Include advertiser's header to class where you plan to report your app starting:

        #import "Advertiser.h";
4. Start advertizer's session in your main class, when your application starts:

        [Advertiser
                startSession: @"Your appId"
                secret: @"Your secret"];
5. Stop advertizer's session before your application will terminate. It is recommended to use applicationDidEnterBackground function.

        [Advertiser stopSession];
To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.
##Publisher integration
###Basic integration

Download PublisherSDK — `adventize-publisher-ios-sdk.tar.gz`.

Archive with PublisherSDK also contains `ExamplePublisher` project and `SDK_pub` directory. SDK directory contains two libraries for ARM and x86 architectures, `deps` — dependencies directory and `include` — directory with library headers.

To integrate PublisherSDK in your app follow these steps:

1. Add all SDK_pub directory (containing libraries, deps and include directories) to your project. Remove libs and deps, which you have already had (do not forget to remove all files, including headers!);
2. Link these frameworks to your project: `CoreData`, `SystemConfiguration`, `AdSupport`
3. Include main SDK header in class, where you plan to use sdk:

        #import "Publisher.h";
4. Use this to initialise sdk with your `appId` on start (before calling ad!):

        [Publisher setAppId: @"Your appId"];
5. Stop publisher's session before your application will terminate. It is recommended to use `applicationDidEnterBackground` function.

        [Publisher stopSession];

###Banner integration

1. Include `AskBannerCallback.h` interface and `OfferWallAPI.h` class in class, where you want to initiate banners downloading.

        #import "AskBannerCallback.h"
        #import "Publisher.h"
2. Call askBanners function, passing AskBannerCallback and NSArray* of NSString* locations, that you mention in your dashboard.

        NSMutableArray *locations = [NSMutableArray array];
        [locations addObject:@"Your location"];
        [locations addObject:@"Your another location"];
        [Publisher askBanners:self andLocations:locations];

3. Implement `onBannersLoaded` callback in your AskBannerCallback implementation. In this method you get an array of banners, which can be empty, if no banners exists.

        -(void)onBannersLoaded:(NSArray *)banners

4. Check banner's stimulation and if none - use it as normal UIWebView

        if(![banner checkStimul])
        {
            CGRect webFrame = CGRectMake(x,y,width,height);
            [banner setFrame:webFrame];
            [self.window addSubview:banner];
        }

5. If banner appeared to be stimulated - open it in system browser, as Apple prohibits in-app ad stimulation.

        if([banner checkStimul])
        {
            [[UIApplication sharedApplication] openURL:[banner url]];
        }

To pass the integration you should make 3 ad requests. Just ask for banner 3 times. Do not worry if the banner will be empty. It is so because your app is not integrated and under moderation.

###Adding banner's custom callbacks.
If you want to specify custom callbacks on banner's actions do following:

1. Import CustomCallback class in class, where you want to keep callbacks

        #import "CustomCallback.h"
2. Add your callback to SDK, passing action name and CustomCallback. Action name is the name of banner's matching action for this callback. CustomCallback is new object, that gets delegate object and selector with yout method.

        @autoreleasepool{
            [Publisher addCallback:@"goToShop" andCallb:[[[CustomCallback alloc] initWithCallback:@selector(exampleCallback:)                                                                                 andDelegate:self] autorelease]];
        });
3. You can get some params from callback. But first you should check if params are not null.

        if(params)
            NSLog(@"got callback with params : %@",params);
        else
            NSLog(@"got callback without params.");

Note, that you can add multiple callbacks with different names.
##Both publisher and advertizer

To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK. Follow publisher's instructions to integrate publisher and advertizer's instructions from 3 till last items to integrate advertizer.
