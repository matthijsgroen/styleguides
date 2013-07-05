Data Context Interaction
========================

For details about the concept of this design pattern, see:

- [Wikipedia entry][wiki]
- [Orinating Presentation from Trygve M. H. Reenskaug][dci-origin]
- [Clean Ruby][cleanruby]

DCI Execution in Ruby
---------------------

We use the basics for implementation according to: [The Right Way to
Code DCI in Ruby][dci-ruby]

We have a few changes/additions to the concept of the blog post:

=== Contexts as gerunds

Whe do not suffix the contexts with the word Context.
We use a 'gerund' in wording and place all contexts in a contexts/
folder since we use a 'gerund' in naming (suggested by the [Clean
Ruby][cleanruby] book we prevent name clashes.

=== Contexts return 'self' after execution

We return the stage where the events have happened after the execution
of the 'play'. This solves the problem of specifying return values.
Instead, the caller can fetch the resulting objects it is intrested in
from the stage.

Execution
---------

The above format and additions to it will result in the following code
structures and responsibilities:

=== The Context

Contexts are placed in `app/contexts`. It is the responsibility of the
Context to have data-objects interact with one another.

The context will take care of:

- Placing data objects on the stage
- Assigning roles to the data objects
- Executing the play
- Expose resulting data objects

Unit tests for contexts should guarantee that the context fulfills its
responsibilities.

```ruby

class MoneyDonating # a gerund

  # The objects available on stage at the end of the play.
  attr_reader :source_account, :target_account, :receipt, :amount

  def self.call(source_account, target_account, amount)
    new(source_account, target_account, amount).call
  end

  # intialize will place the objects on stage and take
  # care of identifying 'actors' and assigning them the proper roles.
  def initialize(source_account, target_account, amount)
    # place objects on stage
    @source_account = source_account
    @target_account = target_account
    @amount = amount

    # casting: give actors the proper roles
    source_account.extend MoneyDonator
  end

  # call will execute the interactions between objects
  def call
    # not all objects need to stay on the stage for
    # afterwards inspection
    # they could have a limited timespan and be created and die on stage
    donation_transaction = source_account.start_transaction

    donation_transaction.donate_amount_to(target_account, amount)
    donation_transaction.end_transaction

    # when new objects are born that are alive and interesting
    # at the end of the play, they should be assigned to the play:
    # by using only instance variables on assignment to stage (and the
    # attr_readers in other usage it becomes really visible when reading
    # the code when new objects are introduced
    @receipt = donation_transaction.receipt

    self # always return the stage, so that objects can be directly
    # accessed.
  end

end

# usage:

MoneyDonating.call(account1, account2, 200).receipt

```

=== Roles

Roles are defined in `app/roles`. Roles are ruby Modules mixed in object
instances in the casting fase of a Context. A data object can play
multiple roles.

```ruby

module MoneyDonator # this actor will play the role of ...

  def donate_money
  end

end

```

For unit testing roles, it is best to setup an actor and mix in the role

RSpec example:

```ruby

describe MoneyDonator

  let(:actor) { double('actor object') }
  before do
    actor.extend described_class
  end

  describe '#donate_money' do
    subject { actor.donate_money }
    # etc
  end

```

Considerations
--------------

We are pretty new to the concept of DCI, and haven't figured out
everything yet. We learn every day, try to find patterns in our way of
working and update this styleguide accordingly.

Things we haven't covered yet in this styleguide:

- Exception handling

[wiki]: http://en.wikipedia.org/wiki/Data,_Context,_and_Interaction
[cleanruby]: http://www.clean-ruby.com/
[dci-origin]: http://oredev.org/videos/dci--re-thinking-the-foundations-of-oo
[dci-ruby]: http://mikepackdev.com/blog_posts/24-the-right-way-to-code-dci-in-ruby



