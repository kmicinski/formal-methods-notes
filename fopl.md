# First-Order Logic for Computer Science

This set of notes is meant to be read alongside Melvin Fitting's
"First-Order Logic and Automated Theorem Proving" These notes roughly
follow chapter 5 in that book.

One popular everyday logic---indeed the logic with which you are
probably most intuitively familiar---is first-order propositional
logic (FOPL). FOPL includes variables, the connectives "or" and "and",
written `/\` and `\/`, along with the implication `->`. We also allow
negation, though there is some trickiness on the interpretation we
take to it. Also, there is the ability to talk about predicates whose
arguments are expressions. Expressions are either constants,
variables, or functions whose arguments are expressions. For example:

P(f(x),x)

Asserts some predicate P of two arguments f(x) and x. Of course this
is not syntactically apparent: we must specify a syntactic set of
predicates and a syntactic set of functions. Thus, FOPL is specified
in terms of a first-order language, L⟨R,F,C⟩, where R is a set of
relations / predicates, F is a set of function symbols, and c is a set
of constants.

A very important restriction in FOPL is that predicates contain
expressions as arguments, but expressions cannot contain
predicates. This stratification makes predicates a bit "second class,"
but simplifies our presentation---defining the semantics of relations
is much easier when we don't have to do so mutually alongside defining
the semantics of expressions.

It may seem like a minor point right now, but we need to take a lot of
care to connect the logical *language*---which is an infinite set of
mathematical objects representing first-order terms and defined
syntactically / algebraically via a grammar---from its *interpretation*
as a mathematical object. We can think of first-order predicate logic
as having a semantics in the same way as a programming language has a
semantics: via the interpretation of its syntactic structure into some
mathematical domain.

Up until now, I have restricted myself to describing only predicate
logic. But first-order predicate logic adds one more thing: the
quantifiers "for all" written as ∀, and "there exists" ∃. For example,
I might say:

```
∀x. P(x) /\ Q(x) -> Q(x) /\ P(x)
```

This is read as: for all x, P(x) and Q(x) being true implies that Q(x)
and P(x) must be true. When we say it like this, it seems obviously
true, but that's a bit deceitful: I explained it via an English
sentence, and my brain is naturally extrapolating a model from the
structure. There are, of course, many formulas which are invalid:

```
∀x. P(x) -> ¬ P(x)
```

And there are formulas which are valid under some assignments but not
others:

```
∀x. P(x) -> P(y)
```

Most formulas don't have much meaning on their own. Sure, certain
things are tautologies, like these:

```
∀x. P(x) -> P(x)
∀x. P(x) -> P(x) ∨ R(x)
∀x. ∀y. P(x) ∧ P(y) -> P(x) ∨ P(y)
```

But these formulas are often uninteresting to us, because they don't
really admit much case-specific reasoning for us to exploit and use to
build cool applications as computer scientists. If I had a machine
that could prove formulas for you in FOPL, but it could only work with
these formulas, it might be a little less interesting than if it could
work with formulas like these:

```
∀x. ∀y. >=(+(x,(+(x,y))),+(*(2,x),y))
```

Which you probably recognize more readily as:

```
∀x. ∀y. x + x + y >= 2x + y
```

These kinds of formulas only really have a meaning when we have a
sensible interpretation of things like >=, +, *, and etc... To do this
we define a first-order structure as follows:

- We need a set D or U, called the "domain of discourse" or the
  "universe"

- We need an interpretation I, which assigns, to each non-logical
  symbol, a mapping. Thus I assigns:

    - To each constant c in the language, a corresponding element c' ∈ D
    - To each function symbol in the language of arity n, a
      corresponding function f ∈ Dⁿ → D.
    - To each predicate symbol in the language, a boolean-valued
      function that maps Dⁿ → ⎨true,false⎬.

Note that first-order logic is "first order" because quantification is
only allowed over objects of D, rather than relations themselves. When
we write "∀x ..." we implicitly mean, "for all x ∈ D." Specifically,
first-order logic does not allow quantifying over *relations*
themselves. Second and higher-order logics (higher-order logics are
reducible to second-order logic wrt certain semantic properties) are
more complex to formulate, and care must be taken to avoid things like
Russel's paradox (https://en.wikipedia.org/wiki/Russell%27s_paradox).

It is worth taking a step back now to think about why we need to set
things up this way. Remember, our goal is to give a mathematical
interpretation to formulas. For example, I might have a formula such
as, `R(f(x),g(y))`. If I take D to be the integers, R to be greater
than, f to be the "add1" function, `x` to be `1`, and `y` to be `0`,
this formula is true. But how do I know that? Intuitively I can think
of "running" the formula. First, I need to tell you the values of `x`
and `y`, as they are "free." Formulas are closed when they contain no
free (unquantified) variables. As in other branches of programming
languages (e.g., lambda calculi), substitution is boring, laborious,
important, and I will not focus too much on it here. But clearly it is
easier to allow me to define an interpretation over base values, and
assume that the logic will handle substitution in a consistent way.

We say that a formula "evaluates to" either true or false given an
interpretation I and a variable assignment A ∈ Var → D, which maps
each free variable in the formula to some value in D. To avoid
specifying "free variables," it may simply help to assume this is a
total function which may assign any arbitrary value in D for variables
not used in the formula. Be careful to notice that there is a
difference between an assignment and a substitution: a substitution
maps variables to expressions, while an assignment maps variables to
objects in D. One maps onto syntax, while the other maps onto objects
in the domain of discourse. This is easy to confuse.

A "model" of a first-order language is a pair of ⟨D,I⟩ where D is a
domain of discourse and I is an interpretation given above. Care must
be taken to ensure you do not confuse objects in the language with
elements of the model. For example, consider the following first-order
sentence:

   0 + 0 = 1

This looks obviously false. But I can choose a model wherein this is
true: pick D to be {0}, map all constants to 0, and define f(x,y) = x
in the interpretation. You should read through the other examples in
5.3 to build more intuition.

We say that a formula Φ is "true in the model M" if the formula is
true given any possible assignment of its free variables. We say a
formula is "valid" when it is true in *all* models. For example,
consider the following statement:

    ∀x. x + 1 > 0

This statement is true in the model ⟨Nat,I⟩ where I is the
interpretation that maps + to its usual interpretation and > is the
usual "greater than" relation. However, the formula is false when we
allow D to be the integers.

By contrast, the following statement is valid:

    ∀x. x + 1 = x + 1

This is true because + is a function, and syntactically identical
terms will evaluate to semantically-equivalent interpretations in
every model.

A formula is "satisfiable" if there is *some* assignment that will
make it true. For example, the following formula is satisfiable in our
above model:

    ∀x. x + y = x + 1

By contrast, the following formula is not satisfiable:

    ∀x. x + y = x ∧ x + y != x

A very important observation, used throughout formal methods, is the
following: Assume Φ is first-order formula. Φ is valid precisely when
¬Φ is satisfiable. In other words, if we want to check the validity of
Φ, we can attempt to find a contradiction in ¬Φ. This is good: it
turns a "for all" problem into a "there exists" model. Many
first-order and SMT solvers use this observation to prove validity or
hunt down contradictions.

Recall that interpretations may map constants from the language into
any arbitrary element of the domain D. This leads to some confusing
stuff: it would be weird to map the source constant 1 to integer 0. A
model of a first-order language L is called a "Herbrand model" if D
precisely coincides with the closed terms of L, and the interpretation
I maps closed terms to closed terms. Herbrand models are a bit
special, and have several special properties that I will allow you to
read about. But you can think of them as preserving syntactic identity
into the model.

Example: a Herbrand model of a language ⟨{<}, {+}, {0,1}⟩ will contain
as its elements things like 0, 1, +(0,1), +(0,+(1,0)),
etc... Specifically, it *cannot* simply be the Naturals: +(1,0) in the
syntax must map to +(0,1) (structurally) in the model. Of course, a
statement may have multiple satisfying assignments (and thus models),
but a *Herbrand* model must treat "syntax as syntax."

#### First order theories, model existence, and completeness

We conclude our notes on first-order logic by stating the model
existence theorem. This theorem is important because it allows easily
proving first-order completeness (which we will not do).

We first need to define a first order theory. This is an important
concept, and you should commit to remembering it, as it will crop up a
lot:

> A first order "theory" for a first-order language L is a set of
> statements called "axioms" of that theory.

Intuitively, a first-order theory is a set of rules we must follow for
things in that theory to be true. For our theory to be useful, it must
be "consistent", i.e., it must not be the case that we can derive a
contradiction from the set of axioms in our theory. If our theory is
inconsistent, it is useless: once we can derive a contradiction, we
can prove anything. Thus, we care only about consistent first-order
theories: an inconsistent theory would be "buggy."

I often think of theories T as generating a closure of "true closed
sentences," i.e., a set of first-order sentences in L derivable via
the axioms.

You should look at the Wikipedia page of first-order theories for some
good examples:
https://en.wikipedia.org/wiki/List_of_first-order_theories

First-Order Model Existence says the following:

Assume that T is a consistent first-order theory, i.e., it is
impossible to derive a contradiction from the axioms in T. First-order
model existence says that there exists *some* Herbrand model M that
exists for T.

Intuitively, first-order model existence says the following: as long
as my first-order theory T is consistent (i.e., not buggy), I can find
some Herbrand model for it. Recall that Herbrand models map source
terms to semantic terms identically, so the constant elements of D in
this model M will be syntactically identical to the closed terms of
the language L for T.

So far in these notes, we have only talked about models, not
proofs. Next time, we will talk about proofs. Proofs are syntactic
structures that justify statements made about a given first-order
theory. "Proofs" in everyday colloquialism just mean some
convincing-enough argument to show that something is true. But if we
want proofs to be meaningful, we must mathematically represent proofs
as objects whose meaning we can define.

Gödel famously proved the following statement:

> Assume Φ is a valid first-order formula, i.e., true in every
> model. Then there exists some finite deduction (proof) of Φ.

To understand this statement, we will need to formally study proofs as
syntactic objects. In subsequent notes, we will cover natural
deduction, Gentzen-style proofs, and sequent calculus.

Why should we care about this as computer scientists? Well, we wonder
whether we could write a program that will consume a first-order
statement and produce its proof. Completeness tells us that, assuming
the statement is true, we can find a proof for it in finite time. This
opens up the door for us to write first-order theorem provers, which
output syntactic proofs of true things.

Unfortunately, the story is bittersweet: Gödel's more famous
*incompleteness* theorem says that there are intense limits to what we
can prove. Simply put, it says that there are plenty of consistent
theories (sets of formulas mutually consistent with one another) which
cannot be finitely axiomitized in first-order logic. A famous example
is Peano arithmetic: there is no first-order theory that allows us to
correctly capture and completely prove every true statement in Peano
math.

