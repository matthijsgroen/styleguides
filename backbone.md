Backbone
========

Each method body should start on a separate line.

Bad:

```coffeescript

hello: -> 'stuff'

```

Good:

```coffeescript

hello: ->
  'stuff'

```

Methods in a backbone class have specific ordering. Backbone (framework)
related methods should be at the top, implementation specific methods at
the bottom of the file. The ranking should be as follows:

- Property settings
- defaults
- events
- Initialize
- Backbone related methods (like render)
- own methods

We don't specifically mark private methods. so we don't prefix them with
underscores.

Bad:

```coffeescript

class MyView extends Backbone.Model
  renderItem: ->
  tagName: 'ul'
  defaults:
  updateBackground: ->
  events:
  render: ->
  initialize: ->

```

Good:

```coffeescript

class MyView extends Backbone.Model
  tagName: 'ul'
  defaults:
  events:
  initialize: ->
  render: ->
  renderItem: ->
  updateBackground: ->

```

Handling events
---------------

Normally registering to events happens in the initialize method. Instead
of adding the implementation of the handling there, we extract them in a
sepearate method. This has the following benefits:

- handling of an event gets a name, and therefor will be revealing
  intent. What is your goal of handling this event?
- Extracting code in small bits. The initialize method will become huge
  and unreadable if you don't extract this.

Bad:

```coffeescript

initialize: ->
  @model.on 'change', (model, change) =>
    @$('.notify').html @template(change)
    @$('.notify').addClass 'highlight'
  @on 'event', (event) =>
    # eventHandling

```

Good:

```coffeescript

initialize: ->
  @listenTo @model, 'change;, @notifyUserOfChange
  @on 'event', @handleEvent

notifyUserOfChange: (model, change) ->
  @$('.notify').html @template(change)
  @$('.notify').addClass 'highlight'

```

Disposing views
---------------

To have a uniform naming in standard actions, the disposing of a view will be called `dispose`

This method has the following responsibilities:

+ Disposing views of subviews where we still have the references of
+ Unbinding events on its models and collections where it was attached
+ Unbinding events on its DOM elements
+ Removing its contents from the DOM

The example code looks like this:

```coffeescript

dispose: ->
  @subView.dispose()
  @unbind() # unbind all javascript events on our view
  @remove() # remove our elements from the DOM

```

Update: Since Backbone 1.0.0 has the `listenTo` construct, the
`remove()` method will call `stopListening()` which will unbind all
dependencies to other objects. So these kinds of statements should no
longer be necessary when `listenTo` is properly used:

```coffeescript

@model.off null, null, this
@model.collection?.off null, null, this

```

More information can be found here:

+ [ThoughBot's Backbone-support](https://github.com/thoughtbot/backbone-support/blob/master/lib/assets/javascripts/backbone-support/composite_view.js)


Rendering Views and Templates
-----------------------------

The View object should act as a presenter when it comes to rendering
templates.

We take the following approach for this:

```coffeescript

render: ->
  @$el.html @template this
  this

```

This will expose the methods of our view to the template
in the template we can use them directly:

```haml

%menu.toolbar
  %li.user-info= @userDisplayName()
  %li.basket= @basketTotal()

```

The view will act as a presenter and take care of the formatting.
A benefit of this approach is that this can also be unit tested.

So the whole view could look like this:

```coffeescript
#= require backbone/templates/template_name

class ToolbarView extends Backbone.View
  template: JST['template_name']

  render: ->
    @$el.html @template this
    this

  userDisplayName: ->
    @user.get('displayName') || 'Not logged in'

  basketTotal: ->
    App.Helpers.formatCurrency(@basket.totalAmount())

```

