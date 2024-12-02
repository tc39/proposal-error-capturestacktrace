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

I would actually like to specify this like the JSC one. 

<!-- Comment out rest of this while I work on this
## Before creating a proposal

Please ensure the following:
  1. You have read the [process document](https://tc39.github.io/process-document/)
  1. You have reviewed the [existing proposals](https://github.com/tc39/proposals/)
  1. You are aware that your proposal requires being a member of TC39, or locating a TC39 delegate to “champion” your proposal

## Create your proposal repo

Follow these steps:
  1. Click the green [“use this template”](https://github.com/tc39/template-for-proposals/generate) button in the repo header. (Note: Do not fork this repo in GitHub's web interface, as that will later prevent transfer into the TC39 organization)
  1. Update ecmarkup and the biblio to the latest version: `npm install --save-dev ecmarkup@latest && npm install --save-dev --save-exact @tc39/ecma262-biblio@latest`.
  1. Go to your repo settings page:
      1. Under “General”, under “Features”, ensure “Issues” is checked, and disable “Wiki”, and “Projects” (unless you intend to use Projects)
      1. Under “Pull Requests”, check “Always suggest updating pull request branches” and “automatically delete head branches”
      1. Under the “Pages” section on the left sidebar, and set the source to “deploy from a branch”, select “gh-pages” in the branch dropdown, and then ensure that “Enforce HTTPS” is checked.
      1. Under the “Actions” section on the left sidebar, under “General”, select “Read and write permissions” under “Workflow permissions” and click “Save”
  1. [“How to write a good explainer”][explainer] explains how to make a good first impression.

      > Each TC39 proposal should have a `README.md` file which explains the purpose
      > of the proposal and its shape at a high level.
      >
      > ...
      >
      > The rest of this page can be used as a template ...

      Your explainer can point readers to the `index.html` generated from `spec.emu`
      via markdown like

      ```markdown
      You can browse the [ecmarkup output](https://ACCOUNT.github.io/PROJECT/)
      or browse the [source](https://github.com/ACCOUNT/PROJECT/blob/HEAD/spec.emu).
      ```

      where *ACCOUNT* and *PROJECT* are the first two path elements in your project's Github URL.
      For example, for github.com/**tc39**/**template-for-proposals**, *ACCOUNT* is “tc39”
      and *PROJECT* is “template-for-proposals”.


## Maintain your proposal repo

  1. Make your changes to `spec.emu` (ecmarkup uses HTML syntax, but is not HTML, so I strongly suggest not naming it “.html”)
  1. Any commit that makes meaningful changes to the spec, should run `npm run build` to verify that the build will succeed and the output looks as expected.
  1. Whenever you update `ecmarkup`, run `npm run build` to verify that the build will succeed and the output looks as expected.

  [explainer]: https://github.com/tc39/how-we-work/blob/HEAD/explainer.md
