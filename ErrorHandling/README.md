# Improvement of ILIAS Error Handling 

The improvement of error handling in ILIAS was chosen to be the first project
of the SIG Refactoring on the [first meeting of the SIG on 2015-01-28](http://www.ilias.de/docu/ilias.php?ref_id=4516&cmd=sendfile&cmdClass=ilrepositorygui&cmdNode=65&baseClass=ilRepositoryGUI).

## Intent

Currently the error handling in ILIAS is not implemented in a coherent fashion.
Besides the error handling via exceptions, where ILIAS uses a custom exception
class, some parts of the code also use PHP error handlers provided by the PEAR
library to signal runtime errors.
When an exception is not caught, the user is redirected to the error.php-page,
losing the context under which the exception was thrown. This makes it unnecessarily
hard for a developer to find the problem which causes the exception.

This proposal aims at unifying the error handling in ILIAS and providing facilities
for the ILIAS developers to analyze the source of an error.

## Requirements

* Exceptions should not be leaving the context under which they were thrown.
* It should be possible for developers to recover context information from an
  exception to make analysis of exceptional cases easier.
* All exceptional cases in ILIAS should be handled via exceptions, the usage of
  PEAR error handlers should be disestablished.
* A top level exception handler should be introduced that makes it possible to
  provide information for developers when the DEVMODE of ILIAS is used.

## Proposal

The SIG-Refactoring checked the current state of ILIAS error handling and 
proposes the following changes to ILIAS to achieve the requirements outlined
above. To disestablish the PEAR error handlers, two options are proposed,
where comments are requested from the other ILIAS-developers and the Head 
of Development.

### Introduction of Whoops

A top level exception handler should be introduced, that catches all exceptions
that aren't caught and handled in the code. This exception handler should be provided
by [Whoops](https://github.com/filp/whoops).

Whoops is an error handling framework that is able to create developer friendly 
error pages out of the box, showing the exception, the code it was thrown from 
and the data present in the HTTP-request.

It is distributed under the MIT license which is compatible with the GPL-3 used 
by ILIAS. At it seems to be mature framework, the SIG considers it to be a low
risk move to integrate it into ILIAS.

It provides a global shutdown function, which will catch exceptions and fatal
errors as well.

### Remove PEAR error handlers

The error handler provided by pear should be removed completely. As there are
dependencies on PEAR_ErrorHandler in some parts of the code, the SIG proposes
two options how to deal with those:

* **Option 1:** Remove the PEAR_ErrorHandler calls completely and throw exceptions
  instead.
* **Option 2:** Rip the PEAR_ErrorHandler and throw exceptions from there.

While Option 1 seems to be the cleaner one, Option 2 seems to require less effort
to be realized.

## Outlook

The SIG Refactoring considers this proposal as a first step towards an enhanced
error handling in ILIAS, where future steps could provide even more improvements:

* A set of standard ILIAS exceptions could be introduced to facilitate the handling
  of exceptions in ILIAS.
* Access handling could be exchanged to throw exceptions as well.
* In general an improved system of exceptions could help to only encode the happy
  path in the code and handle exceptions where they could be handled, this making
  the code easier to read and reason about.
* The errors could be used to generate appropriate HTTP status codes, making it
  easier to monitor running ILIAS systems for different error modes.
* The top level exception handler could depend on the context ILIAS currently runs
  in and thus provide different error handling for the different contexts (Web, SOAP
  Cron, ...).

## Status

This proposal was accepted on the [Jour Fixe of the ILIAS e.V. on 2015-03-02](http://www.ilias.de/docu/goto.php?target=wiki_1357_JourFixe-2015-03-02).


