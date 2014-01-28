---
date: 27 January 2014
categories: intermediate, articles
author: Julien Cabanès
summary: There are two kinds of people in the Sass world : those who writes mixins, and those who uses it.
---

# Mixin all the Things

There are two kinds of people in the Sass world : those who write mixins, and those who use them. If you're part of the first kind and in a hurry, you can skip to the [interesting part](#interesting-part), otherwise let me tell you a little story.

## Curiosity is the Key

Seventeen years ago, while in high school, a classmate showed me a graphical calculator for the first time and told me it could run games. It was inconceivable for me. How could a *calculator* (an electronic thing which only does additions and multiplications, right?) run games? The most inconceivable thing was that my friend told me I could make one **myself**! I was curious enough to check, and a few weeks later I wrote a clone of Bomberman.

## Mixins for Everybody

Back to the present. You use Sass, probably because someone told you about it; how it was smart and fun. Like a lot of Sass folks you chose to use Compass as well because it saves keystrokes. Maybe you went further and installed other libraries like the official [Bootstrap Sass port](https://github.com/twbs/bootstrap-sass), or [Foundation](http://foundation.zurb.com/), or... you name it. You know what mixins are, you use them everyday, but did you really write any **yourself** ?

Mixins are the most powerful Sass feature. Some would say ```@extend``` is crazy, and it kinda is, but believe me, mixins are **undervalued**. In this article, I won't repeat what others did well so before going further you must ensure you know [how mixins work](http://thesassway.com/intermediate/leveraging-sass-mixins-for-cleaner-code/#how-mixins-work) and [how to write your own](http://thesassway.com/intermediate/leveraging-sass-mixins-for-cleaner-code/#writing-your-own) and come back because I'm going to share some interesting ways of using mixins. But first let's see what is the *nature* of mixins.

## Anatomy of an Abstraction
"Sasstraction" (the term coined for Sass Abstraction) is a hot topic, Jeremy Keith [wrote about it](http://adactio.com/journal/6606/), Chris Coyier also [wrote about it](http://css-tricks.com/abstraction-in-web-languages-and-variables-and-stuff/) and Chris Eppstein (creator of Compass and Sass contributor) explained [why it matters](http://chriseppstein.github.io/blog/2009/09/20/why-stylesheet-abstraction-matters/) a long time ago. Mixins are powerful because their abstractions can be *high-level*.

Variables was just a beginning, you used them from day one because it's **DRY**. [Functions](http://thesassway.com/advanced/pure-sass-functions/) (especially color ones, my favorites) opened new perspectives for those variables. All in all, these features are *basic* abstractions, they just help you get DRY : you store values in one location, you use and manipulate them in another location instead of repeating them. Finally, this helps your code to be more [maintenable](http://www.w3.org/People/Bos/DesignGuide/maintainability.html) and this is **a good thing**.

Once you add control directives (```@if```, ```@else```, etc...) you're adding **logic**. Depending on how you use it, the logic behind can be really smart or trivial. But smarter doesn't mean better; smart code can be hard to read, hard to learn. On the flip-side, trivial doesn't mean silly.

Mixins can also be trivial. I really love Compass but - without meaning any offense - a good majority of its mixins are pretty trivial; they're just about repeating vendor prefixes, and this helps! Everyday! Some are smarter like the [background](https://github.com/chriseppstein/compass/blob/stable/frameworks/compass/stylesheets/compass/css3/_images.scss)  which handles legacy webkit syntax for instance, you wouldn't do this yourself...

Whether trivial or smart, every abstraction operates the same way : **simplifying** a more or less complex task. It can do this by :

- avoiding repetition : respect the DRY principle
- encapsulating logic : hide or reduce the complexity behind an easier API

## CSS logic

Let's forget Sass for a moment and focus on CSS. Even if you're not aware of it, I'm sure you've already used logic in your stylesheets. Selectors are often read as conditions, this is a no-brainer when you think about it. For instance ```.item.active { display: block; }``` means « **if** the item is active **then** display it ».

We often forget how many abstractions are driving our devices today. From what processors do with electrons, to the code we read and write, to Angry Birds, the sole purpose is to bring the machine language - the binary - closer to human language (well, English at least, generally...). Each time we add an abstraction layer, we're making the machine speak and read more like a human (or is it the opposite?). By *translating* this simple CSS rule, I'm doing this job **mentally**, it took me a really short time but still. Maybe we could do it with a mixin ?

    // _utils.scss
    @mixin activable {
      display: none;
      &.active {
        display: block;
      }
    }

    // _items.scss
    .item {
      @include activable;
    }

This is definitely not the smartest code ever, and I could name my mixin more explicitly, ```display-if-active``` or whatever, but you see where I'm going. Abstraction is also a way for making things more **readable**, not because it is shorter, but more *natural*. We all know how to write a lot of LOC but even with comments it doesn't mean anything until we decrypt it. So what? We're doing it everyday so why bother? Actually, it matters because [we spend more time understanding code](http://www.codinghorror.com/blog/2006/09/when-understanding-means-rewriting.html) than writing it...

## Responsive logic

Responsive Web Design (RWD) generated more questions than answers. We're facing a lot of challenges and one I believe we've all encountered is how to choose breakpoints. You may know we can't use standard screen sizes because <a href="http://www.lukew.com/ff/entry.asp?1816">there's no such thing anymore</a>. I agree with Brad Frost (and other smarter-than-me guys) to <a href="http://bradfrostweb.com/blog/post/7-habits-of-highly-effective-media-queries/#content">let the content determine breakpoints</a> but this can be tiring because you need them at multiple places. So the first idea is to store those breakpoints in variables, sure there's nothing wrong with this, but I also like to use... yes, mixins.
    
    // _breakpoints.scss
    @mixin when-primary-nav-is-available {
      @media (min-width: 40em) {
        @content;
      }
    }

    // _mobile-nav.scss
    .mobile-nav {
      @include when-primary-nav-is-available {
        display: none;
      }
    }
  
    .primary-nav {
      @include when-primary-nav-is-available {
        display: block;
      }
    }
    
This example is obvious but it starts to look *over-engineered*, what do you think ? At first, I was confused like « Is it really a good idea ? Why not just using a variable ? ». But then, I used it on a project, and then another one. I came back to my code 2 days later and it started to make sense, it really was more readable and more maintenable.

I could not only change the min-width, but also add other logic behind like using another way of including media queries like [Breakpoint](https://github.com/Team-Sass/Breakpoint) for instance. The most interesting point was the appearance of some connections between random breakpoints I didn't expect and consider before. Some elements needed adjustments as I was progressing in the project and this technique helped me refactor without hassle. I decided to keep the use of the "when" prefix for every media-query mixin because it worked well for my team and I.

## Moving the mixin forward

The last case I want to share is about layouts, page layouts. We often deal with only 2 or 3 different layouts on a project, but for my last project I had 6 different layouts. The short story was that depending on the template, the main content could be on its own and full-width, on its own and padded, on its own and centered or with a sidebar on the left, or on the right, or both... The main problem were the sidebars : depending on their number, they didn't have the same size for a given viewport, so their content didn't break accordingly; they didn't have the same **break**points.

I started by adding context classes, this approach is nothing new, Drupal themes uses this for instance : it applies some "two-sidebars" or "sidebar-first" on the &lt;body&gt;. I added these - modifier - classes  on the layout container, not the &lt;body&gt;, so I could have more than one on a single page, this is especially useful for testing, documentation and flexibility, anyway.

Then I needed to apply styles and a layout grid but was facing this multiple context situation. What I ended up doing was... yes, **mixin all the things**, literally every style. I already had a mixin to handle the layout math, but sidebars and main content needed different styles in different contexts. This is almost what it looked like in the end :

    // Contextual mixins
    @mixin with-sidebar-left {
      .l-main-wrapper--with-sidebar-first { @content; }
    }
    @mixin with-sidebar-right {
      .l-main-wrapper--with-sidebar-second { @content; }
    }
    @mixin with-two-sidebars {
      .l-main-wrapper--with-two-sidebars { @content; }
    }
    // [...]

    // MQ mixins
    @mixin when-sidebar-left-is-available {
      @media (min-width: 40em) { @content; }
    }
    @mixin when-sidebar-right-is-alone-and-aside {
      @media (min-width: 56em) { @content; }
    }
    @mixin when-sidebar-right-is-aside {
      @media (min-width: 64em) { @content; }
    }
    // [...]

    // Layout : grid calculation
    @mixin layout-grid($gutter, $left-size, $right-size...) {...}

Ok now, this looks like a lot of mixins that don't do a lot of things (except layout-grid) but when you combine them, something fun happens...

## Interesting part

    @include with-sidebar-left {
      @include when-sidebar-left-is-available {
        @include layout-grid(...);
      }
    }

    @include with-sidebar-right {
      @include when-sidebar-right-is-alone-and-aside {
        @include layout-grid(...);
      }
    }

    @include with-two-sidebars {
      @include when-sidebar-left-is-available {
        @include layout-grid(...);
      }
      @include when-sidebar-right-is-aside {
        @include layout-grid(...);
      }
    }

Meme time !

<a><img src="/attachments/mixin-all-the-things-medium.jpg" alt="Mixin all the Things meme" alt="Mixin all the Things meme" class="full" /></a>

For the sake of this article I didn't include every mixin, this extract is enough to see how *natural* it is. Another benefit I would point out is **reusability**. Mixins are the most reusable Sass feature, even ```@extend``` can't beat that because it can not be used across multiple media-queries. Some would argue this method will cause a lot of bloat, it's not entirely false. Regarding ressource weight, gzip will handle this well. Regarding rendering performance, I don't think it would be such a concern, and I'm not sure that there are better alternatives anyway.

## Conclusion

I hope these few examples helped you to see how mixins can help enhance **your** regular styling work. The purpose is to demonstrate how mixins are not only for framework-makers but can also help you better manage your code. I'm not saying you should use the same exact methods I've shown. These are only a few ways to use mixins as a way to make your code more natural, readable and learnable, even if you're not part of a team. A clearfix mixin is useful, but you can do more than that.

## Be curious

Back to high school, a few years after the calculator anecdote some new software came to town : Flash 2. The same story happened again : I didn't think I could script vector objects animation, I was wrong. Later on, I needed Flash to store user data on a server, PHP 3 was in the place, I thought I would need a smarter guy for this stuff, but again I was wrong. SQL is only for DBA! Wrong. Finally, Flash was not fast enough for my needs, so I went to HTML and CSS. I thought JavaScript and the DOM was too complicated, wrong (well, it is sometimes...). jQuery was born and I didn't even imagine writing my own plugins, wrong. The same story happened again and again, but less and less often as I gained experience.

Everytime I thought I couldn't handle a new technology, language, feature, I was wrong. Actually it turned out it always was the best move I could do. It brought me where I am today (a Lead Developer) and I'm definitely not smarter than you. So don't be stupid, be curious. Don't just try mixins. Try all the things! And share.
