---
title: "IoC in JavaScript with InversifyJS"
description: "A description of how I implemented IoC in my TypeScript project using InversifyJS."
tags:
  - js
  - javascript
  - dependency injection
  - typescript
  - ts
  - inversify
  - inversifyjs
  - ioc
  - inversion of control
---

In C#, interfaces are used to set up a definition that classes can implement. Any class that implements an interface must adhere to the definition declared by the interface.

Think of them as a sort of menu you'd find at a typical pizzeria. The menu says they serve Margherita pizza.

Then you have the classes that implement the interfaces - i.e. the cooks that prepare the pizza mentioned in the menu.

The pizzeria may have multiple cooks, and the cooks may differ in the way they prepare the dish - i.e. one cook may use more cheese, and another may use less salt in the dough. Regardless, the resulting product will still be served as Margherita pizza.

Similarly, the consumers of your modules should only be made aware of the "menu", without having to worry about how the pizza is prepared. This way, you can swap out the "cook" whenever you want, without the consumers being any the wiser.

This is something I've been taking for granted in .NET development, and while there have been some efforts in bringing this concept to JavaScript, the implementation has not been that great - until now.

<!--more-->

## Introducing InversifyJS

> A powerful and lightweight inversion of control container for JavaScript & Node.js apps powered by TypeScript. - InversifyJS

[InversifyJS][1] is a generic lightweight container that brings inversion of control (IoC) to JavaScript through TypeScript. It comes with a super easy-to-use API that encourages developers to write code using the best Object-Oriented Programming (OOP) and IoC practices.

I've been using it for the past few months now, and I have to say - **it's freakin' awesome!**

So I decided to put together a small example of how you would implement IoC in a TypeScript app using InversifyJS.

On to some code!

### Setting up the interface

```typescript
// ICart.ts
export const ICartId = Symbol.for('ICart');

export interface ICart {
  items: CartItem[];
  addItem(item: CartItem): void;
  removeItem(item: CartItem): void;
}
```

As you may have already noticed, I have a constant declared in my interface file. JavaScript does not understand interfaces, and when TypeScript is transpiled to JavaScript, all interface references are removed. This is why we need to register the interface using something that will stick around after the transpilation, like `Symbol`.

### Implementing the interface

Once we have the interface set up, we need to create the class that implements it.

```typescript
// Cart.ts
import { injectable } from 'inversify';
import { ICart } from '@/models/ICart.ts';

@injectable()
export default class Cart implements ICart {
  items = [];

  addItem(item: CartItem) {
    this.items.push(item);
  }

  removeItem(item: CartItem) {
    const index = this.items.findIndex(x => x.id === item.id);
    if(index === -1) {
      throw new Error('Item not in array!');
    }

    this.items.splice(index, 1);
  }
}
```

### Setting up the app

The main app doesn't need to be injectable, but this is just a demonstration of how it can be done. I also wanted to show that I didn't need to manually set the injected dependencies, and let the container take care of it for me.

```typescript
// App.ts
import { inject, injectable } from 'inversify';
import { ICart, ICartId } from '@/models/ICart.ts';

@injectable()
export default class App {
  @inject(ICartId)
  private _cart: ICart;

  init() {
    // do stuff
  }
}
```

### Setting up the container

This is where you couple the interface with the class that implements it. This should be the only place where any coupling occur.

```typescript
// inversify.config.ts
import { Container } from 'inversify';
import { ICart, ICartId } from '@/models/ICart.ts';
import App from '@/App';

const container = new Container();

container.bind<ICart>(ICartId).to(Cart).inSingletonScope();
container.bind<App>(App).toSelf();

export { container };
```

### Hooking everything up

According to the InversifyJS docs:

*You can use the method `get<T>` from the `Container` class to resolve a dependency. Remember that you should do this only in your composition root to avoid the service locator anti-pattern.*

What this means is that you should set up an entry point where you fetch a binding from the container once to kick off the dependency resolution process for the rest of your application. In my case, this is the app `index.ts`.

```typescript
// index.ts
import 'reflect-metadata';
import { container } from '@/inversify.config';
import App from '@/App';

const app = container.get<App>(App);
app.init();
```

## Final words

C# and JavaScript are two fundamentally different languages, but as a fullstack developer who work with both, it makes me very happy whenever I come across a way to write code that bridges the gap between the two.

With InversifyJS, I can now write front-end code in a manner that is familiar to my back-end colleagues, and by doing so I can save time explaining the differences between the two.

Do you use IoC in your apps? Have you used InversifyJS? Is this the way to go? Let me know what you think in the comments below.

[1]: http://inversify.io