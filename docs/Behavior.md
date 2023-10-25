# Behavior

This document describes the current behavior of `gssproxy` and the `libgssapi`
interposer plugin.

Note that `gssproxy` acts as server not only for the interposer plugin but also
directly for the kernel and, potentially, for other clients, so the proxy
behavior may include additional behaviors not directly available to the
`libgssapi` interposer plugin.


## gssproxy

### Application based behavior

Currently `gssproxy` can be configured to behave differently for each user
connecting to it. By "user" here we really mean `euid` at this point (we are
planning to make it possible to act on a per-application basis provided SELinux
is used and each application has a different label that can be transmitted via
SCM Rights like calls).

The euid is obtained through a SCM Rigths call on the Unix Socket used to talk
to the proxy.

Each euid can have a config entry which can specify whether the euid is trusted
or not and based on specific mechanism some options. The currently only
supported mechanism is krb5. For this mechanism a euid specific keytab and
ccache can be specified.

When a 'user' is considered trusted it means it is allowed to command gss-proxy
to act on behalf of another user (for example init a context as a user
specified in an optional field in the protocol).

The following table represent the current thinking around default/allowed
behavior depending on the connecting peer:

| Peer         | Initiate       |         Accept             |
| ------------ | -------------- | -------------------------- |
|              |With ccache available | Never allow to accept for  |
|euid not      |always try to init    | unconfigured euids         |
|explicitly    |                      |                            |
|configured in |Use default ccache    |                            |
|gssproxy.conf |defined in [global]   |                            |
|              |                      |                            |
|              |Never use keytab      |                            |
---------------|----------------------|-----------------------------
|              |With ccache available | If keytab is explicitly    |
|`euid != 0`        |always try to init    | configured always allow to |
|(referenced   |                      | try to accept via proxy    |
| explicitly   |When keytab available |                            |
| in a config  |init with keytab only |                            |
| section)     |if following option   |                            |
|              |is set to True:       |                            |
|              |krb5_init_with_keytab |                            |
|              |defaults to False     |                            |
---------------|----------------------|-----------------------------
| `euid 0`     |                      | If keytab is explicitly    |
|              |                      | configured always allow to |
|              |                      | try to accept via proxy    |
|              |                      |                            |
|              |                      | Allow to fallback to host  |
|              |                      | keytab if not configured ? |
--------------------------------------------------------------------

### Credentials

At the moment the GSS Proxy cannot be fully stateless due to limitations in
GSSAPI (they are being addressed in MIT 1.11). The gss-proxy keeps a list of
credential structs in a ring buffer and sends applications an encrypted token to
reference them when the same credential needs to be used across multiple calls.

### Contexts

Context are always exported to the clients once obtained.  Currently both
lucid-type contexts and native MIT format contexts are supported.


## libgssapi Interposer Plugin

The interposer plugin currently tries to perform local only operations first
and falls back to attempting proxy communication if it can't obtain
contexts/credentials using local calls (`LOCAL_ONLY`, this default can be
changed at compile-time).

Furthermore, the interposer plugin behavior can be configured via the
`GSSPROXY_BEHAVIOR` environment variable, which accepts four different values:

* `LOCAL_ONLY`
* `LOCAL_FIRST`
* `REMOTE_ONLY`
* `REMOTE_FIRST`

See `man gssproxy-mec 8` for further details.

Currently only a hardcoded set of mechanisms is supported. In the future it is
planned that the supported set of mechanisms can be queried from `gssproxy` or
the configuration file instead.
