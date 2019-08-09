## <img src="images/icon.png" height="40" style="float:left;margin:-2px 5px 0 0"> Xamarin.Plugin.SharedTransitions
Activate shared element transitions between pages in Xamarin.Forms (IOS/Android)

[![NuGet](https://img.shields.io/nuget/v/Xamarin.Plugin.SharedTransitions.svg?label=NuGet)](https://www.nuget.org/packages/Xamarin.Plugin.SharedTransitions/)  [![License](https://img.shields.io/github/license/Evolutionlab/Xamarin.Plugin.SharedTransitions.svg)](https://opensource.org/licenses/MIT)



#### What is a shared element transition?
Shared element transitions are animations connecting common elements from one page to the next/previous.<br>
A shared element transition determines how elements that are present in two pages transition between them.
For example, an image that is displayed on both Page A and Page B, transitions from A to B when B becomes visible.
Transitions in apps provide visual continuity, connecting common elements from one page to the next.


<img src="images/transition_droid_new.gif" height="500">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="images/transition_ios_new.gif" height="500">

## Setup

* [Available on Nuget](https://www.nuget.org/packages/Xamarin.Plugin.SharedTransitions/)  [![NuGet](https://img.shields.io/nuget/v/Xamarin.Plugin.SharedTransitions.svg?label=NuGet)](https://www.nuget.org/packages/Xamarin.Plugin.SharedTransitions/)
* Install into your NetStandard project and Client projects.

**Platform Support**

|Platform|Version|
| -------------------  | :------------------: |
|Xamarin.iOS|iOS 9+|
|Xamarin.Android|API 21+|

## Usage


To activate transitions between pages, use the ```SharedTransitionNavigationPage``` instead of the classic NavigationPage:
```csharp
MainPage = new SharedTransitionNavigationPage(new Page1());
```

Add the xmlns namespace in your pages:
```
xmlns:sharedTransitions="clr-namespace:Plugin.SharedTransitions;assembly=Plugin.SharedTransitions"
```

#### Basic shared transitions with fixed elements

This is the most basic type of transition. We are going to animate one or more views from Page1 to Page2.

Tag the views to transition in the **source page**, for example *Page1.xaml*: 

```xml
<Image Source="mydog.jpg" sharedTransitions:Transition.Name="dogtransition" WidthRequest="100" />
```

Tag the views to transition in the **destination page**, for example *Page2.xaml*: 

```xml
<Image Source="mydog.jpg" sharedTransitions:Transition.Name="dogtransition"  WidthRequest="300" />
```

Now from Page1 you can push Page2 to start the transition:

```csharp
 NavigationPage.Push(new Page2())
```

When doing a Pop prom Page2 to Page1 a return transition will occour


#### Advanced shared transitions between collection of views and their details page

This type of shared transition is useful to animate a selected item from a collection of views (for example a listview or any custom control with bindable views) to his "details" page.
To make this work we need to set the ```sharedTransitions:Transition.Group``` property.

Listview sample in the **source page**, binding the Transition Group to a unique integer. The Transition Name property is used the same as the basic shared transition example.

```xml
<ListView HasUnevenRows="True">  
    <ListView.ItemTemplate>  
        <DataTemplate>  
            <ViewCell>  
                <StackLayout Orientation="Horizontal">  
                    <Label Text="{Binding Name}" Font="18"/>
                    <Image  Source="{Binding imgsource}" 
                            HorizontalOptions="EndAndExpand"
                            HeightRequest="30" WidthRequest="50"
                            sharedTransitions:Transition.Name="test" 
                            sharedTransitions:Transition.Group="{Binding Id}" />
                </StackLayout>  
            </ViewCell>  
        </DataTemplate>  
    </ListView.ItemTemplate>  
</ListView>  
```

Tag the views to transition in the **destination page**, for example *Page3.xaml*. We dont have to specify the Transition Group in the destination page, the match between pages will occour with the Transition Name property.

```xml
<Image x:Name="detailImage" sharedTransitions:Transition.Name="test" WidthRequest="300" />
```

When navigating from the source listview page to the destination, you need to inform the NavigationPage of what Transition Group has been selected:

 ```csharp
 private async void MyItemTapped(object sender,  ItemTapEventArgs e)
{
    var tappedItemData = e.Item as MyModel;
   //this is required in order to pass the views to animate
    SharedTransitionNavigationPage.SetSelectedTransitionGroup(this, tappedItemData.Id);
    await Navigation.PushAsync(new Page3(tappedItemData));
}
```

if you are using MVVM, you can bind the SelectedTransitionGroup property directly in XAML with binding:
```xml
<ContentPage .....
			 .....
			 SharedTransitionNavigationPage.SelectedGroup={Binding Selectedgroup}
```

Then, in your VM valorize the SelectedGroup property before the push.


In the destination page, you have to just set the Transition Name property to display the animation.

#### SharedNavigationPage methods

For every setter specified here, we have the corrisponding getter

```csharp
// Sets the selected tag group for this page
public static void SetSelectedTransitionGroup(Page page, int value)

// Sets the duration of the shared transition for this page (in ms).
public static void SetSharedTransitionDuration(Page page, long value)

// Sets the background animation for this page. 
// This is applied to also to all the views not included in the shared transition
public static void SetBackgroundAnimation(Page page, BackgroundAnimation value)
```

Background animations provided:

```csharp
    /// Background Animation Type
    /// </summary>
    public enum BackgroundAnimation
    {
        /// <summary>
        /// Do not animate.
        /// </summary>
        None = 0,

        /// <summary>
        /// Show a fade animation.
        /// </summary>
        Fade = 1,

        /// <summary>
        /// Show a flip animation.
        /// </summary>
        Flip = 2,

        /// <summary>
        /// Show a slide from left animation.
        /// </summary>
        SlideFromLeft = 3,

        /// <summary>
        /// Show a slide from right animation.
        /// </summary>
        SlideFromRight = 4,

        /// <summary>
        /// Show a slide from top animation.
        /// </summary>
        SlideFromTop = 5,

        /// <summary>
        /// Show a slide from bottom animation.
        /// </summary>
        SlideFromBottom = 6
    }
}

```

## Important notes
* The ```sharedTransitions:Transition.Name``` in source and destination page needs to match in order to display the animated transition
* You can animate multiple views at once, just remember that every Transition Name in a page needs to be unique
* You can animate the same element in multiple, subsequent pages
* On IOS you can pop the page using a PanGesture on the left side of the screen
* On IOS BoxView animation is not supported. You can animate other layouts like frame, stacklayout, ecc...
* When animating layouts (frame, stacklayouts....) i suggest to dont use a background transition other than "Fade" (or "None"). Android doesnt play well with background animation + shared transitions of layouts

## More samples!

Xamarin.Forms good looking UI [sample](https://github.com/jsuarezruiz/ArtNews) using BindableLayout and CollectionView. 

<img src="images/artnews.gif" Height="500" />

This sample is based on [Art News App](https://dribbble.com/shots/6282441-Art-News-App) designed by [Shirley Yao](https://dribbble.com/shirleyyao).

## Roadmap

- [x] More background animations (scale, etc)
- [x] Support layouts (frame, stacklayout, ecc..
- [x] Full MVVM Support
- [x] New sample app

## To do
- [ ] Fix layouts bounds during transitions in ios where certain type of layout placement are used (only for layouts)
- [ ] Enahnce iOS animations, investigate better solutions
- [ ] Add a property to control SelectedGroup in viewmodels (see the viewmodel sample for details)


## Latest release notes
2.0.0
* **Properties renamed to Transition.Name and Transition.Group <-- BREAKING**
* Full MVVM support with TransitionSelectedGroup
* New, improved Transition management under the hood (no more limitation to int number for transition names, faster view lookup for transitions)
* New sample app with listview and MVVM example, as well as normal transitions
* General bug fixing


1.1.0
* Update to .NETStandard 2
* Fixed transitions between images with different aspect ratios
* Enable transitions between layouts (frame, stacklayout, ecc..) **Note: BoxView in iOS is not currently supported**
* Updated Xamarin.Forms version from 3.1 to 3.6 (#6) by **@jsuarezruiz**
* Added new BackgroundAnimations (Flip, SlideFromLeft, etc.) (#7) by **@jsuarezruiz**
* Updated README with a new sample reference (#8) by **@jsuarezruiz**

## Copyright and license

Code released under the [MIT license](https://opensource.org/licenses/MIT),  [see license file](https://github.com/Evolutionlab/Xamarin.Plugin.SharedTransitions/blob/master/LICENSE).