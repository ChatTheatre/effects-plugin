%META:TOPICINFO{author=\"andreww\" date=\"1088940420\" format=\"1.0\"
version=\"1.1\"}% %META:TOPICPARENT{name=\"EffectsFramework\"}%

# Using the Effects System

The effects system is comprised of a base class (Effects:UrEffect), a
base class for each \'class\' of effect (such as
Effects:Poison:UrPoison) and associated children. The base class
contains all necessary scripts and default values for a set of
properties (via *export*). An individual effect is implemented by
inheriting from the base class and overriding the appropriate values.

## What is an \'Effect\', in game?

In game, an effect is anything that may be applied to a character that
causes ongoing effects for a period of time. Spells, poisons, smells are
all effects. If a single message or change is to be applied to the
target, then the effects system is probably not useful. However, if
there is ongoing activity then the effects system can handle this with a
minimum of effort by the user.

## Applying an effect

Applying an effect goes through a number of stages\...

### Initialisation

When an effect is first applied (via the *apply* call) the system checks
for resistance to the effect, and then configures several status
variables, including the dosage and time applied.

### Initial delay

A delay period (optional) is applied before the effect begins to act.
Then any intial messages are printed before the main loop begins.

### Main loop

The main loop is followed. This essentially is:

    while dose > 0 and/or time remains
    - generate messages
    - apply delay
    - adjust dose

### Clean up

Removes unneeded properties and displays final messages.

## How do I create an effect?

To create an effect, first choose a class of effect (for example,
Poison), and thenspawn a child of the appropriate parent (for example,
`Effects:Poison:UrPoison`). Once you have your new object, set the
relevant properties. The only required property is `effect:name`, which
is used to check resistance, among other things. In general, it should
be at least a tuple, such as (for a poison)
` 'euphoric:red-berry-juice`\'. This says that the poison name is
red-berry-juice, and it is of the \'euphoric\' class of poisons. I have
not yet written any form of [poison taxonomy](EffectsPoisonTaxonomy).

Beyond this, you can changes the (boring) default timing and messages by
overriding other properties.

### How do I apply an effect to someone?

All effects are triggered by a call of the form
`Call (effect-object, "apply", $who: _victim_, $dose: 1 )`. \$who is the
target object, which must have volition for the poison to work. \$dose
is an integral number of doses, with a default of 1.

Once this call is made, the effect library does the rest, based on the
properties you set.

If you set the optional \$debug property non-nil, debugging messages
will be sent to \$actor (only do this for testing, obviously).

## What properties are available?

### Complete list of supported properties

This is the list of all properties, as taken from `Effects:UrEffect`.

All listed property names are prefixed by \"`effect:`\"

#### General Properties

    name: string - name of effect
      required
    strength: float - difficulty to resist
      default: 1.0
    strength:by-dose: boolean - if true, multiply strength by dose
      default: FALSE

#### Timing and Duration

    onset: int - amount of time before effect first occurs, in seconds
      default: 0
      0 => non-periodic (once only)
    onset:variance: float - modifier to onset, as fraction
      default: 0.1
      Period varies by +/- variance. 1.0 is 100% (too big!)
    period: int - amount of time between iterations, in seconds
      default: 0
      0 => non-periodic (once only)
    period:variance: float - modifier to period, as fraction
      default: 0.1
      Period varies by +/- variance. 1.0 is 100% (too big!)
    time: int - total number of seconds effect applies for
      default: 0
      0 => lasts until it degrades
      If an additional does is applied before time expires, the time is extended
    time:variance: float - modifier to time, as fraction
      default: 0.1
      Time varies by +/- variance. 1.0 is 100% (too big!)
    degrade:chance: int - chance of effect degrading, as %
      default: 100
      Every period, this chance is checked to see if the effect degrades (loses doses)
    degrade:value: int - maxiumum number of doses to be lost when effect degrades
      default: 1
      The effect degrades (loses doses) between 1 and this value.

#### Effects

    random: int - each period, add between 0 and this many doses to the effect before calculating effect
      default: 0
      Does not actually change the number of doses.

A message may be emitted every period, using the various `message`
properties to define the message.

    message-pre: string - before all other messages
      default: nil (or "")
    message-post: string - after all other messages
      default: nil (or "")
    message:<N>: string - if dose is >= N, and none higher matches
      default: nil (or "")
      effect:message:0 acts as a default
    message:<N>:<A> string - if dose is >= N, and none higher matches, randomly pick one
      default: nil (or "")
      A can be any string.
      If there is both effect:message:<N> and one or more of these,  the unqualified one is printed last.
      If this message exists but is empty, the unqualified message is also not printed.

You can also have separate start and stop messages.

    message-start: string - when effect is first experienced
      default: nil (or "")
    message-stop: string - when effect wears off
      default: nil (or "")

#### 3rd person messages

All the above messages are sent to the target of the effect. It is also
possible to send messages to 3rd parties; those in the same environment
as the effect.

In such a case, replace `message` with `message3`.

## How resistance works

## What is an Effect Class?

An effect class is merely a top level grouping of effects. It is
implemented via an object that defines `export:effect:class` (eg
\'poison\'). The main role of classes is administrative, although it is
possible for a class to redefine standard effect behaviour for its
children only.

## Wish list

### Substitutions

The ability to use things like \$W in a message and have it replaced by
Describe(\$who).

### Custom effects

Firing scripts as well as displaying messages.

\-- Main.SpZiph - 04 Jul 2004
