[![Changelog](https://img.shields.io/github/v/release/gssapi/gssproxy?label=changelog)](https://github.com/gssapi/gssproxy/releases)
[![Build Status](https://github.com/gssapi/gssproxy/actions/workflows/ci.yaml/badge.svg)](https://github.com/gssapi/gssproxy/actions/workflows/ci.yaml)

This is the `gssproxy` project.

Documentation lives in the [docs folder](/docs/README.md), the
latest version can be found in the [upstream
repository](https://github.com/gssapi/gssproxy/tree/master/docs/README.md).

The goal is to have a GSS-API proxy, with a standardizable protocol and a
(somewhat portable) reference client and server implementation.  There
are several motivations for this, some of which are:

 - Kernel-mode GSS-API applications (CIFS, NFS, AFS, ...) need to be
   able to leave all complexity of GSS\_Init/Accept\_sec\_context() out of
   the kernel by upcalling to a daemon that does all the dirty work.

 - Isolation and privilege separation for user-mode applications.  For
   example: letting HTTP servers use but not see the keytab entries for
   `HTTP/*` principals for accepting security contexts.

 - Possibly an `ssh-agent`-like agent for GSS credentials -- a `gss-agent`.

We have a
[mailing list](https://lists.fedorahosted.org/archives/list/gss-proxy@lists.fedorahosted.org/)
and an IRC channel (`#gssapi` on [libera.chat](https://libera.chat/)).
