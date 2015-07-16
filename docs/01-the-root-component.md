#Build an Angular 2 App: The Root Component

Angular 2 is in active development right now, but is still in an alpha state. In fact, at the time of writing this article, the alpha 31 release was just cut. There are a lot of things in flux, but the platform has enough meat on it that you can start playing with it, and see just what the next version of Angular has to offer.

**And that is exactly what we are going to do; build a small application using Angular 2.**

##Getting Started

***Note:*** *I happen to be using ASP.Net 5, but the server portion of this example is largely irrelevant.*

There are several different ways you can get started with the Angular 2 bits, but the easiest way is to simply use the bundles hosted on https://code.angularjs.org/.

That's exactly what we are going to do.

In our main page, we need to include the following script tags at the bottom of the page in order to get up and running with Angular 2.

```html
<!-- Traceur Runtime -->
<script src="https://github.jspm.io/jmcriffey/bower-traceur-runtime@0.0.87/traceur-runtime.js"></script>

<!-- System.js -->
<script src="https://jspm.io/system.js"></script>

<!-- Angular 2 -->
<script src="https://code.angularjs.org/2.0.0-alpha.31/angular2.dev.js"></script>
<script src="https://code.angularjs.org/2.0.0-alpha.31/router.dev.js"></script>
```

##ES6 Polyfills

You will notice that the first two things we import are the Traceur Runtime and System.js. These are basically polyfills that allow us to use some ES6 features in the browser despite their not being implemented yet.

System.js gives us module loading, which aligns really nicely with ES6 and TypeScript.

##Setting The Stage

Angular 2 is all about embracing components. That means we need to start thinking about our application as a tree of components, rather than a disconnected set of controllers and views. 

Every tree has a root, and our application is no exception. We want to load our application right into the page by dropping in some kind of component that will represent the entry point of our application.

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- head stuff -->
  </head>
  <body>
  
    <contact-manager>
    
		<h1>Loading...</h1>
		
    </contact-manager>
    
    <!-- scripts -->
  </body>
</html>
```

You can see we are dropping in our custom `<contact-manager>` component directly into the body of the page. When our application bootstraps, this is where we want it to go. The `<h1>Loading...</h1>` we put right into the middle is just temporary content. When the real component loads, anything inside that tag will get replaced.

##Application Structure

Our application structure is actually going to be really simple for now, basically in our root folder we are going to have an `app` folder where our Angular application will reside.

```
wwwroot
│   index.html
│   
└───app
    │   contact-manager.ts
```

##The Application Component

Obviously we haven't actually created our `contact-manager` component yet, so let's go ahead and do that.

```typescript
import {Component, View, bootstrap} from 'angular2/angular2';

@Component({
	selector:'contact-manager'
})
@View({
	template:'<h1>Welcome to {{title}}</h1>'
})
export class ContactManager { 
	title:string;
	
	constructor(){
		this.title = "Contact Manager";
	}
}

bootstrap(ContactManager);
```

This is about as simple a component as we can possibly get, but there are several interesting and new things going on here, so let's dig through them one by one.

###ES6 Module Imports

Even though I am using TypeScript in this example *(Angular 2 is being written in TypeScript)*, everything you see, with the exception of the `:string` type annotation, is pure ES6/7. The `import` keyword, is pulling in dependencies from the Angular 2 bundle so I can create and bootstrap my component.

###Decorators

Decorators are the funny `@foo` syntax you see in the code, and they are a [new proposal for ES7](https://github.com/wycats/javascript-decorators). The good news is that decorators are essentially just functions that can modify the thing that they decorate.

>A decorator is:
>
>an expression
>that evaluates to a function
>that takes the target, name, and property descriptor as arguments
>and optionally returns a property descriptor to install on the target object
>
> source: https://github.com/wycats/javascript-decorators#detailed-design

In this instance, `@Component` and `@View` are ways of adding meta-data to the class that Angular will use to find, and load our component into the DOM. For instance, `selector:'contact-manager'` is the CSS selected that angular will use to locate our component. While a `@Component` is limited to being only an element, there is also a `@Directive` decorator that can take advantage of a larger range of CSS selectors. This is a much simpler model than Angular 1, and provides a far greater degree of control.

For example, if you wanted to match only a `div` with an attribute `name`, that had a specific value of `josh`, *and* had the CSS class `crazy-selector`, your selector would look like this:

```typescript
@Directive({
	selector: 'div[name=josh].crazy-selector'
})
```

The markup for something so crazy would look like this:

```html
<!-- selector: 'div[name=josh].crazy-selector' -->
<div name="josh" class="crazy-selector">This will get matched</div>

<div class="crazy-selector">But not this</div>
```

###Classes

You may have heard that controllers were going away in Angular 2, but that really isn't true. It's more accurate to say that all **components** and **directives** are essentially just controllers with some meta-data.

In our simple little application, our controller is using the new `class` syntax from ES6. This doesn't mean that you *have* to use classes. At the end of the day, it is just syntactic sugar over constructor functions and `prototype`... so you could write this same thing in ES5.

```typescript
export class ContactManager {
	title:string;
	
	constructor(){
		this.title = "Contact Manager";
	}
}
```

What you don't see in here is any sort of `$scope` construct. That's because in Angular 2, data binding happens directly against the component, and there is no intermediary that you have to deal with.

###Bootstrapping

The last thing we need to do is actually bootstrap our application by telling Angular which component to use as the root. That's this little bit of code here:

```typescript
import {Component, View, bootstrap} from 'angular2/angular2';

//Snip...

//load our component into the DOM
bootstrap(ContactManager);
```

Notice there are no funky magic strings to deal with here... just a concrete instance of our `ContactManager` component. Angular will look for the specified `selector` in the DOM and bootstrap our application by loading the component into the element that matches.

**Pro Tip:** `bootstrap` returns a promise that you can use to log information out to the console in case your application fails to load. It will help with debugging, or even displaying a friendly error message to the user.

```typescript
bootstrap(ContactManager)
	.then(app => {
		console.log('Bootstrap Successful');
	}, err => {
		console.error(err);
	});
```

##Compiling The TypeScript

Since we are using TypeScript, we can't just send that down to the browser and expect it to work. Well... we could actually use the TypeScript transpiler directly with System.js to compile the TypeScript files directly in the browser, but I want to walk through using the compiler directly.

The first step is to install the compiler, which you can do with **npm**. In this case we actually want to use 1.5, but it is still in beta, so you can use this command to install it globally.

```
npm install -g typescript@1.5.0-beta
```

**Important: ** Once you have it installed, make sure you are actually running the correct version by using this command.

```
tsc -v
```

**Important: ** If you don't see `message TS6029: Version 1.5.0-beta` printed on the commandline, then you may have a previous version of TypeScript installed in your `PATH` environment variable, that you will need to manually remove. This is pretty typical if you have been using Visual Studio, so just be aware.

###TypeScript Definition Files

In order for TypeScript to figure out how to interpret the `angular2/angular2` stuff, especially the annotations, we need to give the compiler a little bit of help. This is achieved through what are called [TypeScript Definition Files](http://www.typescriptlang.org/Handbook#writing-dts-files). Simply put, they are meta-data files that describe the public API of a library like **jQuery** or in this case, **Angular 2**.

***Where do you get these files?***

There is an entire repository of them up on a [GitHub project called DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped), and luckily the creator has put together a utility called `tsd` to pull these files into your project that works much like `npm`.

You can get detailed information on [the official tsd site](http://definitelytyped.org/tsd/), but all we really need to do for now is install the utility using npm, and then use it to install the **angular2** definition files.

```
npm install tsd -g
tsd install angular2 --resolve --save
```

This will create a `/typings` folder in your project that you can later reference in order to let TypeScript know about all your dependencies. There are a couple of difference ways to do this, but for now you can simply put this special reference comment at the top of your `contact-manager.ts` file.

```typescript
/// <reference path="../typings/tsd.d.ts" />
import {Component, View, bootstrap} from 'angular2/angular2';
```

###Compiling With tsc

Once you have your definition files, and the compiler, you can run it against your application folder. There are several options we need to pass into the compiler, so instead of doing that on the commandline, TypeScript 1.5 allows us to create a special file called `tsconfig.json` where it will read the compiler options from.

As an added benefit, if we stick this file into our `/app` directory, then TypeScript will compile all the files in that directory unless we tell it otherwise.

For detailed information you can read the [specifications for the `tsconfig.json` file](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json).

The TypeScript compiler config file for this project looks like this:

```json
{
	"compilerOptions": {
		"target": "ES5",
		"module": "commonjs",
		"sourceMap": true,
		"emitDecoratorMetadata": true,
		"watch": false,
		"removeComments": true
	}
}
```

Now we can compile all our `.ts` files from the commandline with this command:

```
tsc --project .\app
```

##Loading The Application

Now that we have compiled our TypeScript down into JavaScript, the final piece we need to make this all work, is to load our application resources into the browser. 

Because the application is using ES6 style module syntax, we are going to use [System.js as our module loader](https://github.com/systemjs/systemjs).

The magic that makes it all happen is this tiny little script block that we need to stick at the very bottom of our page. It tells System.js where our application code lives, and then loads our root contact manager component into the browser. **If there were any other dependencies, System.js would take care of loading them in for us.**

```html
<body>

	<contact-manager></contact-manager>

	<!-- Other scripts: Traceur, System, Angular2 -->
	
	<!-- Application Bootstrap -->
	<script>
	  System.config({
	    baseURL: '/app'
	  });
	  
	  System.import('contact-manager');
	</script>
</body>
```

**Magic!**

Now when we load the page up, we should **"Welcome to Contact Manager"** in big bold text on the screen.

##Conclusion and Next Steps

Alright, so that isn't exactly impressive, but **stay tuned for the next installment where we dig into the new data binding syntax** and begin to see where Angular 2 really shines.

The full source code can be found here as well as the contents of this article in markdown format:

https://github.com/Wintellect/angular2-contact-manager

> Written with [StackEdit](https://stackedit.io/).
