---
title: Bind Mounting On macOS
---

Bind mounts on macos are implemented in the XNU kernel (as nullfs in its BSD components) but are hidden from the user.

For most uses FUSE elleviates this issue as [bindfs](https://formulae.brew.sh/formula/bindfs) is available for making bind mounts in userspace.

However for in some cases we might want to make use of the kernel implementation of bind mounts.

There are three main roadblocks:
1. mount_null does not exist
2. SIP prevents all programs without the `com.apple.private.nullfs_allow` entitlement from mounting nullfs
3. Normal developers cant give programs private entitlements.

### Building mount_null

Implementing mount_null is as simple as modifying the existing source code for another mount command to mount the implemented nullfs filesystem. We dont actually have to do this because georghe-crihan has done it for us [here](https://github.com/georghe-crihan/binary-kernel-modules/blob/2a489b7a98d37891b7759b7d9f746ae682ae1bd6/mount_nullfs/mount_null.c).

Compiling this we get a mount_null binary but it fails with the `Operation not permitted` error indicating that we need more entitlements to run it.

### Getting Entitlements

Normally getting an Apple private entitlement would require signing the application with an Apple certificate which we obviously cant get.

The way we can get around this is by signing our program with a root-trusted code signing certificate which we can make for personal use with the guide on [this](https://sourceware.org/gdb/wiki/PermissionsDarwin) page.

Drawbacks to this way of signing is that our `mount_null` wont be usable on any other macOS machine without first trusting our root certificate for code signing.
