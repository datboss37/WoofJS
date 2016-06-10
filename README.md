# WoofJS - *JavaScript Unleashed*

WoofJS is a JavaScript library for making web and mobile games and animations.

It was originally inspired by Scratch and can help ease the trasition to JavaScript.

WoofJS is developed with :heart: by [The Coding Space](http://thecodingspace.com).

<b>Notice</b>: *This is new software and is under rapid development, so expect things to break and be frequently changed. We expect our first stable release will come out by the end of Summer 2016. Until our first stable release, this software <b>only runs on desktop Chrome</b>, at which point we will support all modern desktop and mobile browsers.*

## Getting Started

You can either <a href="https://jsbin.com/lekovu/edit?js,output" target="_blank">clone this JSBin</a> or follow the steps below to setup your first WoofJS project.

1) Put the Woof library between the `<head>` tags.
```html
<script src="https://cdn.rawgit.com/stevekrouse/WoofJS/6b1615bad6b59a23313ad7a72e9c8bd3659b3fa4/woof.js"></script>
```
2) Throw in some JavaScript, and tell Woof to fetch it.
```javascript
var project = new Woof.Project({fullScreen: true, debug: ["project.keysDown", "project.mouseX", "project.mouseY", "timer"]}); 

// Set the backdrop URL (preferably of similar dimensions to your canvas)
project.setBackdropURL("http://pic.1fotonin.com//data/wallpapers/24/WDF_715771.jpg");

// Add an image via a url
var dawg = project.addImage({url: "http://t11.deviantart.net/e9EiWxrehU301T_fNZMt01PyBSA=/300x200/filters:fixed_height(100,100):origin()/pre11/c3dc/th/pre/f/2008/295/c/3/c3f0e39599272ba0b3bcdbcdda81ace2.png", x: 0, y: 0, imageHeight: 100, imageWidth: 135});

// Make it move with the arrow keys by checking which keys are down
project.forever(() => {
  if (project.keysDown.includes("LEFT")){
    dawg.x -= 5;  // move left by 5
  }
  if (project.keysDown.includes("RIGHT")){
    dawg.x +=5; 
  }
  if (project.keysDown.includes("UP")){
    dawg.y += 5; 
  }
  if (project.keysDown.includes("DOWN")){
    dawg.y -= 5; 
  }
});

var timer = 20;  // make the timer start at 20
var timerText = project.addText({x: 0, y: project.maxY - 20, size: 20, color: "white"}); // add text that diplays the timer
project.every("1", "second", () => {
  if (timer === 0){ project.stopAll(); } // stop everything when the timer reaches 0
  timer--;   // make the timer go down every second
  timerText.text = "Timer: " + timer;   // change the text to refer to the timer's new value every second
});
```

## Woof.Project

### Creating a Project

  - **Reccomended**: Create a full-screen project: 

```javascript
var project = new Woof.Project({fullScreen: true, debug: ["project.keysDown", "project.mouseX", "project.mouseY"]});
```

  - Create a project with a fixed height and width:

```javascript
var project = new Woof.Project({height: 500, width: 350});
```

  - Create a project with a pre-existing `<canvas id="project">` element by id:

```javascript 
var project = new Woof.Project({id: "project"});
```

### Setting the Backdrop

  - Set/change the backdrop to an image URL: 
  

```javascript
project.setBackdropURL("http://example.com/img.jpg");
```

  - Set/change the backdrop to an color: 

```javascript
project.setBackdropColor("blue");
```
  
### Sensing
  
  - Mouse X: `project.mouseX`
  - Mouse Y: `project.mouseY`
  - Mouse down?: `project.mouseDown`
  - List of keys currently pressed: `project.keysDown`
  - Is 'A' pressed?: `project.keysDown.includes('A')`
  - Is the space key pressed?: `project.keysDown.includes(' ')`
  - Is the up key pressed?: `project.keysDown.includes('UP')`
  - Right edge of the screen: `project.maxX`
  - Left edge of the screen: `project.minX`
  - Top edge of the screen: `project.maxY`
  - Bottom edge of the screen: `project.minY`
  - Random X value on the screen: `project.randomX()`
  - Random Y value on the screen: `project.randomY()`
  - Width of the screen: `project.width`
  - Height of the screen: `project.height`

  
### Responding to Events

- Do something on-click once: `project.onClick((mouseX, mouseY) => { ... });`

  
### Stopping Everything

To stop your whole project and freeze everything on the screen:


```javascript
project.stopAll();
```

### Project Control Flow

There are two types of commands in JavaScript:

1) **Synchronous**: "Do this immediately and move on when it's done."

*Synchronous example in real life*: Open heart surgery. When a doctor begins open heart surery on a patient, she stays in the operating room with the patient until the surgery is done and the patient is stitched back up. She doesn't start open surgery with one patient and then move on to another room where she begins operating on a second patient before the first operation is done. She starts and finishing one operation before starting a second.

2) **Asynchronous**: "Start this immediately, but don't wait till it's done. Move on to the next command immediately after you start this command (which might be before this command is done)."

*Asynchronous example in real life*: Ordering food or drinks. First, let's imagine what a synchronous resturant would look like. A waiter would come to your table, take 1 person's order, rush it back to the kitchen, wait for the kitchen to finish with that person's order, and then bring it back to that person. Finally once this first order is taken care of, the waiter would ask the 2nd person at the table for their order. Of course this would be ridiculous. It makes much more sense for a resturant to process its customers' orders asynchronously. That is, after a waiter takes one person's order, he is free to take another person's order before finishing the first order. This allows the resturant to do multiple things at once, which is ultimately faster for some types of tasks.

Most commands are synchronous. For example, if-statements, setting or changing variables, and calling most Woof methods like `rectangle.move(10)` are all synchronous commands.

`project.forever` is an example of an asynchronous command. Think about it: if `project.forever` told the computer to wait until it was done, it would never move on to the next line.

`project.repeat`, `project.repeatUntil`, `project.every`, `project.after` are also asynchronous.

Asynchronous commands become quite confusing when you something happen after an asynchronous command is *finished*. If you want something after the 10th time you `project.repeat` it, you can't just put it on the line below the `project.repeat`. Why? Because the line below `project.repeat` happens immediately after the asynchronously command *starts*, not after it finishes. 

So how do we tell the computer to do something after an asynchronous command is *finished*? This is different for each language, but for Woof's `project.repeat` and `project.repeatUntil`, add a function as an extra parameter to those commands that specifies what should happen after the asynchonous command is *finished*. This is called a "callback" because that function is "called back" and run *after* the main part of the command is done.

Also, be sure not to nest async commands within each other's main body. For example if you want to make an image move in a square for forever, you can't just put four nested repeats inside a forever. Instead you have to use recursion and tell your repeat code to start-over only after the 4th repeat is finished finished. (If you put a repeat in a forever, it'll  keep starting new repeats for forever really quickly. It's like repeatedly asking a waiter for seconds before your first course has arrived.)


  - Repeat 10 times: 

```javascript
project.repeat(10, () => { 
  NAME.x++;
});
```

  - Do something after done repeating:

```javascript
project.repeat(10, () => { 
  NAME.x++;
}, () => {
  NAME.color = "blue";
});
```

  - Chaining repeats:

```javascript
project.repeat(100, () => {
  NAME.angle = Woof.RIGHT;
  NAME.move(1);
}, () => { 
  project.repeat(100, () => {
    NAME.angle = Woof.UP;
    NAME.move(1);
  }, () => { 
    project.repeat(100, () => {
      NAME.angle = Woof.LEFT;
      NAME.move(1);
    }, () => { 
      project.repeat(100, () => {
        NAME.angle = Woof.DOWN;
        NAME.move(1);
      });
    });
  });
});
```

  - Repeat constantly until (you have to put the condition in "quotes"): 

```javascript
project.repeatUntil("IMAGE_NAME.y < project.minY", () => {
  IMAGE_NAME.y -= SPEED;
});
```

  - Do seomthing after a `repeatUntil`:

```javascript
project.repeatUntil("IMAGE_NAME.y < project.minY", () => {
  IMAGE_NAME.y -= SPEED;
}, () => {
  project.stopAll();  
});
```  

  - Do this constantly forever: 

```javascript
project.forever(() => { 
  CIRCLE_NAME.radius = CIRCLE_NAME.distanceTo(project.mouseX, project.mouseY);
});
```
  
  - Do this every second: 

```javascript
var timer = 20;
var timerText = project.addText({x: 0, y: project.maxY - 20, size: 20, color: "white"});
project.every("1", "second", () => {
  if (timer === 0){ project.stopAll(); }
  timer--;
  timerText.text = "Timer: " + timer;
});
```

  - Do this when a condition (in "quotes") is true. This is a short-hand for a forever-if statement.

```javascript
project.when('project.keysDown.includes("LEFT")', () => {
    rectangle.x -= 5; 
});
```
  
  - Do this after one second: 

```javascript
project.after(1, "second", () => {...});
```

## Woof Objects (Images, Text, Rectangles, and Circles)

When creating a new Image, Text, Rectangle or Circle, you may use the following parameters:

```javascript
  var IMAGE_NAME = project.addImage({x: 0, y: 0, angle: 0, rotationStyle: "ROTATE", showing: true});
  var TEXT_NAME = project.addText({x: 0, y: 0, angle: 0, rotationStyle: "ROTATE", showing: true});
  var CIRCLE_NAME = project.addCircle({x: 0, y: 0, angle: 0, rotationStyle: "ROTATE", showing: true});
  var RECTANGLE_NAME = project.addRectangle({x: 0, y: 0, angle: 0, rotationStyle: "ROTATE", showing: true});
```

### Detecting

  - Get the x-position: `NAME.x`
  - Get the y-position: `NAME.y`
  - Get the angle: `NAME.angle`
  - Get the width: `NAME.width()`
  - Get the height: `NAME.height()`
  - Touching another thing?: `if (NAME.touching(OTHER_NAME)) { ... }`
  - Mouse over?: `if (NAME.mouseOver()) { ... }`
  - Do something on click: `NAME.onClick((mouseX, mouseY) => { ... });`
  - Clicking and holding?: `if (NAME.mouseDown()) { ... }`
  - Get the distance between two things: `NAME.distanceTo(OTHER_NAME);`
  - Get the distance to an X,Y: `NAME.distanceTo(X, Y);`

### Changing

  - Set the X position: `NAME.x = 200;`
  - Change the Y position: `NAME.y += 10;`
  - Set the angle: `NAME.angle = 30;`
  - Set the angle using direction: `NAME.angle = Woof.LEFT;` 
  - Turn left: `NAME.turnLeft(10);`
  - Turn right: `NAME.turnRight(10);`
  - Don't rotate the sprites costume when the angle changes: `NAME.setRotationStyle("NO ROTATE");`
  - Rotate left to right when it points in a direction and moves `NAME.setRotationStyle("ROTATE LEFT RIGHT");`
  - Hide: `NAME.showing = false;`
  - Move in the direction of the angle: `NAME.move(10);`
  - Send to the back layer: `NAME.sendToBack();`
  - Send to the front layer: `NAME.sendToFront();`
  - Point towards another thing: `NAME.pointTowards(OTHER_NAME)`
  - Point towards an X,Y : `NAME.pointTowards(project.mouseX, project.mouseY)`
  - Delete: `NAME.delete();`
  - Draw a line behind a sprite: `NAME.penDown = true;`
  - Stop drawing a line behind a sprite: `NAME.penDown = false;`
  - Set pen color: `NAME.penColor = "blue";`
  - Set pen width: `NAME.penWidth = 10;`


### Image
In addition to the default parameters (`x`, `y`, `angle`, `rotationStyle`, and `showing`), you may use these parameters to create a new image: 

```javascript 
var IMAGE_NAME = project.addImage({url: "http://www.loveyourdog.com/image3.gif", imageWidth: 30, imageHeight: 30});
```
  - Add another image: `IMAGE_NAME.addImageURL("http://www.urdu-english.com/images/lessons/beginner/shapes/shapes-pics/rectangle.png");`
  - Change the image: `IMAGE_NAME.image = 0;`
  - Set the width: `IMAGE_NAME.imageWidth = 10;`
  - Set the height `IMAGE_NAME.imageHeight = 20;`

### Circle
In addition to the default parameters (`x`, `y`, `angle`, `rotationStyle`, and `showing`), you may use these parameters to create a new circle:

```javascript
var CIRCLE_NAME = project.addCircle({radius: 10, color: "black"});
```
  - Change the radius: `CIRCLE_NAME.radius = 15;`
  - Change the color: `CIRCLE_NAME.color = "blue";`

## Rectangle

In addition to the default parameters (`x`, `y`, `angle`, `rotationStyle`, and `showing`), you may use these parameters to create a new rectangle: 

```javascript
var RECTANGLE_NAME = project.addRectangle({rectangleHeight: 10, rectangleWidth: 20, color: "pink"});
```
  - Change the width: `RECTANGLE_NAME.rectangleWidth = 20;`
  - Change the height: `RECTANGLE_NAME.rectangleHeight = 45;`
  - Change the color: `RECTANGLE_NAME.color = "purple";`
  

## Line

In addition to the default parameters (`angle`, `rotationStyle`, and `showing`), you may use these parameters to create a new line between (`x`,`y`) and (`x1`, `y1`): 

```javascript
var LINE_NAME = project.addLine({x: -100, y: 100, x1: 10, y1: 20, color: "pink", lineWidth: 10});
```
  - Change the x startpoint: `LINE_NAME.x = -100;`
  - Change the y startpoint: `LINE_NAME.y = 100;`
  - Change the x endpoint: `LINE_NAME.x1 = 10;`
  - Change the y endpoint: `LINE_NAME.y1 = 20;`
  - Change the width: `LINE_NAME.lineWidth = 5;`
  - Change the color: `LINE_NAME.color = "purple";`

### Text

In addition to the default parameters (`x`, `y`, `angle`, `rotationStyle`, and `showing`), you may use these parameters to create new text: 

```javascript 
var TEXT_NAME = project.addText({text: "Text", size: 12, color: "black", fontFamily: "arial", textAlign: "center"});
```
  - Set the text value: `TEXT_NAME.text = "Sample Text";`
  - Set the font size: `TEXT_NAME.size = 20;`
  - Set the font color: `TEXT_NAME.color = "white";`
  - Set the font color to a hex value: `TEXT_NAME.color = "#32CD32";`
  - Set the font family: `TEXT_NAME.fontFamily = "arial";`
  

### Clones

If you want to make many objects that look and act very similar to each other, it's pretty straight-forward:

```javascript
project.every(4, "second", () => {
  var CLONE_NAME = project.addCircle ({radius: 10, color: "pink", x: project.randomX(), y: project.randomY()});
  project.forever(() => { 
    CLONE_NAME.x++; 
    if (CLONE_NAME.x > project.maxX) {
      CLONE_NAME.delete();
    }
  });
});
```

## Helper Functions

1) Get a random integer between any two numbers

```javascript
var number = Woof.randomInt(10, 20);
```
2) Repeat something 10 times

```javascript
Woof.repeat(10, (time) => {
  console.log(`I'm going to repeat this ${10-time} more times!`)
});
```
3) Get a random integer between any two numbers: `Woof.randomInt(10, 20);`

4) Get the current hour: `Woof.hour();`

5) Get the current hour in military time: `Woof.hourMilitary();`

6) Get the current minute: `Woof.minute();`

7) Get the current second: `Woof.second();`

8) Get the current day of the month (1-31): `Woof.dayOfMonth();`

9) Get the current day of the week (Monday-Sunday): `Woof.dayOfWeek();`

10) Random color: `Woof.randomColor()`


## Cloud Data via Firebase

Cloud data can be used to create high scores, logins, and multiplayer games.

1) Login to [Firebase](https://firebase.google.com/) with your Google account.

2) Create a new project.

3) Make reading and writing to your database possible via the Database rules tab.

4) Whitelist your project's domain via the Auth sign-in tab.

5) Copy the Firebase config into your `Woof.Project`:

```javascript
project.firebaseConfig({
    apiKey: "AIzaSyAWa4XzsdE5haWBWq4fTef2Ko1dpbsE4qM",
    authDomain: "sample-a81f9.firebaseapp.com",
    databaseURL: "https://sample-a81f9.firebaseio.com",
    storageBucket: "",
});
```

6) Set some data:

```javascript
project.setCloud("high_score", 100);
project.setCloud("player1", {x: project.mouseX, y: project.mouseY});
```

7) Get some data:

```javascript
project.getCloud("high_score", 0) // returns 100, with a default of 0
project.setCloud("player1", {x: 0, y: 0}) // returns  {x: 14, y: 104}, with a default of {x: 0, y: 0}
```

## [Learnable Programming](http://worrydream.com/LearnableProgramming/)

Despite its many [noted](http://worrydream.com/LearnableProgramming/) flaws, Processing still remains the dominent graphics programming framework for beginners. Woof strives to solve the same problems as Processing, and be a beginner-friendly graphics frameworks for art, animation and game development, but it also hopes to improve upon Processing's main flaws:

  - **opaque parameters setting** - In Woof, all object properties can be set explicitly with dot notation or as named-parameters in the constructor.
  - **side effects in render** - In Woof, you can cause state to change in response to events or intervals. You don't have to hook your effectful code into your render method to make things move.
  - **render** - In Woof, (like ReactJS) we take care of rendering your objects onto the canvas for you. This means you don't even have to think about how your view layer works. It just does.
  - **hidden state** - In Woof, there's no hidden view-layer state. If you make one circle red, only that circle is red.
  - **non-modular** - In Woof, because all view-layer state is encapulated into objects, it allows you to easily build modular and functional code without worrying about side effects.
  - **poor decomposition** - In Woof, you can have infinitely many objects listen to infinitely many events. You don't have to funnel all of your code through the same top-level events that forces you to build tangled code.
  - **lack of identity / metaphor** - Woof steals Scratch's and Smalltalk's everything-is-an-object-that-you-can-see-on-the-screen metaphor. It also takes the pen, angles, turning and moving athropomorphic metaphors from LOGO.

## Showing off

  - Making a page of draggable elements takes Pixi.js [80 lines](http://www.goodboydigital.com/pixijs/examples/8/), while using WoofJS it only takes [20 lines](https://jsbin.com/fuheki/edit?js,output).
  - Making a bunch of crazy bouncing images takes Pixi.js [254 lines](http://www.goodboydigital.com/pixijs/bunnymark/), but only takes WoofJS [32 lines](http://output.jsbin.com/nuhoben).
  - Making pong using JQuery and Underscore.js takes [146 lines](https://jenniferdewalt.com/pong.html), while it takes WoofJS [under 60 lines](http://output.jsbin.com/keborur) (and our version has more elements PLUS it works on mobile!).
  - Making this analog clock takes [125 lines](https://jenniferdewalt.com/analog_clock.html) of JavaScript, but only [40 lines](http://output.jsbin.com/yaciqe) using WoofJS.
  - Recreating the insanely viral game [Flappy Birds](http://output.jsbin.com/rarexo) takes under 70 lines using WoofJS.
