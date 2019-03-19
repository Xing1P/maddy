# maddy

Fast, cross-platform mail server.

Inspired from [Caddy](https://github.com/mholt/caddy).

## Features

* Easy configuration with Maddyfile (same as Caddyfile)
* Transparent PGP support
* Runs anywhere with no external dependencies (not even libc)

## Installation

```shell
go get github.com/emersion/maddy/cmd/maddy
```

## Configuration

#### Syntax

Maddy uses configuration format similar (but not the same!) to Caddy's
Caddyfile.  You may want to read [Caddyfile page from Caddy docs](https://caddyserver.com/docs/caddyfile).

Notable differences from Caddy's format:
* Directives can't span multiple lines
* Environmental variables are not expanded
* You can't omit braces if you have only one configuration block

#### Modularity

Maddy does have a module-based design and you specify in configuration modules
you want to use and how you want them to work.

Generic syntax for configuration block is as follows:
```
module_name instance_name {
    configuration_directives
}
```

`instance_name` is the unique name of the configuration block. It is used when
you need to refer to the module from different place in configuration (e.g.
configure SMTP to deliver mail to certain specific storage)

#### Defaults

Maddy provides reasonable defaults so you can start using it without spending
hours writing configuration files. All you need it so define smtp and imap
modules in your configuration, configure TLS (see below) and set domain name.

Here is the minimal example to get you started:
```
imap imap://0.0.0.0 imaps://0.0.0.0 {
    tls cert_file pkey_file
}

smtp smtp://0.0.0.0 smtps://0.0.0.0:587 {
    tls cert_file pkey_file
    hostname emersion.fr
}
```
Don't forget to use actual values instead of placeholders.

With this configuration, maddy will create an SQLite3 database for messages in
current directory and use it to store all messages.

#### go-sqlmail: Database location

If you don't like SQLite3 or don't want to have it in the current directory,
you can override the configuration of the default module.

See [go-sqlmail repository](https://github.com/foxcpp/go-sqlmail) for
information on RDBMS support.

```
sqlmail default {
    driver sqlite3
    dsn file_path
}
```

You can then replace SQL driver and DSN values. Note that maddy needs to be
built with a build tag corresponding to the name of the used driver (`mysql`,
`postgresql`) for SQL engines other than sqlite3.

DSN is a driver-specific value that describes the database to use.
For SQLite3 this is just a file path.
For MySQL: https://github.com/go-sql-driver/mysql#dsn-data-source-name
For PostgreSQL: https://godoc.org/github.com/lib/pq#hdr-Connection_String_Parameters

#### TLS

Currently, maddy doesn't implement any form of automatic TLS like Caddy. But
since we don't want to have insecure defaults we require users to either
manually configure TLS or disable it explicitly.

Valid variants of TLS config directive:

Disable TLS:
```
tls off
```

Use temporary self-signed certificate (useful for testing):
```
tls self_signed
```

Use specified certificate and private key:
```
tls cert_file pkey_file
```

#### SMTP pipeline & other customization options 

List of all configuration options and all modules you can use is in
[CONFIG_REFERENCE.md](CONFIG_REFERENCE.md) file.

## License

MIT
