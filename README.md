# gren-cookies

Read and write HTTP cookies in your Gren application.

```
gren package install joeybright/gren-cookies
```

## How it works

This package lets you define a cookie with a name, encoder, and decoder. Once
created, you can use that same cookie to read values from requests, add them to
responses, or clear them from the browser.

## Getting started

Define a cookie with the type of value you want to store:

```gren
import Cookie
import Cookie.Decode
import Cookie.Encode

visitCookie : Cookie.Cookie Cookie.Insecure Int
visitCookie =
    Cookie.create
        { name = "visits"
        , encoder = Cookie.Encode.int
        , decoder = Cookie.Decode.int
        }
```

In your HTTP request handler, read the cookie and use its value in the response:

```gren
import HttpServer.Response as Response

when Cookie.fromRequest visitCookie request is
    Ok visits ->
        response
            |> Response.setBody ("Visits: " ++ String.fromInt visits)
            |> Response.send

    Err _ ->
        response
            |> Response.setBody "No valid visit cookie found."
            |> Response.send
```

To store a value, set the cookie on the response before sending it:

```gren
response
    |> Cookie.set visitCookie 42
    |> Response.setBody "Visit count saved."
    |> Response.send
```

To clear it, send a response using the same cookie definition:

```gren
response
    |> Cookie.clear visitCookie
    |> Response.setBody "Visit count cleared."
    |> Response.send
```
