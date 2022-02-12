# email sending library

[![Build Status](https://github.com/go-pkgz/email/workflows/build/badge.svg)](https://github.com/go-pkgz/email/actions) [![Coverage Status](https://coveralls.io/repos/github/go-pkgz/email/badge.svg?branch=master)](https://coveralls.io/github/go-pkgz/email?branch=master) [![Go Reference](https://pkg.go.dev/badge/github.com/go-pkgz/email.svg)](https://pkg.go.dev/github.com/go-pkgz/email)


The library is a wrapper around the stdlib `net/smtp` simplifying email sending. It supports authentication, SSL/TLS, 
user-specified SMTP servers, content-type, charset, multiple recipients and more.
    
Usage example:

```go
client := email.NewSender("localhost", email.ContentType("text/html"), email.Auth("user", "pass"))
err := client.Send("<html>some content, foo bar</html>", 
	email.Params{From: "me@example.com", To: []string{"to@example.com"}, Subject: "Hello world!"})
```

## options

`NewSender` accepts a number of options to configure the client:

- `Port`: SMTP port (default: 25)
- `TLS`: Use TLS SMTP (default: false)
- `Auth(user, password)`: Username and password for SMTP authentication (default: empty, no authentication)
- `ContentType`: Content type for the email (default: "text/plain")
- `Charset`: Charset for the email (default: "utf-8")
- `TimeOut`: Timeout for the SMTP connection (default: 30 seconds)
- `Log`: Logger to use (default: no logging)
- `SMTP`: Set custom smtp client (default: none)

_Options should be passed to `NewSender` after the mandatory first (host) parameter._

## sending email

To send email user need to create a sender first and then use `Send` method. The method accepts two parameters:

- email content (string)
- parameters (`email.Params`)
    ```go
    type Params struct {
        From    string   // From email field
        To      []string // From email field
        Subject string   // Email subject
    }
    ```

## technical details

- Content-Transfer-Encoding set to `quoted-printable`
- Custom SMTP client (`smtp.Client` from stdlib) can be set by user with `SMTP` option. In this case it will be used instead of making a new smtp client internally.
- Logger can be set with `Log` option. It should implement `email.Logger` interface with a single `Logf(format string, args ...interface{})` method. By default, "no logging" internal logger is used. This interface is compatible with the `go-pkgz/lgr` logger.
- The library has no external dependencies, except for testing. It uses the stdlib `net/smtp` package.
- SSL/TLS supported with `TLS` option. Pls note: this is not the same as `STARTTLS` (not supported) which is usually on port 587 vs SSL/TLS on port 465.

## limitations

This library is not intended to be used for sending emails with attachments or sending a lot of massive emails with 
low latency requirements. The intended use case is sending simple messages, like alerts, notification and so on.
For example, sending alerts from a monitoring system, or for authentication-related emails, i.e.  "password reset email", 
"verification email", etc.
