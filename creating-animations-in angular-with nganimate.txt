In the past, making web apps feel delightful with animations required programmatically adding and removing classes to DOM elements. For example, if I wanted to have an element bounce in using a custom CSS class using jQuery, it would look something like this: `$('.item').show().addClass('bounceIn')`. If I wanted to hide the element afterwards with a custom fade out animation, I would then have to call `$('.item').hide().addClass('fadeOut')`.  As you probably know from experience, having this sort of logic littered throughout your javascript files quickly becomes hard to manage as your application grows in size.

Before we dive into how Angular solves this problem, lets first think about when you typically want to animate DOM elements: after user interaction with your app, which then triggers displaying / hiding elements, moving elements in a list around, etc. Sound familiar? That's right -- ng-hide/ng-show, ng-if, ng-repeat, and the other directives provided by Angular automatically deal with this functionality for us. **Wouldn't it be great if the ng-hide/ng-show, ng-if, ng-repeat, etc directives automatically added CSS classes to their respective DOM elements that we could hook into for providing animations?**

This is precisely how Angular's [ngAnimate module](https://docs.angularjs.org/api/ngAnimate) works. To demonstrate, lets start with a quick example of ng-hide/ng-show animations.

{info}
You'll need to include the angular-animate.js file and inject ngAnimate into your module before you can use animations.

# ng-hide & ng-show
{video: basic-ng-hide}

Animating the transition between true and false states for ng-show and ng-hide is as simple as defining an extra CSS class for `your-css-selector.ng-hide`. The `.ng-hide` CSS will be activated when the element is being hidden.  For example, take this div named 'box-one':

```html
<div class="box-one" ng-show="test.showBoxOne">
  Hello! I am <b>box one</b>.
</div>
```

To have this box fade in and out when it's being shown or hidden (respectively), the CSS would look like this:

```css
.box-one {
  -webkit-transition:all linear 0.5s;
  transition:all linear 0.5s;
}
.box-one.ng-hide {
  opacity:0;
}
```

Notice that we're using the CSS transition property to set the duration of the animation - ngAnimate takes note of this property and will perform its magic.

{x: hide/show-css-transition}
**[See this code in action and experiment with it](http://codepen.io/EricSimons/pen/PwdKNE)**

{info}
There is not an ng-show CSS class - instead, the `.ng-hide` class is added for both ng-hide (when evaluated to true) and ng-show (when evaluated to false).

# Customizing animations
{video: custom-ng-hide}

At some point you may want to have different animations for showing and hiding, entering and leaving, etc. The simplest way to perform this is by hooking into ngAnimate's add and remove classes (for ng-hide, these are `.ng-hide-add` and `.ng-hide-remove`). This way, when ng-hide is being added to the element you can fire an animation through `.ng-hide-add`, but when it's being removed, you can specify a different animation altogether with `.ng-hide-remove`.

This functionality also allows you to use [custom keyframe animations](https://css-tricks.com/snippets/css/keyframe-animation-syntax/) instead of the transition property, if desired. Taking our example from before, lets animate our box with a custom bounce in animation when it's being a shown and a bounce out animation when its being hidden. To do this, we just change our CSS like so:

```css
.box-one { }

.box-one.ng-hide-add {
  -webkit-animation:0.5s hide;
  animation:0.5s hide;
}

@keyframes hide {
  0% {
    opacity:1;
    transform: scale(1);
  }
  30% {
    transform: scale(1.02);
  }
  100% { 
    opacity:0;
    transform: scale(0.5);
  }
}

@-webkit-keyframes hide {
  0% {
    opacity:1;
    transform: scale(1);
  }
  30% {
    transform: scale(1.02);
  }
  100% { 
    opacity:0;
    transform: scale(0.5);
  }
}


.box-one.ng-hide-remove {
  -webkit-animation:0.3s show;
  animation:0.3s show;
}

@keyframes show {
  0% {
    opacity:0;
    transform: scale(0.5);
  }
  70% {
    transform: scale(1.02);
  }
  100% { 
    opacity:1;
    transform: scale(1);
  }
}

@-webkit-keyframes show {
  0% {
    opacity:0;
    transform: scale(0.5);
  }
  70% {
    transform: scale(1.02);
  }
  100% { 
    opacity:1;
    transform: scale(1);
  }
}
```

{x: hide/show-keyframes-transition}
**[See this code in action and experiment with it](http://codepen.io/EricSimons/pen/bNxrpK)**

Creating custom animations can be done for a variety of Angular directives besides ng-show/ng-hide -- don't worry, we'll cover them all in the section ["Other ngAnimate supported directives"](#other-nganimate-supported-directives).

# Animate entrances & exits of ng-repeat elements

{video: ng-repeat}

ngAnimate provides CSS classes for ng-repeat elements when they're appearing, disappearing, and moving to a new index (`.ng-enter`, `.ng-leave`, and `.ng-move` respectively). To demonstrate, lets create an ng-repeat where the elements fade in and out for all three of these animation states.

```html
<div class="item" ng-repeat="person in test.people">
  {{ person.name }}
</div>
```

```css
.item { }

.item.ng-move,
.item.ng-enter,
.item.ng-leave {
  -webkit-transition:all linear 0.5s;
  transition:all linear 0.5s;
}

.item.ng-leave.ng-leave-active,
.item.ng-move,
.item.ng-enter {
  opacity:0;
}

.item.ng-leave,
.item.ng-move.ng-move-active,
.item.ng-enter.ng-enter-active {
  opacity:1;
}
```

{x: ng-repeat-animations}
**[See this code in action and experiment with it](http://codepen.io/EricSimons/pen/VYGQwX)**

<input type="text" ng-model="search.name" />
    
    <div class="item" ng-repeat="person in test.people | filter:search">

As shown in the video above, these animations are especially neat when your ng-repeat is using dynamic filters or ordering. It provides immediate feedback to the user that the application is changing based on their input in a very delightful way.

# Activate specific animations using ng-class

{video: ng-class}

What if you want to fire a custom animation on an element, but there are other animations that need to hook into this element as well? ng-class allows you to programmatically activate certain animations by adding custom CSS classes at your leisure.

In this example, we will have two animation states: `.bigger`, which simply increases the font size, and `.light-theme`, which changes the background and font color. Our HTML will have two buttons that will activate these CSS classes through the ng-class directive:

```html
<button ng-click="test.bigger = !test.bigger">
  Set bigger = {{ !test.bigger }}
</button>
<button ng-click="test.lightTheme = !test.lightTheme">
  Set lightTheme = {{ !test.lightTheme }}
</button>

<div class="box" ng-class="{ 'bigger': test.bigger, 'light-theme': test.lightTheme }">
  This is just a box.
</div>
```

```css
.box {
  background:#333;
  color:#fff;
  
  -webkit-transition:all linear 0.5s;
  transition:all linear 0.5s;
}

.box.bigger {
  font-size:30px;
}

.box.light-theme {
  background:#eee;
  color:#333;
}
```

{x: ng-class-animations}
**[See this code in action and experiment with it](http://codepen.io/EricSimons/pen/KwxQpw)**

Another cool benefit of this, as seen in the video above, is the ability to fire multiple animation states simultaneously (if desired). However, there are countless other ways you can use ng-class for animating elements programmatically - so don't hesitate to use your imagination when tackling difficult animation challenges!

# Other ngAnimate supported directives
We've covered some basic usage of ngAnimate with ng-show/ng-hide, ng-repeat and ng-class. This knowledge can be directly applied to animating many of the other directives present in Angular - below is a list all the directives with their corresponding CSS classes:

**ngRepeat**: enter, leave and move
**ngView**: enter and leave
**ngInclude**: enter and leave
**ngSwitch**: enter and leave
**ngIf**: enter and leave
**ngClass**: add and remove (the CSS class(es) present)
**ngShow & ngHide**: add and remove (the ng-hide class value)
**form & ngModel**: add and remove (dirty, pristine, valid, invalid & all other validations)
**ngMessages**: add and remove (ng-active & ng-inactive)
**ngMessage**: enter and leave

{x: ngAnimate-docs}
**[For more information, we recommend that you read the ngAnimate documentation](https://docs.angularjs.org/api/ngAnimate)**

# $animate API
When writing your own directives, you can use the $animate API to animate DOM elements. To learn how to use the $animate API, we recommend the following resources:

{x: moo-animate}
[Read the "Rolling out animations with your own directives" section](http://www.yearofmoo.com/2013/08/remastered-animation-in-angularjs-1-2.html#rolling-out-animations-with-your-own-directives) in YearOfMoo's blog post on Angular animations.

{x: animate-docs}
[The $animate page in the AngularJS docs](https://docs.angularjs.org/api/ngAnimate/service/$animate) is a fantastic resource for understanding what is available to you in the $animate service.

# Go forth and make pretty UI's
Thanks to ngAnimate, creating awesome animations with Angular is now insanely easy. If you have any questions feel free to [tweet me](https://twitter.com/intent/tweet?text=@EricSimons40%20I%20love%20you). I will update this tutorial accordingly as new animation techniques and functionality becomes available in subsequent version of Angular, so [follow me on Twitter @ericsimons40](https://twitter.com/ericsimons40) if you want to stay in the loop!