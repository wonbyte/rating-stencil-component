# Stencil JS

## About
It is a building tool that allows you to use a high-level abstraction to define your UI components and to generate pure JavaScript code implementing standard-based Web Components. The compiled code runs in all major browsers since Stencil uses a small polyfill only on browsers that lack some needed features.

## Demo Rating Component
UI component allowing the user to provide their feedback about a product, an article or whatever by assigning a number of stars.

> To set up the Stencil development environment run `npm init stencil component rating-stencil-component`. The component starter project provides a standard Node.js development environment.

## Creating a Basic Stencil Component
The `my-rating-component.tsx` file contains TypeScript and JSX code. Actually, these are the languages used by Stencil to define the component. A Stencil component is a TypeScript class, `MyRatingComponent` in this case, that implements a `render()` method. This class is decorated by the `@Component` decorator, previously imported from the `@stencil/core` module. This decorator allows us to define some meta-data about the component itself. In particular, we defined the tag name that will be associated with the component. This means that we will use the `<my-rating></my-rating>` element to put this component inside an HTML page. We also defined the CSS file containing styling settings for the component via the styleUrl property. The last property, `shadow`, isolates the internal component DOM and styles so that it is shielded by name conflicts and accidental collisions. This feature should be granted by the Web Component's Shadow DOM. All recent browsers support this feature. Anyway, if any older browser doesn't support it, a polyfill will be used.

The `render()` method describes the component's appearance by using JSX expressions. In our example, the component's markup consists of a sequence of six-span HTML elements: three of them contain the HTML entity for the full star (&#x2605;), and the other three contain the code for the empty star (&#x2606;).

You may notice that the `h()` function has been imported from the `@stencil/core` module. This function is needed to allow Stencil to turn the JSX into Virtual DOM elements. In fact, Stencil uses a tiny Virtual DOM layer similar to the one used by React.

> **Note: The Stencil development environment provides support for hot reloading, so if your environment is running after an npm start, any changes to the project's files will cause its automatic rebuilding and running. This means that you don't need to type again npm start.**

We imported the `@Prop()` decorator. This decorator allows us to map the properties of the component class to attributes in the markup side of the component. We added the `maxValue` property, which represents the maximum number of stars to show, and the v`alue` property, which indicates the current rating value and so the number of full stars to be shown. As you can see, each property has a default value. These properties decorated with `@Prop()` allows us to use the component's markup as follows: `<my-rating max-value="6" value="2"></my-rating>`

By using this markup, we are mapping the value of max-value attribute to the `maxValue` property and the value of value attribute to the `value` property. Notice how the kebab case naming style of the attribute names is mapped to the camel case naming style of the class properties.

Finally, the `createStarList()` method dynamically creates the sequence of the stars to display based on maxValue and value properties' value.

## The Reactive Nature of Properties on Stencil
The component's properties are not only a way to set customized initial values through HTML attributes. The mapping between the attributes and the properties is reactive. This means that any change to the attribute fires the render() method so that the component's UI is updated.

## Managing State of Stencil Components
We can assign internal state to our component. The state of a component is a set of data internally managed by the component itself. This data cannot be directly changed by the user, but the component can modify it according to its internal logic. Any change to the state causes the execution of the `render()` method. Stencil allows you to define the component state through the `@State()` decorator.

> **Note: Stencil watches state and props for changes in order to run the `render()` method. However, it actually compares references for changes, so a change in data inside an array or to an object's property doesn't cause a re-rendering of the component. You need to assign a new array or object to the state.**

## Managing Stencil Components' Lifecycle
Stencil provides us with a few hooks to handle the various events of the component lifecycle:

- `componentWillLoad`: the component is ready to be loaded into the DOM, but it is not rendered yet.
- `componentDidLoad`: the component has been loaded and rendered.
- `componentWillUpdate`: the component is about to be updated.
- `componentWillRender`: the component is about to be rendered.
- `componentDidRender`: the component has been rendered.
- `componentDidUpdate`: the component has been updated.
- `componentDidUnload`: the component has been removed from the DOM.

To use these hooks, you simply need to implement a method with the same name in your component.

## Managing Mutable Properties on Stencil
As said above, a component property decorated with `@Prop()` acquires a reactive nature. This means that if a change to the property occurs from the external of the component, this immediately fires the re-rendering of the component itself. However, a property change can only occur through an external action. Props are immutable from inside the component. This means that an internal attempt to change the value of a prop doesn't work.

## Emitting Events from Stencil Components
Stencil also allows you to emit events so that users of your component can be informed when something happens. The first thing you notice is the import of the EventEmitter class and of the @Event() decorator. Both are used to define the `ratingUpdated` property. This property is an instance of the `EventEmitter` class and provides a way to generate an event with the same name. In fact, you added a call to its method `emit()` inside the `setValue()` method. You also passed an object representing the current value to the `emit()` method.

## Preparing Stencil Components for Production
The `stencil.config.ts` file contains Stencil specific configuration options. In particular, it defines three output targets for your project:

- **dist**: this output target generates a library that can be distributed via npm. It also requires that the package.json file is correctly configured. The esmLoaderPath option defines the folder where the compiler will put the code to load the library in different development environments.
- **docs-readme**: this target generates readme files formatted in markdown from comments in your code. In the current project.
- **www**: this target is intended for web apps and websites. This is the default output target, that is, if you don't specify any target, the `www` target will be generated. Even if a project is meant to build a library, the `www` target is useful to test the components during the development.

The `package.json` file is already prepared to generate code to support the so-called differential bundling. This means that Stencil will create code for modern and legacy browsers i.e. IE11. In addition, it will generate code to be consumed in different ways, like npm or CDN. Finally, it will produce code to be used in different development environments. For example, it will generate types to allow developers to use the components in TypeScript projects.

Once we have prepared the build configuration, run the command `npm run build` to start the building task.

After a few seconds, we will find a `dist` and a `loader` folder in the root of the project with everything we need to use your brand new Web Component. The additional subfolders in the `dist` folder match the different production scenarios discussed above. In particular, the `rating-stencil-component` folder is all we need to use the component locally in our HTML page.

The best practices to distribute the component require publishing the package to the npm registry as the first step. Then, we can use the library in our project using one of the following approaches:

- **via script tag**: add a script tag similar to the following to the head section of your HTML page:
`<script src='https://unpkg.com/rating-stencil-component@1.0.0/dist/rating-stencil-component.js'></script>`

- **via Node modules**: run the following command in the root of your Node project: `npm install rating-stencil-component`

In both cases, you can simply use the <my-rating> tag wherever you can use HTML markup.