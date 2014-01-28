---
date: 27 January 2014
categories: intermediate, articles
author: Julien Cabanès
summary: So your friend, co-worker, web-buddy or whomever told you about Sass, Compass ... or both. Great! Now what? In this beginner guide we take you through the first steps of getting started with Sass and Compass. We walk you through installation, creating a test project, compiling your first lines Sass to CSS and we even "mixin" a little Sass history.
---

# Mixin all the Things

There are two kinds of people in the Sass world : those who writes mixins, and those who uses it. If you're part of the first kind and in a hurry, you can skip to the [interesting part](#interesting-part), otherwise let me tell you a little story.

## Curiosity is the Key

Seventeen years ago, while in high school, a classmate showed me a graphical calculator for the first time and told me it could run games. It was inconceivable for me. How a *calculator* (an electronic thing which only does additions and multiplications, right?) could run games? The most inconceivable thing was my friend told me I could make some **myself**! I was curious enough to check, and few weeks later I wrote a bomberman-like.

## Mixins for Everybody

Back to the present. You use Sass, probably because someone told you about it, how it was smart and fun. Like a lot of Sass folks you chose to also use Compass because it saves keystrokes. Maybe you went further and installed other librairies like the official [Bootstrap Sass port](https://github.com/twbs/bootstrap-sass), or [Foundation](http://foundation.zurb.com/), or... you name it. You know what mixins are, you use them everyday, but did you really write some **yourself** ?

Mixins are the most powerful Sass feature. Some would say ```@extend``` is crazy, and it kinda is, but believe me, mixins are **undervalued**. In this article, I won't repeat what others did well so before going further you must ensure you know [how mixins work](http://thesassway.com/intermediate/leveraging-sass-mixins-for-cleaner-code/#how-mixins-work) and [how to write your own](http://thesassway.com/intermediate/leveraging-sass-mixins-for-cleaner-code/#writing-your-own) and come back because I'm going to share some interesting way of using mixins. But first let see what is the *nature* of mixins.

## Anatomy of an Abstraction
"Sasstraction" (coined term for Sass Abstraction) is a hot topic, Jeremy Keith [wrote about it](http://adactio.com/journal/6606/), Chris Coyier also [wrote about it](http://css-tricks.com/abstraction-in-web-languages-and-variables-and-stuff/) and Chris Eppstein (creator of Compass and Sass contributor) explained [why it matters](http://chriseppstein.github.io/blog/2009/09/20/why-stylesheet-abstraction-matters/) a long time ago. Mixins are powerful because its abstraction can be *high-level*.

Variables was just a beginning, you used it from day one because it's **DRY**. [Functions](http://thesassway.com/advanced/pure-sass-functions/) (especially color ones, my favorites) opened new perspectives for those variables. All in all, those features are *basic* abstractions, they just help you get DRY : you store values in one location, you use and manipulate them in another location instead of repeating them. Finally, this helps your code being more [maintenable](http://www.w3.org/People/Bos/DesignGuide/maintainability.html) and this is **a good thing**.

Once you add control directives (```@if```, ```@else```, etc...) you're adding **logic**. Depending on how you use it, the logic behind can be really smart or trivial. But smarter doesn't mean better : smart code can be hard to read, hard to learn. Vice-versa, trivial doesn't mean silly.

Mixins can also be trivial. I really love Compass but without offense a good majority of its mixins is pretty trivial, it's just about repeating vendor prefixes, and it helps! Everyday! Some are smarter like the [background](https://github.com/chriseppstein/compass/blob/stable/frameworks/compass/stylesheets/compass/css3/_images.scss) one  which handle legacy webkit syntax for instance, you wouldn't do this yourself...

Whether trivial or smart, every abstractions operates the same way : **simplifying** a more or less complex task. It can do this by :

- avoiding repetition : respect the DRY principle
- encapsulating logic : hide or reduce the complexity behind an easier API

## CSS logics
Let's forget Sass for a moment and focus on CSS. Even if you're not aware of it, I'm sure you already used logic in your stylesheets. Selectors are often read as conditions, this is a no-brainer when you think about it. For instance ```.item.active { display: block; }``` means « **if** the item is active **then** display it ».

We often forget how many abstractions are driving our devices today. From what processors do with electrons, to the code we read and write, to angry birds, the sole purpose is to bring the machine language, the binary, closer to the human language (well, english at least, generally...). Each time we add an abstraction layer, we're making the machine speaking and reading more like a human (or is it the opposite?). By *translating* this simple CSS rule, I'm doing this job **mentally**, it took me a really short time but still. Maybe we could do it with a mixin ?

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

This is definitely not the smartest code ever, and I could name my mixin more explicitly, ```display-if-active``` or whatever, but you see where I'm going. Abstraction is also a way for making things more **readable**, not because it is shorter, but more *natural*. We all know how to write a lot of LOC but even with comments it doesn't mean anything until we decrypt it. And what ? We're doing it everyday so why bother ? Actually, it matters because [we spend more time understanding code](http://www.codinghorror.com/blog/2006/09/when-understanding-means-rewriting.html) than writing it...

## Responsive logics

Responsive Web Design (RWD) generated more questions than answers. We're facing a lot of challenges and one I believe we all had is how to choose breakpoints. You may know we can't use standard screen sizes because <a href="http://www.lukew.com/ff/entry.asp?1816">there are not anymore</a>. I agree with Brad Frost (and other smarter-than-me guys) to <a href="http://bradfrostweb.com/blog/post/7-habits-of-highly-effective-media-queries/#content">let the content determine breakpoints</a> but this can be tiring because you need them at multiple places. So the first idea is to store those breakpoints in variables, sure there's nothing wrong with this, but I also like to use... yes, mixins.
    
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
    
This example is obvious but it starts to look *over-engineered*, what do you think ? At first, I was confused like « Is it really a good idea ? Why not just using a variable ? ». But then, I used it in another case, and another case. I came back 2 days later and it started to take sense, it really was more readable and more maintenable.

I could not only change the min-width, but also add other logic behind like using another way of including media queries like [Breakpoint](https://github.com/Team-Sass/Breakpoint) for instance. The most interesting point was the appearance of some connections between random breakpoints I didn't expect and consider before. Some elements needed adjustments as I was progressing in the project and this technic helped me refactor without hassle. I decided to keep the use of the "when" prefix for every media-queries mixins because it worked well for my team and I.

## Moving the mixin forward

The last case I want to share is about layouts, page layouts. We often deal with only 2 or 3 different layouts on a project, but for my last project I had 6 different layouts. To say it short, depending on the template, the main content could be alone and full-width, alone and padded, alone and centered or with a sidebar on the left, or on the right, or both... The main problem was concerning the sidebars : depending on their number, they didn't have the same size for a given viewport, so their content didn't break accordingly, so they didn't have the same **break**points.

I started by adding context classes, this approach is nothing new, Drupal themes uses this for instance : it applies some "two-sidebars" or "sidebar-first" on the &lt;body&gt;. I added those - modifiers - classes  on the layout container, not the &lt;body&gt;, so I could have more than one on a single page, this is especially useful for testing, documentation and flexibility, anyway.

Then I needed to apply styles and layout grid but was facing this multiple context situation. What I ended up doing was... yes, **mixin all the things**, litterally every styles. I already had a mixin to handle the layout maths, but sidebars and main content needed different style on different context. This is almost what it looks like at the end :

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

Ok now, this looks like a lot of mixins which doesn't do a lot of things (except layout-grid) but when you combined them, something fun happens...

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

For the sake of this article I didn't include every mixins, this extract is enough to see how *natural* it is. Another benefit I would point out is **reusability**. Mixins are the most reusable Sass feature, even ```@extend``` can't beat that because it can not be used across multiple media-queries. Some would argue this method will cause a lot of bloat, it's not entirely false. Regarding ressource weight, gzip will handle this well. Regarding rendering performance, I don't think it would be such a concern, and I'm not sure to have better alternatives anyway.

## Conclusion

I hope those few examples helped you to see how mixins can help enhance **your** regular styling work. The purpose is to demonstrate how mixins is not only for framework makers but can also help you better manage your code. I'm not saying you should use the same exact methods I've shown. Those are only a few ways to use mixins as a way to make your code more natural, readable and learnable, even if you're not part of a team. A clearfix mixin is useful, but you can do more than that.

## Be curious

Back to highschool, few years after the calculator anecdote a new software came in town : Flash 2. The same story happened again : I didn't think I could script vector objects animation, I was wrong. Later on, I needed Flash to store user data on a server, PHP 3 was in the place, I thought I would need a smarter guy for this stuff, but again I was wrong. SQL is only for DBA! Wrong. Finally, Flash was not fast enough for I need, so I went to HTML and CSS. I thought JavaScript and the DOM was too complicated, wrong (well, it is sometimes...). jQuery was born and I didn't even imagine to write my own plugins, wrong. The same story happened again and again, but less and less as I won experience.

Everytime I thought I couldn't handle a new technology, language, feature, I was wrong. Actually it turned out it always was the best move I could do. I brought me where I am today (a Lead Developer) and I'm definitely not smarter than you. So don't be studid, be curious. Don't just try mixins. Try all the things! And share.