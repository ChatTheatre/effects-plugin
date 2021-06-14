%META:TOPICINFO{author=\"andreww\" date=\"1088938989\" format=\"1.0\"
version=\"1.4\"}% %META:TOPICPARENT{name=\"EffectsFramework\"}%

# Using the Poison System (old version)

The poison system is comprised of a base class (Effects:Poison:UrPoison)
and associated children. The base class contains all necessary scripts
and default values for a set of properties (via *export*). An individual
poison is implemented by inheriting from the base class and overriding
the appropriate values.

Note that \'poison\' doesn\'t only apply to poisons, but to any effect
applied through a foreign chemical in the person.

## Applying an effect

Applying an effect goes through a number of stages\...

### Initialisation

When an effect is first applied (via the *apply* call) the system checks
for resistance to the poison, and then configures several status
variables, including the dosage and time applied.

### Initial delay

A delay period (optional) is applied before the poison takes effect.
Then any intial messages are printed before the main loop begins.

### Main loop

The main loop is followed. This essentially is:

    while dose > 0 and/or time remains
    - generate messages
    - apply delay
    - adjust dose

### Clean up

Removes unneeded properties and displays final messages.

## How do I create a poison?

To create a poison, spawn a child of `Effects:Poison:UrPoison` and then
set the relevant properties. The only required property is
`poison:name`, which is used to check resistance, among other things. In
general, it should be at least a tuple, such
as` 'euphoric:red-berry-juice`\'. This says that the poison name is
red-berry-juice, and it is of the \'euphoric\' class of poisons. I have
not yet written any form of [poison taxonomy](EffectsPoisonTaxonomy).

Beyond this, you can changes the (boring) default timing and messages by
overriding other properties.

### How do I make the poison take effect?

All poisons are triggered by a call of the form
`Call (poison-object, "apply", $who: _victim_, $dose: 1 )`. \$who is the
target object, which must have volition for the poison to work. \$dose
is an integral number of doses, with a default of 1.

Once this call is made, the poison library does the rest, based on the
properties you set.

## What properties are available?

### Complete list of supported properties

This is the list of all properties, as taken from
`Effects:Poison:UrPoison`.

All listed property names are prefixed by \"`poison:`\"

#### General Properties

    name: string - name of poison
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
    time: int - total number of seconds poison applies for
      default: 0
      0 => lasts until it degrades
      If an additional does is applied before time expires, the time is extended
    time:variance: float - modifier to time, as fraction
      default: 0.1
      Time varies by +/- variance. 1.0 is 100% (too big!)
    degrade:chance: int - chance of poison degrading, as %
      default: 100
      Every period, this chance is checked to see if the poison degrades (loses doses)
    degrade:value: int - maxiumum number of doses to be lost when potion degrades
      default: 1
      The potion degrades (loses doses) between 1 and this value.

#### Effects

    effect:random: int - each period, add between 0 and this many doses to the poison before calculating effect
      default: 0
      Does not actually change the number of doses.

A message may be emitted every period, using the various
`effect:message` properties to define the message.

    effect:message-pre: string - before all other messages
      default: nil (or "")
    effect:message-post: string - after all other messages
      default: nil (or "")
    effect:message:<N>: string - if dose is >= N, and none higher matches
      default: nil (or "")
      effect:message:0 acts as a default
    effect:message:<N>:<A> string - if dose is >= N, and none higher matches, randomly pick one
      default: nil (or "")
      A can be any string.
      If there is both effect:message:<N> and one or more of these,  the unqualified one is printed last.
      If this message exists but is empty, the unqualified message is also not printed.

You can also have separate start and stop messages.

    effect:message-start: string - when effect is first experienced
      default: nil (or "")
    effect:message-stop: string - when effect wears off
      default: nil (or "")

## How resistance works

## Wish list

### Antidotes

The ability to use one \'poison\' to counteract another.

### Custom effects

Firing scripts as well as displaying messages.

### Fatal and accumulating poisons

Poisons whose effects are worse if you\'ve had them before, even though
the initial effect has worn off. Poisons that eventually kill the
character.

\-- Main.SpZiph - 09 Mar 2004
