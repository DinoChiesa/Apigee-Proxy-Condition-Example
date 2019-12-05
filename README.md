# condition-1

This is a loopback proxy that shows how to use Regular expressions to match paths for inbound requests in
Apigee.

View the [quick screencst walkthrough of this example](https://youtu.be/eDKFme9PsaY) on Youtube: 


## Motivation

Many Apigee examples show the use of the [MatchesPath
operator](https://docs.apigee.com/api-platform/reference/conditions-reference#operators)
(shorthand: ~/) to conditionally handle inbound requests.

For example,
```
 <Condition>request.verb = "GET" AND proxy.pathsuffix MatchesPath "/items/*"</Condition>
```

The right-hand-side (RHS) of a MatchesPath operator is a string, a "Path Expression"
which behaves like a simple [path
glob](https://en.wikipedia.org/wiki/Glob_(programming)).
So a pattern of `"/items/*"` matches inbound paths like `/items/foo` and
`"/items/123"`. Somewhat non-intuitively, it also matches `/items/` with an
empty trailing final segment. That's often not what you want.

The regex operator (JavaRegex, shorthand: ~~) allows you to be more precise in what you want to match.

For example, in the following:
```
 <Condition>request.verb = "GET" AND proxy.pathsuffix ~~ "/items/.+"</Condition>
```

The condition matches if and only if there is at least one character following
the `/items/`.

To be more strict you may wish to restrict the set of characters to "anything
but slash". That looks like this:
```
 <Condition>request.verb = "GET" AND proxy.pathsuffix ~~ "/items/[^/]+"</Condition>
```

or you may want to match only paths that have a numeric final segment. That
looks like this:

```
 <Condition>request.verb = "GET" AND proxy.pathsuffix ~~ "/items/[0-9]+"</Condition>
```


## Use the Example

To use this example proxy:

1. deploy to an org + environment.
   To do so you can use a command-line tool like [importAndDeploy](https://github.com/DinoChiesa/apigee-edge-js/blob/master/examples/importAndDeploy.js) or just
   manually zip up the apiproxy directory and use the Apigee UI to import the zip.

2. invoke with various options:

   glob matching with two segments:
   ```
   ORG=myorg
   ENV=myenv
   curl -i https://$ORG-$ENV.apigee.net/condition-1/glob/foo/bar
   ```
   glob matching with empty final segment:

   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/glob/foo/
   ```

   regex matching with lax wildcard, two segments:
   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/regex1/foo/bar
   ```
   regex matching with lax wildcard, empty final segment:
   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/regex1/foo/
   ```
   regex matching with lax wildcard, three segments:
   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/regex1/foo/bar/bam
   ```

   regex matching with strict wildcard, two segments:
   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/regex2/foo/bar
   ```
   regex matching with strict wildcard, three segments:
   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/regex2/foo/bar/bam
   ```

   regex matching with decimal digits only:
   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/regex3/foo/012873
   ```

   regex matching with decimal digits only (negative case):
   ```
   curl -i https://$ORG-$ENV.apigee.net/condition-1/regex3/foo/ABC123
   ```


