# Advanced Python - Training Material

Material I put together while delivering a Python training program. The goal
was to teach intermediate/advanced Python concepts using **real production
code** instead of toy examples, so trainees could see how the pattern is
actually used in the wild.

## `adv_examples.md`

A reference doc walking through 13 core topics - decorators, generators,
type hinting, class internals (class/static methods, inheritance, duck
typing, properties, private attributes), lambdas, recursion, context
managers, `map`/`filter`/`zip`, and regex.

Each topic is illustrated with real snippets pulled from open source
projects, with a link back to the exact lines and a plain-English
explanation of what the code is doing and why it's written that way:

- [Flask](https://github.com/pallets/flask)
- [pydoit](https://github.com/pydoit/doit)
- [chat-langchain](https://github.com/langchain-ai/chat-langchain)
- [Scrapy](https://github.com/scrapy/scrapy)

## `arpans_context_managers.ipynb`

A hands-on notebook digging deeper into context managers specifically -
`__enter__`/`__exit__`, the `contextlib` helpers, and worked examples.

## Who this is for

Written for trainees who already know Python basics and want to see how
these patterns show up in real codebases, not just isolated syntax demos.
