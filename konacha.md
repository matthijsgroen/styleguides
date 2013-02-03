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

Testing method invokations
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


[mocha-as-promised]: https://github.com/domenic/mocha-as-promised
[sinon-chai]: https://github.com/domenic/sinon-chai


