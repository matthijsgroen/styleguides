Cucumber Scenario's
===================

* Given steps must be written in the past tense
* Other steps must be written in the actual tense
* Scenario titles should be written in the same format as commit
messages
* Feature descriptions should read like a userstory
* Feature title should be a short description in the same format as
  commit

```gherkin

  Feature: Show new build status when build completes
    As a user
    I want to see the new build status after building is complete
    So that I do not have to take manual action to see the latest status

  Scenario: Update state when build is successful
    Given a build has been configured in the system
    And the build has been started
    When the build completes successfully
    Then the state is 'success' without refreshing the page

```

> You can read 'This feature will show new build status when build
> completes'

> You can also read 'This scenario will update state when build is
> successful'

Cucumber Steps
==============

Steps in the step file should group all steps on type.
First all 'Given' then the 'When' and finally the 'Then' steps

Each step should execute one or more helper methods, and not contain
large implementation chunks. Therefore steps should never need to call
other steps.

Cucumber helper proxies
=======================

It is really useful to scope certain parts of UI into proxy methods that
hold various command and query methods.

This can lead to small custom DSLs that make the tests readable,
understandable and grouped into smart contexts

```ruby

  build_list.should have(4).build_configurations
  build_list.should have_a_build_running
  build_list.row_of_build(title: 'My Project').click

```

This can be achieved using a file in
`features/support/<object>_helpers.rb`

```ruby
module BuildListHelpers

  def build_list
    BuildListProxy.new self
  end

  class BuildListProxy

    def initialize session
      @session = session
    end

    def build_configurations
      ...
    end

    def has_a_build_running?
      ...
    end

    def row_of_build(search_options)
      ...
    end

    private
    attr_reader :session
    delegate :find, :has_selector?, to: :session
  end

end
World(BuildListHelpers)
```

See:
[Cucumber: building a better World (object)](http://drnicwilliams.com/2009/04/15/cucumber-building-a-better-world-object/)

