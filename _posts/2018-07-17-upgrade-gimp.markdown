---
layout: post
title:  "Upgrade Gimp"
date:   2018-07-17 09:50:00 +0800
categories: chakra packaging
---

We're planning to upgrade [Gimp](https://gimp.org) to [2.10.4](https://code.chakralinux.org/packages/gtk/issues/8)

It introduce new dependencies and give these errors while configuring

```bash
Error: GIMP configuration failed.

  - Error: missing dependency gexiv2 >= 0.10.6
  - Error: missing dependency poppler-data >= 0.4.7
```

However, we have exactly these two packages in our repo which version complies

```bash
testing/libgexiv2 0.10.8-1
core/poppler-data 0.4.8-1
```

## Why would Gimp complains about not finding them?

We first look at the packages if they were broken

```bash
pacman -Ql poppler-data 
poppler-data /usr/
poppler-data /usr/local/
poppler-data /usr/local/share/
poppler-data /usr/local/share/pkgconfig/
poppler-data /usr/local/share/pkgconfig/poppler-data.pc
poppler-data /usr/local/share/poppler/
poppler-data /usr/local/share/poppler/cMap/
```

and

```bash
libgexiv2 /usr/local/
libgexiv2 /usr/local/include/
libgexiv2 /usr/local/include/gexiv2/
libgexiv2 /usr/local/include/gexiv2/gexiv2-enums.h
libgexiv2 /usr/local/include/gexiv2/gexiv2-log.h
libgexiv2 /usr/local/include/gexiv2/gexiv2-managed-stream.h
libgexiv2 /usr/local/include/gexiv2/gexiv2-metadata.h
libgexiv2 /usr/local/include/gexiv2/gexiv2-preview-image.h
libgexiv2 /usr/local/include/gexiv2/gexiv2-preview-properties.h
libgexiv2 /usr/local/include/gexiv2/gexiv2-startup.h
libgexiv2 /usr/local/include/gexiv2/gexiv2-version.h
libgexiv2 /usr/local/include/gexiv2/gexiv2.h
libgexiv2 /usr/local/lib/
libgexiv2 /usr/local/lib/girepository-1.0/
libgexiv2 /usr/local/lib/girepository-1.0/GExiv2-0.10.typelib
libgexiv2 /usr/local/lib/libgexiv2.so
libgexiv2 /usr/local/lib/libgexiv2.so.2
libgexiv2 /usr/local/lib/libgexiv2.so.2.0.0
libgexiv2 /usr/local/lib/pkgconfig/
libgexiv2 /usr/local/lib/pkgconfig/gexiv2.pc
libgexiv2 /usr/local/share/
libgexiv2 /usr/local/share/gir-1.0/
libgexiv2 /usr/local/share/gir-1.0/GExiv2-0.10.gir
libgexiv2 /usr/local/share/vala/
libgexiv2 /usr/local/share/vala/vapi/
libgexiv2 /usr/local/share/vala/vapi/gexiv2.deps
libgexiv2 /usr/local/share/vala/vapi/gexiv2.vapi
```

This is wrong, we distributor wouldn't install files to `/usr/local` at all!

## Time for fix!

### poppler-data

Blame me @[5dc7d90c](https://code.chakralinux.org/packages/core/commit/5dc7d90c01c6ddf2075493cb80729ddb848ddbb3)

And a round up @[ee31d77a](https://code.chakralinux.org/packages/core/commit/ee31d77a8d970a08c1340af452d1540b7060dfdb)

#### Explanation

The souce code includes a matured `Makefile` and a `CMakeLists.txt`, we can directly install files via `make install`

Or, we can regenerate the `Makefile` from `cmake`

### libgexiv2

The [INSTALLING](https://gitlab.gnome.org/GNOME/gexiv2/blob/master/INSTALLING) instruction doesn't work at all.

From our PKGBUILD, we know that this package is using `meson` and `ninja` as compile system.

Thanks to Arch's helper wrapper of `meson`, we have a bunch of predefined variables to use across packages

The fix is straight-forward, we use `chakra-meson` instead of directly call `meson` at compiling. [[ref.](https://code.chakralinux.org/packages/gtk/commit/d489c313877455444347d9f072aada53700a69a4)]

### gimp revisit

By `pacman -Sii libgexiv2`, we found that it is a dependency of `gegl`, which is a dependency of `gimp`

After re-synced the repository, we rebuild gegl first.

Then, we upgrade gimp.

Now that we have updated Gimp in Chakra!

__Thanks @AlmAck and @FranzMari for importing the missing dependencies and test the glib2 update__