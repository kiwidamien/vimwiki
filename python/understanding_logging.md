# Understanding Logging in Python

## The problem in a nutshell

The following code does not behave the way we expect:
```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(loggging.info)

logger.info("Hello")
# Nothing is logged or printed
```

There are a few brittle solutions to the problem, perhaps the most common one being to make a call to `basicConfig` like so:
```python
import logging

logging.basicConfig()
logger=logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.info("Hello")
# "Hello" is printed
```

There are a couple of problems with this approach:
1. The call to `logging.basicConfig()` affects the root logger, so all logging is affected. This causes noisey libraries to propagate their error messages.
2. The call to `logging.basicConfig()` is only processed once, so you have to make sure it isn't called by any of your imports. This also means that your import is going to fight with everyone elses. It isn't a problem if you are all trying to set the level to DEBUG in the `basicConfig`

### A subtle one

```
import logging
logger =  logging.getLogger('example')
logger.setLevel(logging.INFO)
logger.info('one')   # nothing printed
logging.info('two')  # nothing printed -- call to root logger, that defaults to level 'WARN'
logger.info('three') # prints "three"
```

So why is "three" printed, but not "one"? The reason is when we call the root logger with `logging.info(....)`, there is no handler for it. Even though the root logger doesn't handle info messages, it will call the `basicConfig()` to get a handler when it is first called. Then the last message to the `logger` object has a handler of last resort to handle it's message.

Basically, calling the root logger has many hidden side effects, which makes it hard to reason about. Because we are grabbing registered objects, and the root logger is a singleton, you have to be aware of these side effects for everything you import into your code! Ugh!

This example is discussed in this SO answer:
https://stackoverflow.com/questions/57115395/why-does-logger-info-only-appear-after-calling-logging-info/

### The problem

The problems are:
- The handler for the root logger is being used as the [method of last resort](https://stackoverflow.com/questions/43109355/logging-setlevel-is-being-ignored) when there is no other handler configered
- That calling the root logger will call `basicConfig` for you, even though you only asked it to log.
- Logger objects are registered by name, but the root logger is a singleton. If you are not explicitly making your own handler and relying on `basicConfig` then you are at the mercy of whomever called it first.
- The logger that you create will also propagate the message to the root handler, so if you simply create your own handler, you can get double logging.

### A solution

* Create your own handler
* Make sure your logging level is as low as you want to see logging messages (ever) -- changing the level of a handler below the level of the logger does not do anything because the logger will never pass along the messages to handle.
* Do not propagate your messages to the root logger

Code to do this:
```
import logging

....

logger = logging.getLogger(__name__)
logger.setLevel(level.INFO)  # or DEBUG or whatever

handler = logging.StreamHandler()
handler.setLevel(logging.INFO)
formatter = logging.Formatter("%(asctime)s - %(name)s - %(levelname)s - %(message)s")
handler.setFOrmatter(formatter)
logger.addHandler(handler)
logger.propagate=False
```

