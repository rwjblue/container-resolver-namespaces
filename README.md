## What?

This is a testing ground for adding namespace resolution to the 
`ember-jj-abrams-resolver` (found [here](https://github.com/stefanpenner/ember-jj-abrams-resolver)).

## Why?

Namespaces will allow us to scope modules/assets to make redistribution and reuse of Ember libraries
much easier.

## Explain!

Assume the following module structure (roughly equivalent to folder structure):

```
app/posts/route.js
app/posts/index/route.js
app/posts/index/template.hbs
app/posts/index/item-controller.js
```

Suppose that you wanted to use a third party library (perhaps distributed via Bower) named google-maps.

Lets assume that you downloaded this into a folder named `vendor` in your current project, and that it contained
the following folder structure:

```
g-map/component.js
g-map/template.js
g-map/view.js
g-marker/component.js
g-marker/template.js
models/map_options.js
models/map_type.js
```

Now, if you want to use this map component from within your app code (say from within `app/posts/index/item-controller/template.hbs`)
you could use the following:

```handlebars
{{g-map@google-maps lat=29.169444 long=-82.123508}}
```

This would instruct the resolver/container to find the component in the `google-maps` namespace named `g-map`.

Now the interesting thing that needs to be dealt with is that from within the external namespace the items in that namespace
should not need to be prefixed. This means that if `g-map` wanted to render out a number of `g-marker` components it would
use standard syntax (without the namespace specified):

```handlebars
{{g-marker some-options=here}}
```

### A Few Conventions

* The namepaced code should be able to specify its own custom resolver (so that its internal structure is not dictated by each applications structure),
  and the location for that will be `/resolver.js` (under the root of the namespace).
* The namespaced code should be able to supply initializers (via `/initializer.js`) that is ran on application boot.

## How?

For this to work properly the container will need to maintain a list of namespaces that it is aware of, and a resolver that goes along with it.
When a lookup happens in application code, the container will first determine the namespace it goes with, then lookup or instantiate the resolver
for that namespace (looking in `/resolver.js` first then falling back to the default resolver).

## TODO

* Make container aware of namespaces?

  This may not be what we want actually. I am thinking that the container will likely need to simply manage multiple resolver instances hopefully
  in the container itself as `resolver:some-namespace`.

* Add a few helper methods to the resolver to allow looking up namespaces.
* Add a few test helpers that can be used for creating the container 
* Copy `container` packages from `emberjs/ember.js` repo, and `ember-resolver` from `stefanpenner/ember-jj-abrams-resolver` repo.
* Merge the test suites and get tests running.
