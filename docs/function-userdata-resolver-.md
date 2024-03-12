# Userdata Operators or Library Addition

#### Semantics Questions
- [x] Is behavior easy to understand and non-surprising?
- [ ] Is the new functionality used/useful often enough in existing code?
- [ ] Is the behavior general and unambiguous, as opposed to solving a problem / providing an interface that's too specific?

## Summary

One paragraph explanation of the feature.

## Motivation

> Why are we doing this? What use cases does it support? What is the expected outcome?

Currently Userdata looks like this, when used as a key and when converted into a string.
```
{
  [Userdata(16A564EB788)] = "userdata: 0x05d3997bf7c3621a"
}
```

This could mean that they're allocated somewhere. Userdata that have modified their **tostring** output, don't show their address. But when set as a key, it shows this strange Hex. **Point is**, the Userdata has methods to re-present itself.


### Referencing
Mostly when sending through Userdata, since they're not tables, unlike tables they can't be resolved back if the _Receiver_ doesn't have the same Userdata as well, depending on how it looks for it. This is an issue when someone wants to keep a **reference** to a userdata, **in order to easily resolve it back**

Point is to maintain an easy association between these objects across different environments.

#### Why Referencing?
This is useful to index userdata as non-destructive keys that can be resolved back if someone owns it. The purpose of this is purely Networking things, e.g. display a list of _Pseudo Objects_ which hold a reference to the real object.


## Design

This is the bulk of the proposal. Explain the design in enough detail for somebody familiar with the language to understand, and include examples of how the feature is used.

## Drawbacks

It is **questionable** whether this is going to be useful for other things other than ``userdata`` and Roblox API's specific ``Instance`` Class.

Secondly, whatever the identifier is, we'd have to think whether anyone can access stuff that they shouldn't. But we'd have to think a bit further. _Let's assume_ that someone has gained access to the Lua VM, they'd be able to access anything that is locally available to them already, so it wouldn't matter.

What does matter is whether this allows **Sandboxing**, but in a sense where, one can access ``userdata`` that wasn't assigned in the Lua State. But this wouldn't logically make sense, if these functions speficially **only check** the Lua State.

&nbsp;
The next question is, whether wrong use can crash the Lua VM.

&nbsp;
Another is whether this can be bruteforced. If someone would create a customized reference ID, it would mean extra storage, which can be bad. But using what already exists from the Lua State, sounds better. Yet, anyone that can manipulate the VM, would be able to change addresses and access things regardless, whether it's being prevented, but only on a local level.

Point here is, if this is limited to Lua State, you wouldn't be able to access things outside of the Lua State, making this less of an issue.

## Alternatives

There are no good alternatives that solves this general issue in an easy context. Other than implementing ``fromaddress``, except that here, the drawback would be unauthorized access, simply by brute forcing.

This is very specific to the Roblox API and the ``Instance`` Class. In Luau it resolves into ``userdata``, giving it similarities.

You can store and create own IDs, but that would take up extra storage and sanitization checks and since ``userdata`` can appear anywhere, it's hard to predict how.
