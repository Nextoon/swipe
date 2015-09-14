# swipe

##Abstract

This specification defines the features and syntax for Swipe, a mark-up language for describing interactive, media-rich and animated documents.

##Status of this document

This specification describes the current snapshot of the Swipe 0.1, which is still under development and may change drastically.

##1. Introduction

Swipe is a domain-specific, declarative language for non-developers (such as designers, animators, illustrators, musicians, videographers and comic writers) to create interactive documents (books and presentations) that contains photos, videos, vector graphics, animations, voices, musics and sound effects, which will be consumed on touch-enabled devices (such as smartphones, tablets and touch-enabled set-top-boxes). 

Since the introduction of iPhone, the capability of those mobile devices advanced significantly with faster CPU/GPU, a large amount memory and various sensors, but taking a full advantage of those capability is not easy.

While "Native programming" (such as in Objective-C, Swift, Java, and etc.) gives the best possible performance and the user experience, the development cost is very expensive, and supporting multiple devices is a nightmare.

Using a "cross-platform development environment", such as Unity, Coco3D, Corona and Flash has some advantages over native programming, but it still requires a "procedural programming", which only skilled developers are able to do. 

Building interactive applications on top of HTML browsers became possible because of HTML5, but it still has many issues. Providing a good user experience is very difficult (this is why Facebook gave up this approach), and the development cost is as expensive as native or cross-platform development, requireing skilled developers.

People often debate over those three approaches, but they often overlook one important disadvantage of those three approaches. All those approaches require "procedural programming", which can be done only by skilled developers and are very expensive, error-prone and time-consuming. 

This disadvantage makes it very difficult for those creative people to make quick prototypes and experimental works, just like an artist makes sketches with pencils and erasers. It is economically impossible for individual creators to create interactive, media-rich books and publish them. 

Swipe was born to fill this gap. It allows non-developers to create interactive and media-rich documents without any help from developers. The declarative nature of Swipe language (and the lack of "procedual programming environment") makes very easy to learn, write and read. It also makes it easy to auto-generate documents (from data) and create authoring environments.   

##2. Document

A **Document** is a UTF8 JSON file, which consists of a collection of **Pages**. The order of **Pages** in the collection is significant, and they will be presented to the user in the order specified in the collection. 

Here is a **Document** with two pages:

```
{
    "pages": [
        {
            "elements": [
                { "text":"Hello World!" }
            ]
        },
        {
            "elements": [
                { "text":"Good Bye!" }
            ]
        }
    ]
}
```
When the user opens this document with a Swipe viewer, the user will see only the first page with text "Hello World!" in the middle of screen. The user needs to swipe up (since the vertical scrolling is the default) to see the second page.

### Document Properties

- **title** (String): Title of the document, optional
- **bc** (Color): Background color, defalut is *darkGray*
- **dimension** ([Int, Int]): Dimension of the document, default is [320, 568]
- **paging** (String): Paging direction, *vertical* (default), *leftToRight* or *rightToLeft*
- **orientation** (String): Document orientation, *portrait* (default) or *landscape*
- **pages** ([Page+]): Collection of **Pages** 
- **scene** ({Name:Scene}): Named **Scenes** dictionary
- **elements** ({Name:Element}): Named **Elements** dictionary
- **paths** ({Name:Path}): Named **Paths** dictionary

##3. Page

**Page** consists of a collection of **Elements**. The order of **Elements** in the collection is significant, those elements will be rendered in the specified order (from bottom to top). 

Here is a **Document** with a **Page**, which has two **Elements**. 

```
{
    "pages": [
        {
            "elements": [
                { "x":50, "y":100, "w":100, "h":100, "bc":"red" },
                { "x":100, "y":150, "w":100, "h":100, "bc":"blue" },
            ]
        },
    ]
}
```

### Page Properties

- **elements** ([Element+]): Collection of Elements
- **bc** (Color): Background color, default is *white*
- **transaction** (String): Inter-page transaction style, *scroll* (default), *fadeIn* or *replace*
- **animation** (String): Animation timing, *auto* (default), *pause* or *scroll*
- **duration** (Float): Duration of the auto animation in seconds, default is 0.2 seconds
- **repeat** (Boolean): Repeat rule of the auto animation, default is *false*
- **rewind** (Boolean): Rewind rule of the auto animation when the user leaves the page, defaul is *false*
- **scene** (String): Name of the scene, default is *
- **audio** (URL): Specifies the sound effect to be played in sync with the animation
- **speech** (String): Specifies the text-to-speech to be played in sync with the animation
 
##4. Paging direction, inter-page transition and animation

The paging direction is defined by the "paging" property of the **Document**. It must be either *vertical*, *leftToRight* or *rightToLeft*, and the default is *vertical*.

The inter-page transition is defined by the "transition" property of the proceding **Page**. It should be either *scroll*, *fadeIn* or *replace*. The default is *scroll* unless the "animation" property is *scroll*. If the "animation" property is *scroll*, the default is *replace*.

### Value for the "transition" property

- *scroll*: regular scrolling behavior (default)
- *fadeIn*: The proceding **Page** will fade-in while the user drags it in.
- *replace*: The proceding **Page** will replace when the user start dragging.

The "animation" property defines the timing of animation defined on the **Page**, and it must be either *auto*, *pause* or *scroll*. If "auto" is specified, the animation will start automatically after the completion of the paging action. If *scroll* is specified, the animation will be played while the user is scrolling the page. 

### Values for "animation" property

- *auto*: The animation on the **page** will be played after finish scrolling to this page (default)
- *pause*: The animation on the **Page** will not automatically start
- *scroll*: The animation on the **Page** will be performed while the user scrolls the page

##5. Scene

A Scene defines a set of properties and **Elements** to be shared among multile **Pages**. It also defines a background music to be played when one of those **Pages** is active.

A **Page** is always associated with a Scene, either explicity with the "scene" property or implicitly with the default scene with name "*". 

The **Page** inherits all the properties from the associated **scene**, including **Elements**. When the same property is specified both in the **Page** and the **Scene**, the value specified in the **Page** will be used. The only exception to this rule is *Elements*, which will be merged and *Elements* specified in the **Scene** are placed below **Elements** specified in the page.

### Scene specific properties 
- bgm (URL): Specifies the background music to play 

##6. Element

An *Element* is a visible entity on a Page. It occupies a specified rectangle area within a page. An *Element* may contain child *Elements*. 

### Element properties

- **element** (String): the name of the named **Element** to inherit properties from
- **x** (Float or Percent): x-position of the element, default is 0
- **y** (Float or Percent): y-position of the element, default is 0
- **w** (Float or Percent): width of the element, default is "100%"
- **h** (Float or Percent): height of the element, default is "100%"
- **bc** (Color): background color, default is *clear*
- **clip** (Boolean): Specifies clipping behavior, default is false
- **borderWidth** (float): Width of the border, default is 0
- **borderColor** (Color): Color of the border
- **text** (String): Text to display
- **textAlign** (String): Text alignment, *center* (default), *left* or *right*
- **fontSize** (Float or Percent): Font size
- **textColor** (Color): Color of the text
- **img** (URL):
- **mask** (URL):
- **path** (String):
- **lineWidth** (Float):
- **fillColor** (Float):
- **to** ():
- **loop** ():
- **action** ():

##7. Animation

##8. Loop Animation

