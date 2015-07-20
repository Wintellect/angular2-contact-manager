#Build an Angular 2 App - Data Binding Syntax

In our [previous article about building a simple application in Angular 2](www.wintellect.com/devcenter/jcarroll/build-an-angular-2-app-the-root-component), we laid the foundation for moving forward and covered all the very basics about bootstrapping your application. However, it's more or less a hello world application, and that, has [been done](https://github.com/pkozlowski-opensource/ng2-play) already a [number of ways](https://github.com/mgechev/angular2-seed).

The goal is to build a small real-ish application that you can use to gain ideas and inspiration from as you dive into Angular 2 yourself. However, we need to get some basics on the new data binding syntax out of the way.

##Data Binding

Now, I cannot possibly do a better job explaining this than [Victor Savkin already has in his article: Angular 2 Template Syntax](http://victorsavkin.com/post/119943127151/angular-2-template-syntax), so I am not going to try. I highly encourage you to go take a look at that article to get an in-depth explanation of the new data binding and template syntax within Angular 2.

Instead, what I want to focus on is the 90% case, and the implications that has in terms of how you build your applications.

##The Basics

Again, Victor's article goes into a great amount of detail on this, but here is a condensed version of the basic data bindings you will be using everyday in your applications.

```html
<!-- Interpolation -->
<div>Hello, {{name}}!</div>

<!-- Property Binding -->
<div [hidden]="hideMessage">I am a message</div>

<!-- Event Binding -->
<button (click)="doStuff()">Do Stuff</button>

<!-- Bubbling Event Binding -->
<div (^click)="doStuff()">
   <span>Clicks handled by parent</span>
</div>

<!-- Local Variable -->
<input type="text" #name />
<span>{{name.value}}</span>

<!-- Template Binding (ng-for, ng-if, etc...) -->
<ul>
   <li *ng-for="#c of contacts">{{c.name}}</li>
</ul>
```

##Simplified Property And Event Binding

Your first impression of the `[property]` and `(event)` binding syntax may feel a bit strange, and certainly takes some getting used to, but the explicitness make it really nice when working in your application. It's very easy to spot what is binding syntax and what is just a plain old attribute. This also means that machines (editors and IDE's) will have a much better time understanding and supporting the new binding syntax.

Another key benefit of this new format is a subtle but powerful one. The `[]` and `()` bindings are being tied directly into the component/DOM element properties and events. **This means any public DOM event or property can be bound to with no additional coding!**

That means that this little example will work without having to invoke a single directive:

```html
<!-- Show DOM property value -->
<h1>msg.hidden: {{msg.hidden}}</h1>

<!-- Handle DOM onClick event -->
<button (click)="msg.hidden = !msg.hidden">Toggle Message</button>

<!-- Save a reference to the H2 element as 'msg' -->
<h2 #msg>I am a name!</h2>
```

And just so you can see this for yourself in action, here is a working plunker:

<iframe style="border: 1px solid #999;width: 100%; height: 400px"
        src="http://embed.plnkr.co/ZTKnLy/preview" frameborder="0">
    Loading plunk...
</iframe>

##What Does This Mean?

So what does this new crazy binding system get me? **A whole lot of awesome is what!**

With Angular 1.x you were pretty much stuck with the built in directives for binding to properties and events, or you had to write your own. This wasn't particularly difficult to do, but it was still overhead, both in terms of directives you had to learn, or code you had to maintain.

**With Angular 2, your API docs for binding to DOM elements is [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element)**.

It means that if you want to bind the color of an `<h1>` to the value of textbox, then you can do it like this:

```html
<input type="text" #color (keyup) />
<h1 [style.color]="color.value">
  My color is magically set!
</h1>
```

It means that if you also want to use a slider to dynamically change the size of the same `<h1>` then all you have to do is this:

```html
<input type="range" min="10" max="300" value="32" #size (input) />
<h1 [style.font-size]="size.value + 'px'">
 My color is magically set!
</h1>
```

And here is a full example with several elements so you can see the code and play with it. **Remember, this is all out of the box behavior, and doesn't require any special directives, or coding to make it work.**

<iframe style="border: 1px solid #999;width: 100%; height: 500px"
        src="http://embed.plnkr.co/ZbCLxE/preview" frameborder="0">
    Loading plunk...
</iframe>

##Conclusion And Next Steps

As you can see, while **the new data binding syntax may seem a little strange at first, it is really a powerful new system with a lot of flexibility**. As we will continue to build out our application you will see just how nice it becomes with custom properties and events on components you build.

Tune in next time as we get back to building out our little contact manager application by introducing a simple list component.