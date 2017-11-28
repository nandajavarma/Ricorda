http://home.deib.polimi.it/pradella/PL.html
https://www.reactivemanifesto.org/


* Scheme
    Prefix language
    Parantheses FTW
    s-expressions
    symbols for metaprogramming


    expression -- (+ 1 2) The order in which the subexpressions evaluate
    the sub expressions is not defined. + -> symbol -> a pointer to the
    procedure addition. You cannot reply on the order of evaluation. If
    in your program you expect the expression to be evaluated in a
    specified way then your program is wrong. especially when there are
    side effects.
