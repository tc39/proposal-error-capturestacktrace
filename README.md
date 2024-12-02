# Error.captureStackTrace 

**Stage**: [Stage 0](https://tc39.es/process-document/)

**Champion**: Matthew Gaudet (Mozilla) 


V8 has has a non-standard [Stack Trace API](https://v8.dev/docs/stack-trace-api) for a while. 

In August 2023, [JSC also shipped this method](https://github.com/WebKit/WebKit/commit/997e074bb35ed07b69c9b821141c91dd548e0d02) 

Has now turned into a web-compatability problem, and as a result it is time it 
is standardized. 

## `Error.captureStackTrace`

To quote the [V8 documentation](https://v8.dev/docs/stack-trace-api):

> Stack trace collection for custom exceptions
> 
> The stack trace mechanism used for built-in errors is implemented using a general stack trace collection API that is also available to user scripts. The function
> 
>     Error.captureStackTrace(error, constructorOpt)
> 
> adds a stack property to the given error object that yields the stack trace at the time captureStackTrace was called. Stack traces collected through `Error.captureStackTrace` are immediately collected, formatted, and attached to the given error object.
> 
> The optional `constructorOpt` parameter allows you to pass in a function value. When collecting the stack trace all frames above the topmost call to this function, including that call, are left out of the stack trace. This can be useful to hide implementation details that won’t be useful to the user. The usual way of defining a custom error that captures a stack trace would be:
> 
>     function MyError() {
>       Error.captureStackTrace(this, MyError);
>       // Any other initialization goes here.
>     }
> 
> Passing in `MyError` as a second argument means that the constructor call to `MyError` won’t show up in the stack trace.

## Implementation Divergence 

Unfortunately, the JSC implementation diverges from the V8 one: 

- JSC attaches a string valued prop to the object provided, where V8 instead installs their stack-getter function.

I would actually like to specify this like the JSC one. It's simpler. 

