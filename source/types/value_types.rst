.. _basic_types:

Basic Types
===========

The following types are basic types of AVUnit, the other specific types is composed of them.
What's more, variables of these types will always be passed by value, i.e. they are always copied when they
are used as function arguments or in assignments.

.. index:: ! string

.. _type_string:

Strings
--------

``String``: The possible values are cpmposed by string values.

Operators:

*  ``+`` (string merge)

The operators ``+`` is used for string merging, it's useful when we want to name multiple agents.

BNF Defination:
::
  
  stringExpr ::= string | stringExpr '+' stringExpr
  string ::= inputCharacter | string inputCharacter
  inputCharacter ::= letter | digit | symbol

.. note::

  The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  string0 = "lane";
  string1 = "_19";
  string3 = string0 + string1;
  // The value of string3 is "lane_19".

.. index:: ! real, ! value

.. _type_realvalue:

Real Values
--------------

``Real Value``: The possible values are numbers. 

Operators:

* Arithmetic operator 1: ``^`` (exponentiation), this operator has highest priority
* Arithmetic operator 2: ``*``, ``/`` the multiplication and division shares the second priority
* Arithmetic operator 3: ``+``, ``-``, unary ``-`` (only for signed integers), the addition and minus has the least priority.

BNF Defination:
::
  
  realValue ::= [signal] number ['.'number]
  realExpr ::= <term1> | realExpr operator1 term1
  term1 ::= term2 | term1 operator2 term2
  term2 ::= term3 | term2 operator3 term3
  term3 ::= realValue | '(' realExpr ')'
  operator1 ::= + | - 
  operator2 ::= * | / |
  operator3 ::= ^

.. note::

  The range of ``Real Value`` is from ``-infinite`` to ``+infinite``. ``Real Value`` can be both Integer and Float. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  a = -2;
  b = 2^3;
  c = (a + b)*7/3;
  // The value of c is 14.

.. Comparisons
.. ^^^^^^^^^^^

.. The value of a comparison is the one obtained by comparing the integer value.

.. Bit operations
.. ^^^^^^^^^^^^^^

.. Bit operations are performed on the two's complement representation of the number.
.. This means that, for example ``~int256(0) == int256(-1)``.
.. _type_coordinate:

Coordinates 
--------------

``Coordinate``: The possible values are vectors.

Operators:

* Arithmetic operator: ``+``, ``-``, the addition and minus of coordinates.

BNF Defination:
::
  
  coordinate ::= '(' realExpr, realExpr [, realExpr] ')'
  coordinateExpr ::= coordinate | coordinateExpr operator1 coordinate
  <operator1> ::= + | - 


.. note::

  The ``Coordinate`` can be composed of two elements or three elements. The ``realExpr`` here is type of :ref:`Real Value<type_realvalue>`.The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  coord1 = (1,2);
  coord2 = (2,3) + coord1;
  coord3 = (1,2,3);
  coord4 = (2,3,-1) + coord3;