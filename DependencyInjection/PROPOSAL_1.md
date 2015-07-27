# Start to Get Rid of Global Variables (Draft)

## Intent

ILIAS uses a lot of globals to make system wide services accessible from
everywhere. Examples for those services are logging, RBAC and Control-flow.
It is well known that globals are an obstacle for unit tests and also hide
dependencies of objects somewhere in the methods instead of making them
explicit.

The intent of this proposal therefore is to make a first step to abandoning
globals by replacing them with a global registry, which afterwards will be
the only global object in the system.

## Proposal

### Introduction of Pimple

The dependency injection container [Pimple](http://pimple.sensiolabs.org/)
is pulled into the ILIAS core. Pimple is licensed with the MIT license and
could therefore be used in ILIAS without licensing problems.

It is also proposed to copy the Pimple code to ILIAS instead of understanding
it as an external dependency. This would allow to modify Pimple in the case
when there are ILIAS specific requirements for a DIC. As Pimple only comprises
~300 lines including comments and is a battle tested component, it should 
be easily possible to maintain Pimple as a part of ILIAS.

### Introduction of a Global Pimple Container Object

A global instance of a pimple container is introduced in the `ilInitialisation`.
It is created as first step of the initialisation process in `ilInitalisation::initILIAS`.
The name of the global is `$DIC`.

### Deprecating all Globals besides Pimple Container Object

The use of any global in the ILIAS core besides the global pimple container
object is deprecated with the beginning of the ILIAS 5.2 development. The 
migration process should be terminated with the release of ILIAS 5.2.. Afterwards
any global besides the pimple container is considered a bug. Appropriate rules
for the CI are introduced as soon as possible to assess the amount of work to
do for the migration.

### Deprecating the Initialisation of Globals besided the Pimple Container Object

The initialisation of globals outside of `ilInitialisation` is deprecated with the
beginning of the ILIAS 5.2 development. An appropriate rule for the CI is 
introduced as soon as possible to asses the amount of necessary amount of work.

### Forbid the Modification of the Global Pimple Container Object

It is not allowed to modify the global pimple container object outside of 
`ilInitialisation`. This is check by an appropriate rule in the ILIAS-CI.

### Migrating Globals to Global Registry

The SIG-Refactoring provides a script to automatically replace the use of globals 
by the global pimple container, that is transforming

```php
	global $ilLog;
```

to

```php
	global $DIC;
	$ilLog = $DIC["ilLog"];
```

and

```php
	some_function($GLOBALS["ilLog"]);
```

to

```php
	some_function($GLOBALS["DIC"]["ilLog"]);
```

As it won't be possible to replace all globals with a script based on regular
expression, the script provided by the SIG Refactoring will also flag possibly
remaining pathological cases of the use of globals. Those have to be fixed
manually.

Every ILIAS developer is responsible to migrate the code she maintains, starting
with the development of ILIAS 5.2..

The SIG Refactoring will provide a pull request to the ILIAS repo that pairs
initialisations of globals in `ilInitialisation` by a corresponding initialisation
of the pimple container. During the migration phase it will be possible to use
the old globals and the new pimple container object as well.

When the migration of the user sides of globals are completed, the SIG refactoring
will provide a pull request that removes the initialisation of globals in the
`ilInitialisation` completely.

### Outlook

The introduction of a global container object as registry for globals only is
the first step towards getting rid of global in ILIAS completely.
A possible next step is to overhaul the ILIAS initialisation to use the 
capabilities of the DIC to a greater extent. The DIC could supersede the current 
ILIAS initialisation, where the order in which the global services are 
initialised is explicitly given in the code. This order could be automatically 
infered by the DIC from the dependencies of the different services.