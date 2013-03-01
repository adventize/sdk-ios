
# Adventize iOS SDK integration

There are two types of SDK: AdvertiserSDK and PublisherSDK. The difference is that AdvertiserSDK contains only Advertizer's capabilities while PublisherSDK is the full SDK, containing both Publisher and Advertizer.


## Advertiser integration

Download AdvertiserSDK — [adventize-advertiser-ios-sdk.tar.gz](https://github.com/adventize/sdk-ios/raw/master/adventize-advertiser-ios-sdk.tar.gz).

Archive with AdvertiserSDK contains `ExampleAdvertizer` project and `SDK_adv` directory with two libraries for both ARM and x86 (simulator) architectures and `deps` — dependencies directory.

To integrate AdvertiserSDK in your app make following steps:

1. Add all SDK_adv directory (containing libraries and other sources) to your project;
2. Link these frameworks to your project: `CoreData`, `SystemConfiguration`;
3. Add advertizer's class definition in your main header:

	    @class AdventizeManager;

4. Start Adventize session in your main class, when your application starts:
	
		[AdventizeManager
			startSessionWithAppId: @"{Your appId}"
			secret: @"{Your secret}"];

5. Stop adventize session before your application will terminate:

	    [AdventizeManager stopSession];

To pass the integration you should make 3 sessions. The best way is to launch and close your application for 3 times.


## Publisher integration

Download PublisherSDK — [adventize-publisher-ios-sdk.tar.gz](https://github.com/adventize/sdk-ios/raw/master/adventize-publisher-ios-sdk.tar.gz).

Archive with PublisherSDK also contains `ExamplePublisher` project and `SDK_pub` directory. SDK directory contains two libraries for ARM and x86 architectures, `deps` — dependencies directory and `media` — files needed for publisher's offerwall.

To integrate PublisherSDK in your app follow these steps:

1. Add all `SDK_pub` directory (containing libraries, `deps` and `media` directories) to your project;
2. Link these frameworks to your project: `CoreData`, `SystemConfiguration`
3. Add publisher's class definition in your main header:

	    @class PublisherManager;

4. Add this to your main class to initialise sdk with your `appId` on start:

	    [PublisherManager setAppId: @"{Your appId}"];

5. Add class definitions to place, where you plan to call ad offerwall:

	    @class AdvViewController;

6. Call offerwall window:

	    AdvViewController *offers = [[AdvViewController alloc] init];
	    [self.navigationController pushViewController:offers animated:YES];

7. Stop publisher's session before your application will terminate:

	    [PublisherManager stopSession];

To pass the integration you should make 3 ad fetches. Just ask for fetch 3 times. Do not warry if the fetch will be empty. It is so because your app is not integrated and under moderation.

## Both publisher and advertizer

To integrate both publisher and advertizer capabilities in one app you should use PublisherSDK. Follow publisher's instructions to integrate publisher and advertizer's instructions from 3 till last items to integrate advertizer.
