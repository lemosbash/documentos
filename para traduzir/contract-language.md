A Formal Language for Analyzing Contracts 
Nick Szabo
=========================================

#### Preliminary Draft from 2002

The author presents a mini-language for professionals and researchers interested in drafting and analyzing contracts. It is intended for computers to read, too. The main purpose of this language is to specify, as unambiguously and completely and succinctly as possible, common contracts or contractual terms. These include financial contracts, liens and other kinds of security, transfer of ownership, performance of online services, and supply chain workflow.

The following problems may be addressed by the language when interpreted by computer:

-   Accounting and auditing. Sophisticated contracts, including derivatives and combinations, can be specified in our formal language. Then automated or manual accounting rules may be applied to convert transactions completed under the contract to audit trails and ledger entries.

-   Analyze formally specified contracts for flaws in logic, scheduling, opportunities for parties to breach the contract, and conflicts with other contracts one is already committed to.<sup>[[1]](#fn1)</sup>

-   Translate the formal contracts into an existing programming language such as E<sup>[[2]](#fn2)</sup>, and/or cryptographic protocols<sup>[[3]](#fn3)</sup>, for partially self-enforcing and protected execution as [smart contracts](http://szabo.best.vwh.net/idea.html).

-   Some kinds of contracts, especially financial and commodity contracts and their derivatives, can be converted into a decision or game tree that can be analyzed to determine risk, net present value, etc.

The process of designing this language is also a great way to explore the basic nature of contracts (what are the "elements" out of which a wide variety of useful contracts can be drafted?) and their composibility (what rules for composing these atoms rule out impossible contracts?) The language is also a creative tool for thinking up and "sketching" new kinds of contracts. I welcome your participation.

The words in our language follow legal terminology as much as possible – thus for example *performance* means execution to satisfy the terms of the contract (as in the legal field), rather than measured quantities like speed, memory usage, bandwidth, etc. (as programmers use the term). A law degree is not required to use the language, but some familiarity with contract law and the drafting of contracts is recommended. A lawyer who did reasonably well on the analytical and logical sections of the U.S. LSAT or its overseas equivalent, will, I suspect, have better luck drafting contracts in this language than than a programmer whose sole experience lies in traditional procedural language. That is why I call this a *drafting language* not a programming language.

Our language can specify the output of a negotiation (which could be an auction, an exchange, or two parties drafting the contract, or one party drafting and the other agreeing to it, etc. ). It also can define the input to an engine that drives and monitors the transactions that perform the contract:

``` {.snippet}
negotiation --> contract --> performance
```

Performance of the contract, i.e. its reification as a smart contract, can thus be seen as (hypothetical, at this point) execution of a program written in our language. Furthermore, our language incorporates a wide variety of contractual terms, not just abstract monetary terms and their derivatives. These two characteristics make our language very different from special purpose financial contract languages such as<sup>[[4]](#fn4)</sup>. While we use several financial contract examples to introduce our language and demonstrate its flexibility, its scope both in functionality and the kinds of contracts and transaction protocols it can represent is far broader.

Semantics
---------

Each word and phrase in our language has a clear standard meaning. As a result, contracts can be drafted that will be far less subject to disputes over interpretation. On the other hand, the language is not very good at expressing many subjective and ambiguous concepts that are often necessary in contracts. Nor is it any good, in its present state, in referring to jurisdictions or doctrines of law. The language is nevertheless very different from a traditional programming language. Contractual terms are defined in terms of events that trigger their performance. Such events include dates and times, choices made by the parties, observable breaches of contract, and so on.

Our language is not a markup language. It is not about manipulating text for the purposes of drafting contracts. It is not about structuring text, specifying fill-out forms, defining static data formats, or similar tasks of languages such as HTML or XML. For those tasks one should use a markup language, or a text-manipulating programming language (e.g. Perl), not this language. Our language does something very different. It models the dynamics of contract performance – when and under what conditions obligations should be performed.

The words and sentences of the language do not consist of instructions followed down the page from one step to the next. Instead, a contract is read (both by human and computer) by following nested definitions of contractual terms as they expand, and by looking at events in **when** statements and seeing what they trigger. If the drafter does need to explicitly construct a step by step calendar schedule, this can be done by using calendar-driven events or words like **for** and **then**.

The language encourages composition of contracts. Contracts, rights, and obligations can be nested. We call these nested structures *clauses*. Contracts and clauses involve two parties, the *Holder*, from whose point of view we read the contract, and a *Counterparty*. Multi-party agreements can be drafted by composing several two-party contracts.

Example – Futures Contracts
---------------------------

Our first example is a well-known financial contract, the *future*. A future is an obligation on the part of the Holder of the futures contract to purchase a certain amount of a certain commodity in a certain month, and the obligation on the part of the future contract writer, the Counterparty, to deliver these goods. For the purposes of introducing this contract, we give it in the abstract form in which financial analysts usually deal with it, leaving out important details that describe the third parties who act as trusted intermediaries to define "fair bundles" of commodities, and we also leave out many details about the actual delivery.

``` {.snippet}
future(rightA="1 round lot pork bellies",
       rightB="$1,500.00",
       p = "for delivery in July 2002") =

    when withinPeriod(p)
        to Holder rightA   with   to Counterparty rightB
    then terminate
```

Since this language is not yet being computer interpreted, the syntax is designed more for human than computer readability. I will be a bit fast and loose with the syntax, and you can be too. I mostly use tabs instead of brackets {} to structure clauses in a way that seems natural and readable to me – and I hope to you – but might confuse a computer. Feel free to develop your own style.

The top three lines in the contract, in the form **name(parameters)** = tells us that we are defining a *named clause*. The named clause can define an entire contract or just a clause in a larger contract. We can pass the names of other named clauses, lists of events, and other kinds of information that the named clause needs – these are the parameters.

**when withinPeriod(p)** means "when the first calendar or clock event generated during period p". The drafter can elsewhere set how often this regular "clock tick" event occurs. The first such clock tick after the start of the period, in this case the first scheduled delivery day in the month of July, triggers the clause with the brackets {}. More sophisticated schedules are possible, such as those that minimize delivery costs for the Counterparty by delivering to different Holders on different days in July. Fortunately, we can hide these scheduling details within the calendar event and schedule iterator mechanisms, leaving the drafter free from worrying about exactly when markets are open, which days are weekends or holidays or leap days, and the like. The Counterparty's delivery schedule can be negotiated or this detail can be left up to the Counterparty. In the contract above, the constraints on delivery are that it occur within July and only in conjunction with payment from the Holder.

The innermost clause says to swap **rightA** for **rightB**. This clause is split into a Holder right and a Counterparty right. The right clause **Holder rightA** means "Holder has the right to performance of rightA", in this case delivery of the pork bellies. The clause **Counterparty rightB** means "Counterparty has the right to performance of rightB", which here is the payment of 1,500 dollars. **with** indicates a simultaneous swap – the two transactions should occur together, perhaps intermediated by an escrow agent to enforce both the delivery and payment terms.

A **then** statement allows us to proceed step by step. If we had two clauses written thus:

``` {.snippet}
to Holder right1
also to Holder right2
```

They could be performed in any order – right2 might be performed first, or right1 might be, or (most likely) performance on both might proceed at the sime time. Several **when** statements nested together at the same level have any implicit **also** as they can be triggered in any order.

Envision a dancing elf that follows nesting of clauses as they are performed and of events as they are caught. (Programmers call this dancing elf by the boring name "instruction pointer"). There can be more than one dancing elf if there is an **also** or if more than one when statement is triggered while another is active, but usually we only need to think about one at a time.

If we wish to add the constraint that right2 cannot be performed until right1 has been, we use **then**:

``` {.snippet}
to Holder right1
then to Holder right2
```

Traditional programmers will be sorely tempted to fill their contracts with **then** statements, mimicking the style of procedural programming. Do not! Those experienced with drafting contracts know that in some cases such a constraint is clearly appropriate and in some cases it clearly isn't, and it is important when adding constraints to be explicit. Therefore the following statement is illegal, and will be rejected both by the computer and by any right-thinking drafters working without a computer:

``` {.snippet}
# Do not do this!
    to Holder right1
    to Holder right2
```

Finally, we have already seen this kind of clause:

``` {.snippet}
to Holder right1
with to Holder right2
```

This means that right1 and right2 should be performed simultaneously – and either both of them should be performed, or neither of them. In the lingo of computer scientists, it should be an "atomic" transaction.

The **then terminate** at the end of the named clause ensures that all rights and obligations under the contract are terminated once it has been performed. It is not triggered until the one-line body of the future contract has been completed. as well as any outstanding subordinate clauses with their rights and obligations. This clause is implicit at the end of every named clause, but for this one time we make it explicit. This clause is often used explicitly when the drafter wants to ensure the proper termination of non-named clauses nested within a named clause.

Let's now proceed through the futures contract step by step, as clauses are activated and then performed. A normal font indicates the clause is inactive. A **bold** font indicates an active state – the clauses is being performed.

When the parties commit to the contract its first clauses (the clauses at the highest level of indentation) are awoken. In our futures contract we have only one such clause, and so the **when** (but not the clauses nested underneath it) go to in an active state, waiting for the **withinPeriod()** event:

``` {.snippet}
when withinPeriod(p)
    to Holder rightA   with   to Counterparty rightB
then terminate
```

When the calendar has advanced to the end of trading on the last trading day of August, the **withinPeriod(p)** event occurs and the **when** activates the clauses nested at the next level inside. The when itself becomes inactive – it is no longer waiting for an event:

``` {.snippet}
when withinPeriod(p)
    to Holder rightA   with   to Counterparty rightB
then terminate
```

The **then** has cause the **terminate** to wait for the **when** and its subclauses to be performed. Once the swap of rights has been performed, the performed clauses convert to inactivity and the terminate fires:

``` {.snippet}
when withinPeriod(p)
    to Holder rightA   with   to Counterparty rightB
then terminate
```

It is easy to generalize in our language. The generic futures contract looks like this:

``` {.snippet}
future(rightA, rightB, p) =
    when withinPeriod(p)
        to Holder rightA   with   to Counterparty rightB
    then terminate
```

Instead of pork bellies, we can swap any other **rightA** for **rightB**, which can be a wide variety of things beside money. Drafters can specify very general boilerplate and fill in details for specific contracts later.

Example – Option Contract
-------------------------

We now present another kind of financial contract. In this *American option*, the Holder has the right to buy for \$20 (the option strike price) per share one round lot (100 shares) of XYZ Corp on or before the last trading day of August. These kinds of contracts are called "derivatives" because the call option is derived from the underlying right (here a stock).

``` {.snippet}
callOptionAmerican (rightA="1 round lot XYZ Corp.",
                    rightB="$2,000/lot",
                    time="end of trading on last trading day of August") =
    when beforeTime(time)
        when choiceOf(Holder)
            to Holder rightA with to Counterparty rightB
    when afterTime(time)
        terminate
```

Think about that dancing elf again. (When reading or writing in our language it's important to follow these dancing elves. If the elves become annoying, think of some other dynamic character or process for a metaphor that suits). As it goes around it wakes up clauses, making them active, causing them to be performed. Sometimes there can be more than one elf dancing through the code at the same time, for example if more than one when statement is triggered while another is active, but usually we only need to think about one at a time.

The contract starts out with dancing elves in the two top-level clauses:

``` {.snippet}
when beforeTime(time)
    when choiceOf(Holder)
        to Holder rightA with to Counterparty rightB
when afterTime(time)
    terminate
```

These when statements are now waiting for their respective events. Since the events are mutually exclusive (it is first **beforeTime(time)**, then **afterTime(time)**, but never both) we only need to worry about the first to be executed. Note that, unless separated by a **then**, the order of the clauses at the same level is not important. The following code is identical to the code in our example:

``` {.snippet}
when afterTime(time)
    terminate
when beforeTime(time)
    when choiceOf(Holder)
        to Holder rightA with to Counterparty rightB
```

The **beforeTime(time)** is immediately activiated, so that we start with the nested clause(s) at the level immediate below it active as well – in this case, when choiceOf(Holder).

``` {.snippet}
when beforeTime(time)
    when choiceOf(Holder)
        to Holder rightA with to Counterparty rightB
when afterTime(time)
    terminate
```

A series of whenes at the same level in that clause all start waiting for any of them to be triggered. When a when receives an event thrown below it or at it, the nested clause below it becomes active. Then the clauses below it become active and are performed down to the wheness one level below. The whenes at that level go from inactive to active, now waiting for their events to occur.

The **when beforeTime(time)** becomes active when the option itself is first activated, and remains active until **time**. Being active it wakes up the when choiceOf(Holder). This when specifies the event that gives the contract its nature as an option -- the choice of its holder to exercise it or not. If the Holder chooses to do so, the Holder gets the stock (rightA) from the Counterparty (the writer of the call option) in exchange for the money (rightB). The dancing elves then move from the when clause to the swap clause:

``` {.snippet}
when beforeTime(time)
    when choiceOf(Holder)
        to Holder rightA with to Counterparty rightB
when afterTime(time)
    terminate
```

When code gets complicated, we might not be able to easily tell whether the implicit **then terminate** on the last line will get executed. So it's a good idea to **terminate** the contract explicitly when the option expires.

We will see examples where more than one kind of event, and even sequences of events (such as a calender schedule of payment dates) trigger the execution of contractual terms.

Remember that the language does not proceed by default step by step – instead the reader (human or computer) should follow down the nested contract definitions as they expand, and look at events in **when** statements to see what they trigger. Explicit step by step calendar schedules can be built using **for**, **then**, and calendar events.

Here's an example that uses such explicit steps. A *bond* makes a sequence of fixed payments, called *coupons*, on a regular schedule, and then makes a final payment, the *principal*. We don't show here the details of the schedule itself, but in general a schedule could be defined to be any kind of temporal sequence – we could pay out coupons on the last day of every month, on every Japanese holiday, according to the computation of Easter (don't laugh – schedulers of medieval fairs had to face this problem), or however we choose. When implemented the calendar events and schedule iterator will contain a very thorough implementation that solves many of the nasty calendar problems that often appear in transaction processing systems.

**for** iterates through the events in the schedule one by one, with the nested **when** handling them. When it appears after a **for** clause, a **then** advances the iterator one more step.

``` {.snippet}
bond(coupon, principal, schedule) =
    for schedule
        when withinPeriod(schedule.next)
            to Holder coupon
    then
    when withinPeriod(schedule.next)
        to Holder principal
```

Next we sketch a contract to sell a car on credit. Being a sketch rather than a full-fledged design, this is an oversimplified or "toy" example – we leave out fees, any references to a related insurance contract, warrantees, waivers, etc. We also for simplicity have the bank (here the Holder) be the same as the car dealer. Finally, we don't show here any lien on the car to secure the loan. We will make a stab at a lien-like mechanism in an example below.

``` {.snippet}
loanPayments(payment, schedule) =
    for schedule
        when withinPeriod(schedule.next)
            payment
carPurchase(car, downPayment, monthlyPayment, schedule)  =
    to Counterparty getTitle(car)   with   to Holder downPayment
    then to Holder loanPayment(monthlyPayment, schedule)
```

If we want to allow prepayment, our schedule would contain single times rather than periods with a begin time and an end time. And our loan payments would look as follows:

``` {.snippet}
loanPayments(payment, schedule) =
    for schedule
           when beforeTime(schedule.next)
                 payment
```

We could compute the above payment from other information we are more likely to see in the contract, but this involves just normal programming. We can use a function to perform the computation in the first place:

``` {.snippet}
loanPayments(principal, interest, schedule) =
        constant payment.amount = computeInterest(principal, schedule, interest)
                # normal computational function goes here
        then for schedule
                when beforeTime(schedule.next)
                        payment
```

"=" sets, for all time (thus **constant**), the number **payment.amount** to the value returned by the function **computeInterest**. We could also do more complicated things – deduct interest for prepayment, or conversely add prepayment penalties, or a wide variety of other conditions.

We can also structure the car purchase contract so that the new owner doesn't get the title until after the downPayment has been received:

``` {.snippet}
carPurchase(car, downPayment, monthlyPayment, schedule)  =
    to Holder downPayment
    then to Counterparty getTitle(car)
    then to Holder loanPayments(monthlyPayment, schedule)
```

Damage Clauses
--------------

Let's use our language to analyze some important advances in the history of economic institutions. Genoa was an independent and rather libertarian (for its time) city heavily involved in the Mediterranean sea trade. During its height in the twelth through fifteenth centuries it developed many commercial innovations, including two we will examine here, the "dry exchange" loan and pooled-risk insurance.

Here is a clause from a contract made in Genoa on June 23 in 1271 A.D. A man is co-signing an obligation being made by his son:

> Wherefore and for which we promise, both of us [liable] for the whole amount, to give and to pay to you or to your accredited messenger 53 gold hyperpers, good and of correct weight, in Romania [Byzantium], by the Kalends of September. If, however, we do not give you these [hyperpers] within the said time limit, [we promise] for each of the said hyperpers 11 shillings Genoese in Genoa whenever you wish. Otherwise we promise, both of us [liable] for the whole amount, to give to you, making the stipulation, the penalty of the double of the said amount, the aforesaid [conditions] remaining as settled. And we pledge to you as security for the aforesaid [promises] all our goods, existing and future...<sup>[[5]](#fn5)</sup>

Now, this is a very clever contract, called by scholars a "dry exchange". The Catholic Church forbad the charging of interest, so a loan contract that came right out and charged interest would be unenforceable and expose the drafters to further Church sanctions. But both long-distance exchange (making a trade at a distant market at a later date -- often via a sea voyage, thus a "wet" exchange) and currency exchange were quite legal, enforceable, and common. The above contract combined these two, along with damage clauses, in a clever way. None of the above parties had any intention of travelling to Byzantium, or even outside Genoa, to perform on this contract. Its logic can be analyzed as follows (the Holder is the creditor). We've added the statements "in (geographic location)", **security**, and **foreclose** to highlight important aspects of this contract. The latter sells enough of the goods at auction to satisfy penalty (if there was not enough to satisfy the creditors there was a bankruptcy procedure to fairly allocate the remain security among the creditors, but this is not shown):

``` {.snippet}
    counterpartySecurity =  pledge(allGoods(Counterparty))
    also cosignerSecurity = pledge(allGoods(co-signer))
    then
payment1() =
    when beforeTime("Kalends of September 1275")
        to Holder in Byzantium "53 hyperpers"
        terminate
payment2() =
    when breachedPerformance(payment1)
        to Holder in Genoa "53*11 = 583 shillings"
        terminate
payment3() =
    when breachedPerformance(payment2)
        to Holder in Genoa "2*583 shillings"
        terminate
payment3() =
    when breachedPerformance(payment3)
        when choiceOf(Holder)
            to Holder in Genoa foreclose(counterpartySecurity, penalty)
            terminate
        when choiceOf(Holder)
            to Holder in Genoa foreclose(cosignerSecurity, penalty)
            terminate
        continue
```

Neither party expected payment1 to be performed. The amounts of hyperpers and shillings probably reflected accurately the exchange rate between the two coins at the time – there's no sense being too obvious. But it would have been far too expensive to travel to Byzantium just to do this exchange. So in fact both parties expected payment2, a fake damage clause, to normally be performed. If it wasn't, then we have two real damage clauses – the somewhat reasonable "penalty of the double" and the lethal "all our goods, existing and future". Another interpretation of the latter clause is that it would only refer to a value of goods up to the double penalty amount, but the goods could be chosen from among all the goods of the debtor and the co-signer. Certainly a modern court would consider the interpretation I have placed on it in our language to be unconscionable, therefore unenforceable.

Above the part we quoted, the contract does not specify how much the original loan amount was – the debtors simply acknowledge that they have received from the creditor "a number of Genoese deniers" and then promise other currencies in return as quoted above. So a Church investigator couldn't prove just from reading the contract that any interest was charged. As for the Genoese judge arbitrating a dispute, he would likely be in favor of loans at interest, and happy to wink and nod and interpret the contract literally.

Modern derivatives traders do this all the time, creating synthetic assets or combinations that mimic the financial functionality of some other contract while avoiding its legal limitations. Our language is ideal for drafting and analyzing such contracts.

Insurance
---------

The earliest risk-pooling insurance contracts were structured similarly to, and enforced under the same legal principals as, loans. Indeed, let's start with a simple loan for a purchase of goods with no interest where the Holder (the creditor) can call in the loan any time between days **t1** and **t2**:

``` {.snippet}
loan(goods, principal, penalty, t1, t2) =
    counterpartySecurity =  pledge(allGoods(Counterparty))
    with to Counterparty getTitle(goods)
    loanPayment(principal, t1, t2)
    with when breachedPerformance(loanPayment)
        to Holder foreclose(counterpartySecurity, penalty)
loanPayment(principal, t1, t2) =
    when withinPeriod(t1,t2)
        when choiceOf(Holder)
            to Holder principal
```

Let's add to our language a **safeArrival(goods)** event – the event that a ship carrying **goods** arrives safely in port and that **goods** are offloaded and accounted for. Now by adding to this loan contract only one extra line, whening **safeArrival()**, and slightly modifying a few others we can transform it into a marine insurance contract. The insured is the Holder, the insurer is the Counterparty. For this simple version damages are paid in a fixed amount (**principal**) if **safeArrival** does not occur:

``` {.snippet}
insureGoods(goodsPremium, principal, penalty, t1, t2, goodsInsured) =
    counterpartySecurity =  pledge(allGoods(Counterparty))
    with to Counterparty getTitle(goodsPremium)
    insurancePayment(goodsInsured, principal, t1, t2)
    with when breachedPerformance(insurancePayment)
        to Holder foreclose(counterpartySecurity, penalty)
insurancePayment(goodsInsured, principal, t1, t2) =
    when safeArrival(goodsInsured) terminate
    when withinPeriod(t1,t2)
        when choiceOf(Holder)
            to Holder principal
```

Here's an example of an early insurance contract of this kind – again from Genoa, the birthplace of modern commercial institutions. For the first time we see a pool of insurers – not one but many counterparties, each pledging their entire property as security. Often these were feudal lords with large landholdings, so the value that could be brought to bear to back these insurance contracts was vast. This is how Lloyds Names still work today. Since several Names back a single contract (e.g. covering a single shipment of goods, as here), each Name puts only a tiny fraction of their estate at risk in that voyage. An insurance exchange like Lloyds allows the agents of goods owners, shippers, and Names to meet and mass-produce these kinds of contracts.

> ...Geri, [son] of the late Ser Lapo of Florence, Simone Guascone, [9 more Names listed], each of them [liable] for the amount written below, have acknowledged and in truth have declared to me, notary undersigned, as a public official [holding] a public office, making the stipulation and receiving in the name and stead of Federico Vivaldi, citizen of Genoa, that they have bought, have had, and have received from him a certain amount of goods of the said Frederico...And for these goods and in consideration of their price each of them has promised to give and to pay to said Frederico or to his accredited messenger: [from] the said Geri, 150 gold florins, the said Simone, 50 florins, [100 florins each from the other Names] within the next five months from now. Otherwise they have promised to give and to pay to the said Frederico the penalty of the double of it and of the entire amount to which and to the extent of which [this agreement] is violated or is not observed as above, together with restitution of all losses, unrealized profits, and expenses which might be incurred because of it in court or outside – the aforesaid remaining as settled, and under hypothecation and pledge of their goods and [the goods] of any one of them, existing and future.
>
> [The above is binding] with the exception and special reservation that if the amount of goods, property, and merchandise which was loaded or is to be loaded by Frederico Imperiale or by another in his behalf for the account of the said Frederico Vivaldi in Aigues-Mortes – to be transported to Ayassoluk and Rhodes or to either of these two localities in a certain ship...and which departed from Aigues-Mortes or is about to depart in order to sail to aforesaid regions – is brought and unloaded in the said localities of Ayasoluk and Rhodes or in either of them, in safety, then and in such a case the present intrument is cancelled, void, and of no value and pro rata. And be it understood that such a risk begins when the said ship departs and sets sail from Aigues-Mortes, and it remains and lasts, while the captain goes, stays [in port], sails, loads and unloads, from the said locality of Aigues-Mortres up to the said localities of Ayassoluk and Rhodes, in whatever manner and way he wishes, until said amount of goods, property, and merchandise has been brought and unloaded in Ayassoluk and Rhodes or in either of these two localities in safety, and pro rata. Let the present instrument also be cancelled if the said Frederico refrains from asking payments of the aforesaid amounts of money for the space of one year after the time or the time limit has elapsed for asking or obtaining their payment....Done as above, September 15th, around nones. [1393 A.D.]<sup>[[5]](#fn5)</sup>

Ignoring the pro rata language, the specific definition of the risks that prevent generation or not of the safeArrival() event, and ignoring the multiple Names (i.e. treating them as one Counterparty), the contract can be modelled by the insurance contract we drafted above with its parameters filled in as follows, and with Frederico Vivaldi as the insured (the Holder):

``` {.snippet}
insureGoods(goodsPremium="a certain amount of goods",
      principal="100 fl + 50 fl + 7*100 fl",
      penalty=2*principal,
      t1="5 months from now",
      t2="1 year after [legal] time limit has expired"
      goodsInsured="that amount of goods, property,
          and merchandise which was loaded")
```

This contract was still, legally speaking, a loan. This had at least two interesting consequences on what we now call the insurance premium. Firstly, the premium was treated as goods purchases by the insurer on credit. Secondly, even at this late date, contracts were coy about the actual value of such goods. Leaving the value of those goods unspecified made usury difficult to prove in this "loan".

Rules: Logically Combined and Superimposed Events
-------------------------------------------------

Events in a **when** clause can be combined in logical conditions which must evaluate to true in order to trigger the subclause. This can be used to model conditional clauses in contracts, and, more broadly, procedural and substantive rules of law. When constructing rules we call the primitive events *elements*. For example, here is, roughly following the Restatement(Second) of Contracts, a legal rule for promissory estoppel:

``` {.snippet}
when
    "there is a promise"
    P "has relied on that promise"
    D "should reasonably expect P would rely on that promise"  and
    "injustice can only be remedied by enforcing the promise"
then
    "the promise will be enforced"
else
    "the promise will not be enforced"
```

We include a gratuitous "then" here for readability. Computer programmers should note that we are following a shorthand here used by lawyers – we write the logical phrase **(A and B and C and D)** as **(A B C and D)**. When mixing and and or, write out the complete logic, and use parentheses where appropriate.

The rule elements, such as "there is a promise", exist in a *superimposed* state. By default, the logic is ignorant of the facts, and each element is *genuinely at issue*. As a result, "there is a promise" and the other elements of the above rule are both true and false, at the same time. (Those familiar with quantum mechanics or legal reasoning know what I'm talking about here). In the initial state, where every element is genuinely at issue, nontrivial rules will always evaluate to both true and false. Thus, *both* "promise will be enforced" and "promise will not be enforced" clauses will be triggered. When the clauses are incompatible, as these appear to be by their label, it is up to the implementor of the clause to deal with this properly. In this case, such a clause should only be treated as *advisory* until all material elements have been decided – i.e., they are no longer genuinely at issue, at which point the rule can be used to make a *decision*, i.e. trigger a single clause which takes a consistent action. A future version of this document will describe how to resolve elements genuinely at issue into elements not genuinely at issue, and thus decide on a single outcome or course of action. It will also describe how to deal with advisory clauses; for example, to analyze which elements are most favorable to one outcome or another. Finally, another future feature will include elements that cover a range of numerical values, rather than just true or false, and a formalized "balancing test" that determines outcomes based on underlying numerical estimates.

Property – Estates and Future Interests
---------------------------------------

Our rule language is ideal for specifying estates and future interests in real estate deeds. One can also apply these patterns to other kinds of property where appropriate. Here are some examples:

Lease for Term: (n.b. – Grantor = self). This is an old common law lease that actually transfers title for a certain period of time.

``` {.snippet}
leaseWithTerm(Property, Lessee, Start, Term) =
    when afterTime(Start) to Lessee Property
    then when afterTime(Start+Term) to Grantor Property
```

Life Estate with Reverter: (n.b. – Grantor = self)

``` {.snippet}
lifeEstateReverter(Property, Grantee) =
    to Grantee Property
    then when afterDeath(Grantee)
        to Grantor Property
```

Assignable Reverter for Lease. To make future interests assignable, define them separately. Note to make the Grantor explicit. (As usual we are looking at things from the obligor's side):

``` {.snippet}
Reverter (Grantor, Grantee, Property) =
    when afterDeath(Grantee)
        to Grantor Property
```

We can now redefine the Life Estate with Reverter in terms of the separately defined future interest:

``` {.snippet}
lifeEstateReverter(Property, Grantee, Reverter) =
    to Grantee Property
    then Reverter(Grantor, Grantee, Property)
```

Life Estate with Remainder. The only difference here is that the property is remaindered to a third party instead of reverting to the grantor.

``` {.snippet}
lifeEstateRemainder(Property, Grantee, Remainderman) =
    to Grantee Property
    then when afterDeath(Grantee) to Remainderman Property
```

Fee Simple Determinable. The **Condition** can be any verifiable event or change of state of the property or its title. A common real estate condition, for example, is "used for commercial purposes" – i.e., a restriction that the property may not be used for commercial purposes, otherwise the grantee is penalized by losing title to the grantor.

``` {.snippet}
feeSimpleDeterminable(Property, Grantee, Condition) =
    to Grantee Property
    then when Condtion(Property) to Grantor Property
```

Fee Simple Subject to Executory Limitation. Same as Fee Simple Determinable, except the property gets remaindered to a third party instead of revertin to the grantor.

``` {.snippet}
feeSimpleSubjectToExecutoryLimitation(Property, Grantee, Condition, Remainderman) =
    to Grantee Property
    then when Condtion(Property) to Remainderman Property
```

Fee Simple Subject to Condition Subsequent. Here title is not automatically transferred upon occurence of the condtion. Instead the Grantor must do some affirmative and verifiable act (in this example "entering" the property), to reclaim the title.

``` {.snippet}
feeSimpleSubjectToConditionSubsequent(Property, Grantee, Condition, Enters) =
    to Grantee Property
    then when Condtion(Property)
        when Enters(Grantor, Property) to Grantor Property
```

Some More Advanced Examples
---------------------------

In this section we will look at ways to construct multiparty agreements, distinguish ambient from thrown events, and examine a number of other more advanced features of or ways to use our language.

We will complete the life cycle of the American option we drafted above by "writing" the option – creating it from an underlying security **rightA** and selling it for **rightX**. Here the Holder (same party as the Holder above, in this case the person who will buy the written option) first verifies that the Counterparty indeed holds the underlying security (**rightA**) with Broker. The Broker is trusted by the Holder to make sure Counterparty continues to hold the security until the option is exercised or expired. The contract between the Broker and Holder is **escrowRight()**.)

Since Counterparty, the option writer, is not paying anything up front for an option on **rightB**, this right does not need to be escrowed.

``` {.snippet}
escrowRight(right, escrow, newHolder, currentHolder,
            newHolderReleaseEvents, currentHolderReleaseEvents) =
    to escrow right
    then
    when (holderReleaseEvents)
        to rightHolder right then terminate
    when (counterpartyReleaseEvents)
        to currentHolder right then terminate


writeCallOptionAmerican(rightA, rightB, rightX, time) =
    escrowRight(rightA, Holder, Counterparty,
            (optionExercised), (optionExpired))
    then
            to Counterparty callOptionAmercian(rightA, rightB, time)
            with to Holder rightX
```

We now redraft the option itself to take advantage of the escrow. **rightA** is transferred to Holder by the **throw** upon excercise, or back to Counterparty if the option expires.

``` {.snippet}
callOptionAmerican(escrowRight, rightB, time) =
    when beforeTime(time)
        when choiceOf(Holder)
            { throw optionExercised at escrowRight }
            with to Counterparty rightB
            then terminate
    when afterTime(time)
        throw optionExpired at escrowRight
        then terminate
```

We can think of events as coming in two kinds. The first, *ambient* events, occur spontaneously in the environment, or are generated by an entity external to our specification such as a user or a schedule. The second, *thrown* events, are events we explicitly throw as above.

In this manual I have expressed contract clauses in terms of rights. Often contract language is expressed in terms of obligations, which can be done as a mirror image – **to Holder right** is the same as **from Counterparty obligation** and vice versa. Use **from** to distinguish an obligation.

Hooking Up Machines
-------------------

Further showing the flexibility of our language we can add sensors and effectors, adding "smarts" to our contracts and augmenting legal enforcement with technological constraints.

First we draft a specification for the contract-like bevavior of a vending machine:

``` {.snippet}
sellCandy(candyPrice = $0.90) =
    variable moneyAmount = $0.00
    then
        # coins also fall into a temporary till tempTill
    when (nickel)
        add(moneyAmount, $0.05)
    when (dime)
        add(moneyAmount, $0.10)
    when (quarter)
        add(moneyAmount, $0.25)
    when (moneyReturn)
        dropCoins(tempTill, returnTill)
        with moneyAmount = $0.00
    when threshold(moneyAmount, candyPrice)
        when (nickel | dime | quarter)
            redirectNewCoinsTo(returnTill)
        also display("ready to dispense -- please select candy")
        then when (candySelection)
          dropCandy(candyRacks, candySelection)
          with dropCoins(tempTill, permTill)
          with moneyAmount = $0.00
    continue
```

We have here introduced a new language feature, a state variable. Our state variable **moneyAmount** generates an event upon surpassing the candy price threshold of \$0.90. Note that nickels, dimes, etc. are actual physical objects that the sensors (generating events "nickel", "dime", etc.) detect and treat separately – they are not merely abstract amounts of money.

State variables can be troublesome, and should be avoided unless utterly necessary as here. This one is relatively harmless because the coin slot tends to force the coins to come in one at a time, so that no two clauses are trying to change the state variable at the same time. Even if they were, the addition operation is what mathematicians call "commutative", meaning it doesn't matter what order it's done in. But if the operation on the state variable were more complicated or involved certain other kinds of operations, we wouldn't know whether it was commutative. The order in which events occured and changed the state variable might matter very much, and we could get into big trouble. So avoid state variables whenever possible.

To simplify things, we've left out making change – our machine has to have one of those signs you sometimes see, "exact change only". If the customer puts in a coin that pushes the amount from, say, \$.80 to \$1.05 – too bad, the machine eats it. If the customer puts in \$0.90 (or more) and then adds more coins, however, the machine automatically returns the extra coins. The machine will also return whatever has been put in the till, if the customer changes her mind and decides not to buy the candy. Exercise for the reader: verify for yourself that the above behavior descriptions are correct as the code is written.

**RedirectNewCoinsTo(returnTill)** causes any further coins to drop into the return chute instead of onto the sensor that triggers the above events. The reader must here imagine what the mechanism looks like, as part of the behavior is "encoded" in its mechaninism rather than explitly in this statement.

Think of the nested contracts and rights as an upside-down tree – a hierarchy of nested clauses. Events propagate from up from the "leaves" of the tree towards the "root" at the top. They are caught by the first **when event** they encounter for that event. In this case, once we enter the **when threshold()** clause, the **when (nickel | dime | quarter)** clause overrides the **when(nickel)** and so on clauses above them.

Like a perpetuity, our vending machine has no scheduled time or condition where it stops performing – therefore we have a **continue** statement to overrid the implicit **then terminate** on the last line.

Alas, neither I nor real world candy machine makers have any code to solve the case where the candy gets stuck in the machine.

The above is a transcription of machine behavior. Now we make it even more like a contract. Here we incorporate the customer and his choices, which implicitly generated the coin events in the code above – here the coins are rights of the Holder. Thinking more about the party, rather than the machine, allows us to recognize that at each step the customer wants feedback on how much money they have put in, thus **to Counterparty display(moneyAmount)**. This display is done by the Holder (the vending machine as an agent of the vendor) as a right of the Counterparty (the customer). To enable better customer choice, we add a new construct to our language: **choiceOf(agent, right)** which allows the customer multiple choice, based on which right they wish to transfer to the agent's counterparty (here the vending machine, the Holder).

``` {.snippet}
sellCandy(candyPrice = $0.90) =
    variable moneyAmount = $0.00
    then
        # coins also fall into a temporary till tempTill
    when choiceOf(Counterparty, nickel)
        to TempTill nickel
        then to Counterparty add(moneyAmount, $0.05)
        then to Counterparty display(moneyAmount)
    when choiceOf(Counterparty, dime)
        to TempTill dime
        then to Counterparty add(moneyAmount, $0.10)
        then to Counterparty display(moneyAmount)
    when choiceOf(Counterparty, quarter)
        to TempTill quarter
        then to Counterparty add(moneyAmount, $0.25)
        then to Counterparty display(moneyAmount)
    when choiceOf(Counterparty, moneyReturn)
        to Counterparty dropCoins(tempTill, returnTill)
        with moneyAmount = $0.00
        then to Counterparty display(moneyAmount)
    when threshold(moneyAmount, candyPrice)
        to Holder (nickel | dime | quarter)
            to CounterParty redirectNewCoinsTo(returnTill)
        also display("ready to dispense -- please select candy")
        then when (candySelection)
            to Counterparty dropCandy(candyRacks, candySelection)
            with to PermanentTill dropCoins(TempTill)
            with moneyAmount = $0.00
    continue
```

How is it that we have specified the behavior of a vending machine in a language designed for drafting contracts? Can nickels, dimes, quarters, and operations like dropping coins from one till to another be thought of as rights and obligations? I think so. They are not *legal* rights and obligations, to be sure. There is no explicit contract between the vendor and candy machine customers, and if their were it would probably waive liability for violating most of the clauses in our code. What this code describes is the logical and typical behavior of a vending machine. It also reifies the implicit understanding most customers have when using a vending machine. Thus it models a contract-like "meeting of the minds" between the customer and the vendor that is mediated by the machine.

Here is a stab at formally describing the hypothetical ["auto repo auto"](http://szabo.best.vwh.net/idea.html). The car is controlled by a [proplet](http://szabo.best.vwh.net/proplets.html) and the proplet looks to [property titles](http://nakamotoinstitute.org/secure-property-titles.html) to determine ownership authority. The proplet lets only the titled owner enter and drive the car. "Holder" is the bank that made the loan and "Counterparty" is the new owner. As above we ignore the car dealer; the bank originally owning the car. This example highlights the ability of the language to very succinctly describe contracts but also its inability to describe the actual security that will enforce the contract. There is of course a lot missing here, including the items missing from the above car loan contract. From a smart contracts viewpoint, the biggest thing missing is that there is nothing to motivate the "Holder getTitle(car)" in the last **when**, nor any way specified here to enforce it. And of course all connection between ownership and authority to enter, start, and drive the car is here implicit – the actual proplet behavior in this regard would have to account for safety, emergency use, etc.

For all that work-up, we've only actually added one clause to our car purchase contract above. The clause forfeits the title and is structured much like the damage clauses we have seen. A **breachedPerformance()** event is generated if it is detected (by the Holder, a third party auditor, or the proplet itself) that the Countparty failed to make a payment according to the schedule specified by **loan**.

``` {.snippet}
loan(payment, schedule) =
    for schedule
        when withinPeriod(schedule.next)
            payment
carPurchase(car, downPayment, monthlyPayment, schedule)  =
    to Counterparty getTitle(car)  with  to Holder downPayment
    then
    to Holder loan(monthlyPayment, schedule)
    when breachedPerformance(loan)
        to Holder getTitle(car)
```

Technical Note – A Computer-Readable Syntax
-------------------------------------------

Here is a formal specification of the language's grammar in "Backus-Naur Form"(BNF). The specification is for a planned computer-readable version of the language and there are a few minor differences, such as the use of brackets {} instead of tabs to denote nesting. BNF is used to define what linguists call "context-free grammars". It is also used, as here, to define the syntax of languages that computers can also interpret and execute. I also include some more discussion of the meanings of the words and structures, especially how the computer might interpret them. As you can see, this is an evolving language, a work in progress with many unresolved issues. Your suggestions for changing or adding more kinds of contractual terms to our language are quite welcome.

``` {.snippet}

agent = Holder | Counterparty
    ## makes contract look different on each side

period = (startTime,finishTime)
# period is the window within which the performance must be
# executed, e.g. a European option must be exercised
# between the start to finish of the business day on which
# it expires.

[for periodIterator "{" ...periodIterator.next... "}"] [then ... periodIterator.next...]*
# rights performed in temporal order.
# sequence of periods used as inputs to sequence of withinPeriod(p) events.
# can this be more general, an iterator of events?  but periods have
# a natural order while other kinds of events can occur in any order.
# each periodIterator.next generates an event which is caught by
# the next tempral event (withinPeriod() if the iterator generates
# periods, aftertime() or beforeTime() if it generates times)
# *down* from the *for*. The implicit "throw" occurs at periodIterator.next
# so we can still view it, just barely, as propagating up from
# the "throw".
# (this is different from the normal event semantics where throws
# propagate *up* the parse tree) -- should I change it to *up*
# and rewrite the schedule iterator code above?  but it confuses
# me &  perhaps the comuter to put it *outside* the loop.

event = choiceOf(agent) | withinPeriod(period) |
    performed(right)  | breachedPerformance(right) |
    threshold(amount,threshold) | afterTime(time) | beforeTime(time)

right = throw event at [ contract | right] | passEvent([right | contract])
# generates an event to be caught by the specified contract or right.
# if contract or right is not specified, the first parent when(event)
# is triggered.

# just use when withinPeriod(whenWritten,time)
# right = doBefore time { right }

# semantically equivalent:
# Holder right = O Counterparty obligation

right = getTitle(property)
# transfer property title from obligor to obligee
# see http://nakamotoinstitute.org/secure-property-titles/

right = null

right = [ when event "{" right "}" ]*
    ## envision an "instruction pointer" that
    ## follows nesting and events as they occur.
    ## there can be more than one instruction pointer
    ## if there is an "also" or two events occur at
    ## the same time, but usually we only need to think
    ## about one.
    ## a clause is either active or  inactive.
    ## when a when clause is active, it is waiting for
    ## an event to occur.
    ## when the instruction pointer a nested when,
    ## the when goes from inactive to waiting.
    ## A series of when's at the same level
    ## all wait for any of them to be triggered.
    ## When it receives an event  thrown below
    ## it or at it, it becomes active.  Then
    ## the clauses below it become active up
    ## to the when's one level below.  The
    ## whenes at that level go from inactive
    ## to waiting.
    ## alternative design being considered: continue where left off
    ## unless an explicit "terminate" in the when

right = functionPerformance
    ## functional specification of a specific
    ## service goes here
    ## function sig, pre-, post-conditions

obligation = throw event at [contract | right]
  # generates an event to be caught by the specified contract or right.
  # if contract or right is not specified, the first parent when
  # is triggered.

  # semantically equivalent: Holder obligation = Counterparty right

obligation = surrenderTitle(property)
  # transfer property title from obligor to right holder
  # see http://nakamotoinstitute.org/secure-property-titles/

obligation = null

obligation = [ when event "{" obligation "}" ]*
    ## alternative design being considered: continue where left off
    ## unless an explicit "terminate" in the when

obligation = functionPerformance
  ## functional specification of a specific
  ## service goes here
  ## function sig, pre-, post-conditions

contract = agent [right | obligation] ["with" agent [right | obligation]]*
    ## "with" allows composing Holder and Counterparty
    ## rights vs. each other

contract = [ when event "{" contract "}" ]*
```

Basic Fucntions
---------------

``` {.snippet}

doOn(right, period) =
    when withinPeriod(p) { right }
# must perform "right" within (period)
# = zero-coupon bond = coupon
#

doOnDemand(right) =
    when choiceOf(Holder) { right }
# must perform "right" anytime/on demand
#

doOn(contract, period) =
    when withinPeriod(p) { contract }
# must perform "contract" within (period)
#

doOnDemand(contract) =
    when choiceOf(Holder) { contract }
# must perform "contract" anytime/on demand
#
```

More Examples
-------------

``` {.snippet}
future(rightA, rightB, p) =
    Holder doOn(rightA, p)   with   Counterparty doOn(rightB, p)
#
# [additional constraint p=p is not mere algebraic composition]
#
callOptionAmerican(rightA, rightB, t) =
    when withinPeriod(whenWritten, t)
        when choiceOf(Holder)
          Holder rightA  with  Counterparty rightB
#
callOptionEuro(rightA, rightB, p) =
      when choiceOf(Holder)
          Holder doOn(rightA, p)
              with Counterparty doOn(rightB, p))
#
putOptionAmerican(rightA,rightB,t) =
    when withinPeriod(whenWritten, t)
        when choiceOf(Holder)
                Holder rightA   with   Counterparty rightB

# event semantics need to store the choiceOf even
# by waiting for the next when.
putOptionEuro(rightA,rightB,p) =
    when choiceOf(Holder)
        Holder doOn(rightB, p)
            with Counterparty doOn(rightA, p))
#
note(right) = demandDeposit(right) =
    Holder doOnDemand(right)
# [distinction of bearer vs. account holder has not
# been introduced]
#
zeroCouponBond(right,p) = doOn(right, p)
#
callableZeroCouponBond(right,p) =
    when choiceOf(Holder) { right }
    when withinPeriod(p) { right }
#
bond(coupon, principal, schedule) =
    for schedule {
        doOn(coupon, schedule.next)
    } then
    doOn(principal, schedule.next)
bond(coupon, principal, schedule) =
    for schedule {
      doOn(coupon, schedule.next)
    } then
    doOn(principal, schedule.next)
```

Frequently Asked Questions
--------------------------

**Q: There are already languages for specifying financial contracts<sup>[[3]](#fn3)</sup>, what is the novelty here?**

A: This the first specification language to generalize contractual structures to any kind of exclusive rights, not just money. This is also the first language that incorporates the dynamic nature of many contracts, (their dependence on time or events) in a succinct, complete, and potentially executable manner. Surprisingly, this often makes the specification more not less succinct.

**Where's the money?**

A: This language is targeted toward an economy of distributed software and devices performing services for each other. A money economy can be constructed out of a barter economy but not vice versa. Real online money is far more subtle than a mere shared variable (or even the specification of "bank notes" in this language). Money is just one kind of fungible exclusive right, and the structure of financial contracts are generalized by converting money terms into any fungible exclusive right.

**Q: What assumptions are you making?**

A: This is the most important question to ask of any novel scheme! I have identified at least the following:

1.  I'm assuming away, for the moment, issues of the protection and enforcement of performance, which I've addressed elsewhere (http://szabo.vwh.best.com/ has many essays that focus on this topic). An eventual goal is to create protocols to enforce the language's atoms and then to compose these atoms maintaining the enforceability.
2.  Two specific enforcement assumptions – there exists a secure, agreed-upon time source, and the occurence of other defined events can be agreed upon by the parties and/or audited by third parties.
3.  I am assuming some kind of atomicity for each right atom performance – for example, when an event triggers a "when", a functionPerformance in another thread is gracefully either rolled back or completed.
4.  There are only two parties to the contract, the Holder and Counterparty.
5.  A contract comes in two mirror forms, one of which can be inferred from the other. A party always sees himself as the Holder. Obligations of the Holder can always be expressed in and inferred from rights of the Counterparty, and vice versa.
6.  I'm probably making other assumptions I haven't discovered yet – if you find any please let me know!

**Q: What are some problems with this language you'd like to see solved?**

A: Implementations that satisfy the above assumptions for the particular language atoms, and also for compositions of the atoms. (Of course, various protocols in the "financial cryptography" field, in my own proposals, in the E language, etc. provide many valuable building blocks for such solutions).

References
----------

1.  There are many instances of deals gone sour at the last minute when the salesfolk check with legal and discover that the deal is not possible due to a clause in another contract – for example a promise not to sell to a customer's competitors in an industry for a certain period of time. Worse, such a conflict might not be discovered until the contraditory commitment has been made. [↩](#ref1)

2.  [The E programming language](http://www.erights.org/) [↩](#ref2)

3.  *Proceedings of the Financial Cryptography Conferences*, Springer-Verlag [↩](#ref3)

4.  [Composing contracts: an adventure in financial engineering](http://citeseer.nj.nec.com/jones00composing.html) – A different kind of language for specifying financial contracts in order to compute their risk and value. [↩](#ref4)

5.  Lopez and Raymond, *Medieval Trade in the Mediterranean World: Illustrative Documents*, Columbia University Press 2001. [↩](#ref5-1) [↩](#ref5-2)

---

Please send your comments to nszabo (at) law (dot) gwu (dot) edu

Copyright © 2002 by Nick Szabo
 Preliminary draft -- Redistribution only with written permission of author.

---

*Editor's note: Some links may be broken.*
