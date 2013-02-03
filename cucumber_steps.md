Cucumber steps
==============

Steps in the step file should group all steps on type.
First all 'Given' then the 'When' and finally the 'Then' steps

Each step should execute one or more helper methods, and not contain
large implementation chunks. Therefore steps should never need to call
other steps.

see:
[Cucumber: building a better World (object)](http://drnicwilliams.com/2009/04/15/cucumber-building-a-better-world-object/)

