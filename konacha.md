Javascript unit test file structure
===================================

the unit tests are placed in `spec/javascripts` and following the folder structure of `app/assets/javascripts/backbone`. Each test file is suffixed with _spec.js.coffee. Within each spec file, the spec helper is included:

    #= require spec_helper

If this file is not included, the spec's won't run from the command-line using `bundle exec rake konacha:run`

Setup and Teardown
------------------

We use instance variables as mechanism to share the setup data with our test.
By default, CoffeeScript scopes variables within their running scope, so something must be done to get items of the beforeEach accessible within the test blocks.

Old style:

```coffeescript

describe 'feature', ->

  sharedVar = null # make the var accessible within the describe scope to share in beforeEach and tests

  beforeEach ->
    sharedVar = 'value'

  it 'does awesomeness', ->
    sharedVar.should.equal 'value'

```

Our style:

```coffeescript

describe 'feature', ->

  beforeEach ->
    @sharedVar = 'value'

  afterEach ->
    @sharedVar = null

  it 'does awesomeness', ->
    @sharedVar.should.equal 'value'

```

Pro's for our style:

  + Clear that data came from setup
  + no 'floating' var declarations

Con's for our style:

  - We need an afterEach to clean up, or values can 'leak'

Testing promises
----------------

We use [mocha-as-promised][] as extension on mocha. When the test returns a
promise (e.g. last statement returns a promise) the test will pass if
the promise fulfills, and will fail if the promise rejects

```coffeescript

it 'adds fetched product to the collection', ->
  delay().then => @server.respond()
  expect(=> @collection.length).to.change.by(1).when =>
    @collection.fetchProduct @productId

```

Testing method invocations
--------------------------

We use [sinon-chai][] to spy and stub on method calls.
We used `sinon.mock` in the past, but the tests where less readable,
needed a single `.verify()` and not as flexible as stubbing single
methods.

```coffeescript

beforeEach ->
  @model = new Backbone.Model
  sinon.stub(@model, 'methodName').returns(valueForHappyFlow)

afterEach ->
  @model.methodName.restore()
  @mode = null

it 'calls methodName', ->
  @model.doSomething()
  @model.methodName.should.have.been.calledWith(testarg)

```

Pro's for our style:

  + stub values can be changed later with
    `@model.methodName.returns('othervalue')`
  + include one happy flow in before and minimal overrides for bad
    flows.
  + you can do multiple specific assertions in different points in time


Testing class instantiation
---------------------------

We use `.calledWithNew` to test class instantiation. We will stub the
constructor and return a previously instantiated version where we can
apply further stubs and spies.

```coffeescript

beforeEach ->
  @instance = new Backbone.View
  sinon.stub(Backbone, 'View').returns @instance

  # you can now stub methods on the instance
  sinon.stub @instance, 'render'

afterEach ->
  @instance.render.restore()
  @instance = null
  Backbone.View.restore()

it 'instantiates a new view', ->
  Backbone.View.should.have.been.calledWithNew

  # You need to test constructor arguments seperately
  Backbone.View.should.have.been.calledWith
    model: @model

it 'renders the new view', ->
  @instance.render.should.have.been.called

```

Using shared behavior
---------------------

Sometimes you expect the same behaviour for different parts of your
application. To avoid duplication of tests, you can run the same tests
on the different parts of the system.

See [Shared behaviour in mocha][shared-behaviours-mocha] for the general
approach.

### Writing the shared behaviour

place a file in `spec/javascripts/shared` use a filename like:
"behave_like_xxxx_spec" to indicate a general behaviour.

In this file you have to define a method that will execute the shared
tests:

```coffeescript

# use the @ sign to bind this shared example suite to the outer context
# else this variable is not available in the test that wants to execute it.

@shouldBehaveLikeSomething = (contextCallback) ->

  describe 'behaves like something', ->

    it 'is below 10', ->
      # use .call(this) to run the supplied callback in the context of our current test
      contextCallback.call(this).should.be.below 10

    it 'is not 4', ->
      contextCallback.call(this).should.not.equal 4

```

### Applying a shared behaviour

require the shared behaviour in your spec:

    #= require shared/behave_like_something_spec

to apply this test to your current context, you can now use:

```coffeescript

describe 'number 5', ->

  beforeEach ->
    @number = 5

  shouldBehaveLikeSomething -> @number

describe 'number 7', ->

  shouldBehaveLikeSomething -> 7

```

The test suite should run 4 examples in this case.

[mocha-as-promised]: https://github.com/domenic/mocha-as-promised
[sinon-chai]: https://github.com/domenic/sinon-chai
[shared-behaviours-mocha]: https://github.com/visionmedia/mocha/wiki/Shared-Behaviours


