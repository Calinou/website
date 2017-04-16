---
title: "Version 0.17.0 released"
author: The Nim Team
---

This release fixes the most important regressions introduced in 0.16.0. In
particular memory manager and channel bugs have been fixed. The NSIS based
installer is not provided anymore as the Nim website moved to ``https`` and
this causes NSIS downloads to fail.


Changelog
~~~~~~~~~

Changes affecting backwards compatibility
-----------------------------------------

- ``httpclient.request`` now respects ``maxRedirects`` option. Previously
  redirects were handled only by ``get`` and ``post`` procs.
- The IO routines now raise ``EOFError`` for the "end of file" condition.
  ``EOFError`` is a subtype of ``IOError`` and so it's easier to distinguish
  between "error during read" and "error due to EOF".
- A hash procedure has been added for ``cstring`` type in ``hashes`` module.
  Previously, hash of a ``cstring`` would be calculated as a hash of the
  pointer. Now the hash is calculated from the contents of the string, assuming
  ``cstring`` is a null-terminated string. Equal ``string`` and ``cstring``
  values produce an equal hash value.
- Macros accepting `varargs` arguments will now receive a node having the
  `nkArgList` node kind. Previous code expecting the node kind to be `nkBracket`
  may have to be updated.
- ``memfiles.open`` now closes file handleds/fds by default.  Passing
  ``allowRemap=true`` to ``memfiles.open`` recovers the old behavior.  The old
  behavior is only needed to call ``mapMem`` on the resulting ``MemFile``.
- ``posix.nim``: For better C++ interop the field
  ``sa_sigaction*: proc (x: cint, y: var SigInfo, z: pointer) {.noconv.}`` was
  changed
  to ``sa_sigaction*: proc (x: cint, y: ptr SigInfo, z: pointer) {.noconv.}``.
- The compiler doesn't infer effects for ``.base`` methods anymore. This means
  you need to annotate them with ``.gcsafe`` or similar to clearly declare
  upfront every implementation needs to fullfill these contracts.
- ``system.getAst templateCall(x, y)`` now typechecks the ``templateCall``
  properly. You need to patch your code accordingly.
- ``macros.getType`` and ``macros.getTypeImpl`` for an enum will now return an
  AST that is the same as what is used to define an enum.  Previously the AST
  returned had a repeated ``EnumTy`` node and was missing the initial pragma
  node (which is currently empty for an enum).
- If the dispatcher parameter's value used in multi method is ``nil``,
  a ``NilError`` exception is raised. The old behavior was that the method
  would be a ``nop`` then.
- ``posix.nim``: the family of ``ntohs`` procs now takes unsigned integers
  instead of signed integers.
- In Nim identifiers en-dash (Unicode point U+2013) is not an alias for the
  underscore anymore. Use underscores and fix your programming font instead.
- When the ``requiresInit`` pragma is applied to a record type, future versions
  of Nim will also require you to initialize all the fields of the type during
  object construction. For now, only a warning will be produced.
- The Object construction syntax now performs a number of additional safety
  checks. When fields within case objects are initialiazed, the compiler will
  now demand that the respective discriminator field has a matching known
  compile-time value.

Library Additions
-----------------

- Added ``system.onThreadDestruction``.


Tool Additions
--------------

- The ``finish`` tool can now download MingW for you should it not find a
  working MingW installation.


Compiler Additions
------------------

- The name mangling rules used by the C code generator changed. Most of the time
  local variables and parameters are not mangled at all anymore. This improves
  debugging experience.
- The compiler produces explicit name mangling files when ``--debugger:native``
  is enabled. Debuggers can read these ``.ndi`` files in order to improve
  debugging Nim code.


Language Additions
------------------

- The ``try`` statement's ``except`` branches now support the binding of a
caught exception to a variable:

.. code-block:: nim
  try:
    raise newException(Exception, "Hello World")
  except Exception as exc:
    echo(exc.msg)

This replaces the ``getCurrentException`` and ``getCurrentExceptionMsg()``
procedures, although these procedures will remain in the stdlib for the
foreseeable future. This new language feature is actually implemented using
these procedures.

In the near future we will be converting all exception types to refs to
remove the need for the ``newException`` template.

- A new pragma ``.used`` can be used for symbols to prevent
the "declared but not used" warning. More details can be
found `here <http://nim-lang.org/docs/manual.html#pragmas-used-pragma>`_.
- The popular "colon block of statements" syntax is now also supported for
  ``let`` and ``var`` statements and assignments:

.. code-block:: nim
  template ve(value, effect): untyped =
    effect
    val

  let x = ve(4):
    echo "welcome to Nim!"

This is particularly useful for DSLs that help in tree construction.


Language changes
----------------

- The ``.procvar`` annotation is not required anymore. That doesn't mean you
  can pass ``system.$`` to ``map`` just yet though.


Bugfixes
--------

The list below has been generated based on the commits in Nim's git
repository. As such it lists only the issues which have been closed
via a commit, for a full list see
`this link on Github <https://github.com/nim-lang/Nim/issues?utf8=%E2%9C%93&q=is%3Aissue+closed%3A%222017-01-07+..+2017-02-06%22+>`_.

- Fixed "Weird compilation bug"
  (`#4884 <https://github.com/nim-lang/Nim/issues/4884>`_)
- Fixed "Return by arg optimization does not set result to default value"
  (`#5098 <https://github.com/nim-lang/Nim/issues/5098>`_)
- Fixed "upcoming asyncdispatch doesn't remove recv callback if remote side closed socket"
  (`#5128 <https://github.com/nim-lang/Nim/issues/5128>`_)
- Fixed "compiler bug, executable writes into wrong memory"
  (`#5218 <https://github.com/nim-lang/Nim/issues/5218>`_)
- Fixed "Module aliasing fails when multiple modules have the same original name"
  (`#5112 <https://github.com/nim-lang/Nim/issues/5112>`_)
- Fixed "JS: var argument + case expr with arg = bad codegen"
  (`#5244 <https://github.com/nim-lang/Nim/issues/5244>`_)
- Fixed "compiler reject proc's param shadowing inside template"
  (`#5225 <https://github.com/nim-lang/Nim/issues/5225>`_)
- Fixed "const value not accessible in proc"
  (`#3434 <https://github.com/nim-lang/Nim/issues/3434>`_)
- Fixed "Compilation regression 0.13.0 vs 0.16.0 in compile-time evaluation"
  (`#5237 <https://github.com/nim-lang/Nim/issues/5237>`_)
- Fixed "Regression: JS: wrong field-access codegen"
  (`#5234 <https://github.com/nim-lang/Nim/issues/5234>`_)
- Fixed "fixes #5234"
  (`#5240 <https://github.com/nim-lang/Nim/issues/5240>`_)
- Fixed "JS Codegen: duplicated fields in object constructor"
  (`#5271 <https://github.com/nim-lang/Nim/issues/5271>`_)
- Fixed "RFC: improving JavaScript FFI"
  (`#4873 <https://github.com/nim-lang/Nim/issues/4873>`_)
- Fixed "Wrong result type when using bitwise and"
  (`#5216 <https://github.com/nim-lang/Nim/issues/5216>`_)
- Fixed "upcoming.asyncdispatch is prone to memory leaks"
  (`#5290 <https://github.com/nim-lang/Nim/issues/5290>`_)
- Fixed "Using threadvars leads to crash on Windows when threads are created/destroyed"
  (`#5301 <https://github.com/nim-lang/Nim/issues/5301>`_)
- Fixed "Type inferring templates do not work with non-ref types."
  (`#4973 <https://github.com/nim-lang/Nim/issues/4973>`_)
- Fixed "Nimble package list no longer works on lib.html"
  (`#5318 <https://github.com/nim-lang/Nim/issues/5318>`_)
- Fixed "Missing file name and line number in error message"
  (`#4992 <https://github.com/nim-lang/Nim/issues/4992>`_)
- Fixed "ref type can't be converted to var parameter in VM"
  (`#5327 <https://github.com/nim-lang/Nim/issues/5327>`_)
- Fixed "nimweb ignores the value of --parallelBuild"
  (`#5328 <https://github.com/nim-lang/Nim/issues/5328>`_)
- Fixed "Cannot unregister/close AsyncEvent from within its handler"
  (`#5331 <https://github.com/nim-lang/Nim/issues/5331>`_)
- Fixed "name collision with template instanciated generic inline function with inlined iterator specialization used from different modules"
  (`#5285 <https://github.com/nim-lang/Nim/issues/5285>`_)
- Fixed "object in VM does not have value semantic"
  (`#5269 <https://github.com/nim-lang/Nim/issues/5269>`_)
- Fixed "Unstable tuple destructuring behavior in Nim VM"
  (`#5221 <https://github.com/nim-lang/Nim/issues/5221>`_)
- Fixed "nre module breaks os templates"
  (`#4996 <https://github.com/nim-lang/Nim/issues/4996>`_)
- Fixed "Cannot implement distinct seq with setLen"
  (`#5090 <https://github.com/nim-lang/Nim/issues/5090>`_)
- Fixed "await inside array/dict literal produces invalid code"
  (`#5314 <https://github.com/nim-lang/Nim/issues/5314>`_)

