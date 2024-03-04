# Core actions

## Introduction

Core actions are actions directly supported by the process interpreter. They relate to how a process runs, completes or fails.

## success

Complete the top-level process right away and return a success value.

## failure

[SINCE Orbeon Forms 2023.1]

Complete the top-level process right away and return a failure value.

## process

You can run a sub-process with the `process` action:

```xml
<property as="xs:string"  name="oxf.fr.detail.process.home.*.*">
    process("orbeon-home")
</property>
```

You can also run a sub-process directly by name:

```xml
<!-- Define a sub-process which navigates to the "/" URL -->
<property as="xs:string" name="oxf.fr.detail.process.orbeon-home.*.*">
    navigate("/")
</property>

<!-- Use that sub-process from the "home" process -->
<property as="xs:string" name="oxf.fr.detail.process.home.*.*">
    orbeon-home
</property>
```

## suspend

[SINCE Orbeon Forms 4.3]

Suspend the current process. The continuation of the process is associated with the current form session.

## resume

[SINCE Orbeon Forms 4.3]

Resume the process previously suspended with `suspend`.

## abort

[SINCE Orbeon Forms 4.3]

Abort the process previously suspended with `suspend`. This clears the information associated with the process and it won't be possible to resume it with `resume`.

## nop

[SINCE Orbeon Forms 4.3]

Don't do anything and return a success value.
