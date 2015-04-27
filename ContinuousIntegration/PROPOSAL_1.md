# Improve Integration of CI in the ILIAS Development Process (Draft)

## Intent

On its [second meeting](http://www.ilias.de/docu/goto_docu_file_4640_download.html) 
the SIG Refactoring discussed the integration of the CI Server in the ILIAS
development process. The basis for the discussion were the [results of a survey for 
the ILIAS developers](http://www.ilias.de/docu/goto_docu_file_4607_download.html).

This proposal tries to address some of the concerns regarding the current integration 
of the CI in the ILIAS development process.

## Proposal

### Central CI Server for Official ILIAS Repository
There should be one central CI server monitoring the official ILIAS repository. The
Server is run and administrated by the ILIAS e.V.. This server could be the already
implemented Jenkins Server as well as a new solution.

A central CI server would make it possible for all developers (and of course other
interesed parties) to refer to a common set of test, rules and results. 

We propose the following set of test:
- existing unit tests
- newly implemented architectural tests

#### Unit Tests
Currently unit tests are by choice of the responsible maintainers. This proposal does
not include any changes in the handling of unit tests. Already existing unit tests are 
included in the CI service..

#### Implement Architectural Tests in CI
To check architectural rules on the ILIAS code base, [dicto](http://scg.unibe.ch/dicto/)
is integrated in the CI server. Dicto is a simple declarative language for specifying and
automatically checking architectural rules using off-the-shelf tools.

Automated architectural tests could be used to transform current and future guidelines
to executable tests, thus improving the coherence of the ILIAS code base. E.g.: 
```
[dicto]-Rule: WholeIliasCodebase cannot depend on triggerError
```
Meaning: All PHP classes in ILIAS should not invoke the PHP function trigger_error().

A basic set of rules will be proposed by the SIG Refactoring.

### Examine Results Collectively
The results of the CI are examined collectively at every Jour Fixe. This is done in
a similar fashion then the current examination of the bugtracker at every Jour Fixe
or the examination of TestRail in the testing phase for the 5.0 release.
I.e. it is not necessary to look at every result in depth but to get a common sense
about how things are evolving and whether metrics improve or not. 

This measure is taken to improve the visibility of the CI server among developers
and create a sense of shared responsibility for the quality of the code base. It is
desired to integrate the CI in the ILIAS development process more closely in the 
future.

### Enhance Feedback Cycle 
The metrics in the CI are generated per commit. An overview of the latest results
of the CI is sent to all ILIAS developers weekly. After each commit the results of
the CI on the commit are sent to the commiting developer.

This measure tightens the feedback loop for the developers and makes it possible to
note and fix problems in a timely manner.

### Jour Fixe decides on Architectural Rules 
The Jour Fixe decides on the implementation of new architectural rules in the CI. 

The SIG Refactoring will propose rules to the Jour Fixe. It would also like to act 
as an instance where new rules could be proposed by third parties. This would help
to maintain a consistent rule set and save time on the actual Jour Fixes.
