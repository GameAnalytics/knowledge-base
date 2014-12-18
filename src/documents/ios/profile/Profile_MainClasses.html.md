---
layout: "content"
image: "Modeling"
title: "Main Classes & Operations"
text: "Read descriptions of the various social entities ios-profile provides, and see usage examples of operations that can be done to the different entities."
position: 2
theme: 'platforms'
collection: 'ios_profile'
module: 'profile'
platform: 'ios'
---

# Main Classes & Operations

In this document you'll find descriptions of most of the main classes and interfaces of ios-profile. Some of these classes represent the different social elements used in the Profile module, while others contain functionality to perform social-related operations.

![alt text](/img/tutorial_img/soomla_diagrams/Profile.png "Profile Diagram")

<br>
Social actions allow you to entice social engagement by offering your users rewards in exchange for social interactions. For example, you can ask your users to like your page or post a status about your game, and give them various rewards, such as a badge of recognition or free virtual items that you normally sell for money/virtual currency. In this win-win situation your users will be pleased, and the network effect will increase the popularity of your game.

<div class="info-box">`Reward`s are a part of SOOMLA's core module and are used in many methods of Profile. Read about the different types of `Reward`s [below](#auxiliary-model-reward).</div>

## UserProfileUtils [<img class="link-icon" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/ios-profile/blob/master/SoomlaiOSProfile/UserProfileUtils.h)

This class lists the different social networks that exist today. Currently, SOOMLA supports Facebook, Twitter, and Google+.

## SocialActionUtils [<img class="link-icon" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/ios-profile/blob/master/SoomlaiOSProfile/SocialActionUtils.h)

`SocialActionUtils` represents various social actions that can be performed in social networks, such as posting a status or story, or uploading an image.

This class simply holds a string enumeration of the different social actions.

## UserProfile [<img class="link-icon" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/ios-profile/blob/master/SoomlaiOSProfile/domain/UserProfile.h)

This class represents a profile of a user from a social network (provider).

<div class="info-box">Note that each social provider (FB, G+, Twitter, etc..) gives access to different information, so you won't necessarily receive all the fields mentioned below.</div>

**A `UserProfile` contains the following elements:**

- `provider`
- `profileId`
- `username`
- `email`
- `firstName`
- `lastName`
- `avatarLink`
- `location`
- `gender`
- `language`
- `birthday`

## SoomlaProfile [<img class="link-icon" src="/img/tutorial_img/linkImg.png">](https://github.com/soomla/ios-profile/blob/master/SoomlaiOSProfile/SoomlaProfile.h)

This is the main class that controls the entire SOOMLA Profile module. Use this class to perform various social and authentication operations for users. The Profile module will work with the social and authentication plugins of the integrated social provider (FB, G+, Twitter, etc..).

<div class="info-box">Most of the functions in this class call relevant functions from the social provider's SDK, and do NOT return a value, but rather fire appropriate events that contain the return values. Read more about [Event Handling](/cocos2dx/profile/Profile_Events).</div>

<br>
The diagram below depicts the flow that takes place when a `CCSoomlaProfile` function is called. In the diagram, the example function shown is `login`, but this principle holds for all functions.

![alt text](/img/tutorial_img/profile/functionFlow.png "Method Flow")

<br>
###`login / logout`

The `login` function will log the user into the specified provider, and will give the user a reward if one was provided.

Most of the social actions provided in Profile depend on the user being logged in. Therefore, upon successful login, you'll want to enable the rest of your social action buttons. For example, after the user is successfully logged in, you can display "Like" and "Post Status" buttons.

`logout` simply logs the user out of the specified provider. Don't forget to disable the social action buttons in your UI once your user is logged out.

``` objectivec
// If the user clicks on the login button you provide, call the login method:
[[SoomlaProfile getInstance]
	loginWithProvider:FACEBOOK
	andPayload:@""
	andReward:appDelegate.loginReward
];

// If the user would like to logout:
[[SoomlaProfile getInstance] logoutWithProvider:FACEBOOK];
```

<br>
###`isLoggedIn`

As its name implies, this method checks if the user is logged in and returns a boolean value.

``` objectivec
if ([[SoomlaProfile getInstance] isLoggedInWithProvider:FACEBOOK]) {
    // Here you can (and should) set the screen to match the logged-in state.
    // For example display the logout button, like button, share button, etc.
}
```

<div class="info-box">If the user is not logged in, please notice that `isLoggedIn` will not log the user in, you'll need to call the `login` method yourself. </div>

<br>
###`like`

This function opens up the provider page to "like" (a web page in a browser), and grants the user the supplied reward. For example, give the user 100 coins for liking your page.

``` objectivec
// If the user clicks the "Like" button provided in your game, call the
// like method and reward him/her with 100 coins.
[[SoomlaProfile getInstance] like:FACEBOOK
	andPageName:@"The.SOOMLA.Project"
	andReward:appDelegate.likeReward
];
```

<div class="info-box">Note that the user is given the reward just for clicking `Like` from the application. The `Like` function opens the page to like, but does not track if the user *actually* liked the page or not.</div>

<br>
###`updateStatus`

This function updates the user's status, which is simply a message, on the supplied social provider. Upon a successful update, the user will receive the supplied reward. For example, reward users that post a specific status with a `CCSingleUseVG`, such as a sword.

``` objectivec
[[SoomlaProfile getInstance] updateStatusWithProvider:FACEBOOK
	andStatus:@"I love SOOMLA! http://www.soom.la"
	andReward:appDelegate.updateStatusReward
];
```

![alt text](/img/tutorial_img/profile/socialStatus.png "Update Status")

<br>
###`updateStory`
This function posts a story (which is a detailed status) on the user's wall in the supplied social provider. Upon a successful update, the user will receive the supplied reward.

For example, once your user reaches a high score, you could display a popup that allows them to share their high score on Facebook with a click of a button. Once he/she shares the story, you can give them a reward such as a free character.

**NOTE:** This functionality is supported in Facebook only.

``` objectivec
[[SoomlaProfile getInstance] updateStoryWithProvider:TARGET_PROVIDER
	andMessage:@"This is the story"
	andName:@"The story of SOOMBOT (Profile Test App)"
	andCaption:@"SOOMBOT Story"
	andDescription:@"DESCRIPTION"
	andLink:@"http://about.soom.la/soombots"
	andPicture:@"http://about.soom.la/.../spockbot.png"
	andReward:appDelegate.updateStoryReward
];
```

![alt text](/img/tutorial_img/profile/socialStory.png "Post Story")

<br>
###`uploadImage`

This function uploads an image on the user's wall in the supplied social provider. Upon a successful upload, the user will receive the supplied reward.

For example, when your user finishes a level in your game, you can offer him/her to upload an image (perhaps a screenshot of the finished level) and receive a reward.

**NOTE:** This functionality is supported in Facebook only.

``` objectivec
[[SoomlaProfile getInstance] uploadImageWithProvider:FACEBOOK
	andMessage:@"I love SOOMLA! http://www.soom.la"
	andFilePath:filePath
	andReward:appDelegate.uploadImageReward
];
```

![alt text](/img/tutorial_img/profile/socialUpload.png "Upload Image")

<div class="info-box">The image to upload should be on the device already; the path supplied needs to be a full path to the image on the device.</div>

<br>
###`getStoredUserProfile`

This function retrieves the user's page for the given social provider from the **local device storage** (`GetStoredUserProfile` does not call any social provider function, it retrieves and returns its information from the storage, contrary to what is depicted in the diagram at the beginning of this section). This function allows you to get user information even if the user is offline.

For example, you could use `GetStoredUserProfile` to get the user's `FirstName`, and welcome him to the game.

``` objectivec
[[SoomlaProfile getInstance] getStoredUserProfileWithProvider:FACEBOOK];
```

<div class="info-box">This functionality is only available if the user has already logged into the provider.</div>

<br>
###`getContacts`

This function retrieves a list of the user's contacts from the supplied provider.

<div class="info-box">Notice that some social providers (FB, G+, Twitter) supply all of the user's contacts and some supply only the contacts that use your app.</div>

You could use `GetContacts` to show your users a personalized screen where they can see which of their friends are also playing your game, or you could offer the contacts that don't play your game to download your game and receive some free coins.

``` objectivec
[[SoomlaProfile getInstance] getContactsWithProvider:FACEBOOK
	andReward:nil
];
```

<br>
###`openAppRatingPage`

`openAppRatingPage` conveniently opens your application's page in the App Store so that it's simple to rate the app. You can offer your users to rate your app after they've completed a level successfully or have progressed significantly in your game.

``` objectivec
[[SoomlaProfile getInstance] openAppRatingPage];
```

##Auxiliary Model: Reward

A `Reward` is an entity which can be earned by the user for meeting certain criteria in game progress.

<div class="info-box">Note that `Reward` is a part of soomla-ios-core, and not part of the Profile module. However, because `Reward`s are used very often throughout Profile, it's important that you are familiar with the different `Reward` types.</div>

`Reward` itself cannot be instantiated, but there are many types of rewards, all explained below.

<br>
###**VirtualItemReward**

A specific type of `Reward` that you can use to give your users some amount of a virtual item. **For example:** Give users 100 coins (virtual currency) for liking your page.

``` objectivec
VirtualItemReward* coinReward;
...

self.coinReward = [[VirtualItemReward alloc] initWithRewardId:@"coin_reward"
	andName:@"Coin Reward"
	andAmount:100
	andAssociatedItemId:COIN_CURRENCY_ITEM_ID
];
```

<br>
###**BadgeReward**

A specific type of `Reward` that represents a badge with an icon. **For example:** Give the user a badge reward for posting a status on his/her wall.

``` objectivec
BadgeReward* badgeReward;
...

self.badgeReward = [[BadgeReward alloc] initWithRewardId:@"badge_reward"
	andName:@"Badge Reward"
	andIconUrl:@"someUrl"
];
```

<br>
###**SequenceReward**

A specific type of `Reward` that holds a list of other `Reward`s in a certain sequence. The rewards are given in ascending order. **For example:** In a Karate game the user can progress between belts and can be rewarded a sequence of: blue belt, yellow belt, green belt, brown belt, and lastly, black belt.

``` objectivec
SequenceReward* beltReward;

// Assume that these are Badge Rewards that have been created.
NSArray* beltRewards = [NSArray arrayWithObjects:blueBeltReward,
	purpleBeltReward, brownBeltReward, blackBeltReward, nil];

self.badgeReward = [[BadgeReward alloc] initWithRewardId:@"belt_reward"
	andName:@"Belt Reward"
	andRewards: beltRewards
];
```

<br>
###**RandomReward**

A specific type of `Reward` that holds a list of other `Reward`s. When this `Reward` is given, it randomly chooses a `Reward` from the list of `Reward`s it internally holds. **For example:** Give users a mystery box `Reward` for uploading an image, that grants him/her a random `Reward`.

``` objectivec
RandomReward* mysteryReward;

// Assume that these are Virtual Item Rewards that have been created.
NSArray* rewards = [NSArray arrayWithObjects:tenCoinReward,
	hundredCoinReward, thousandCoinReward, nil];

self.mysteryReward = [[RandomReward alloc] initWithRewardId:@"random_reward"
	andName:@"Random Reward"
	andRewards: rewards
];
```