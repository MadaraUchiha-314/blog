# Non-Turning Computation ?
Whats 19*91 ? Anyone who knows a bit about multiplication can tell that its 1729 (this also happens to be the [Hardy-Ramanujan number]). We humans do a lot of such computations every day.
> 50% discount on orders above 300 on Zomato.

Our brain is wired to compute the price of an item listed with the given constraint and find out the best choice.

Let's try to answer a more difficult question.
Is the below number prime ?
```
759,301,153,995,587,786,295,940,234,337,835,566,939,818,242,933,338,764,992,626,636,151,840,060,018,287,495,175,071,640,381,085,598,621,485,324,153,512,723,306,029,347,944,398,765,220,195,985,610,285,655,148,360,429,689,570,446,991,855,700,752,025,872,155,835,192,912,833,873,171,105,200,576,475,258,000,980,674,946,417,119,578,990,569,901,912,595,362,864,383,826,160,631,207,564,428,245,219,788,870,979,717,427,639,960,592,692,739,718,067,446,610,883,944,074,742,923,064,162,539,957,869,762,709,941,009,094,660,335,117,010,525,554,194,337,335,483,429,829,570,293,294,530,317,689,382,387,199,365,645,018,871,418,582,530,303,530,942,736,407,739,032,184,541,272,768,141,863,716,754,690,767,416,741,919,411,494,261,600,447,044,209,989,519,846,225,463,497,405,337,399,025,250,428,219,525,640,436,125,838,929,594,312,133,227
```

We quickly realize that the problem has escalated and we need more powerful methods than our handy mobile app calculator to tell the answer. Although there are some fancy algorithms to [test the primality] of a number, but most of them require some amount of non-trivial computing.

Humans soon found the need for inventing devices to help with such hard arithmetic operations and hence came to be the [calculator].

<table>
    <tr>
        <th>The first Calculator</th>
        <th>A modern iPhone calc app<th/>
    </tr>
    <tr>
        <td>
            <img src="https://edtechmagazine.com/k12/sites/default/files/uploads/1623_calculatingclock.jpg" />
        </td>
        <td>
            <img
                src="https://www.imore.com/sites/imore.com/files/styles/xlarge/public/field/image/2017/05/calculator-app-scientific-mode-landscape-portrait.jpg"
                height="400px"
                width="550px"
            />
        </td>
    </tr>
</table>

The earliest of these devices were just able to multiply and add numbers. Clearly we have come a long way. It's interesting to look at how computers add numbers today. This is the circuit for adding two bits :

<div align="center" >
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Half_Adder.svg/440px-Half_Adder.svg.png" />
</div>

Here **S** is the sum and **C** is the carry bit. Remember that the only numbers you can add in this are 0 and 1 and therefore can add upto only 4 combinations :

|Inputs|||Outputs|
|---|---|---|---|
|A|B|Carry|Sum|
|0|0|0|0|
|1|0|0|1|
|0|1|0|1|
|1|1|1|0|

Our computers have so many of such circuits to add bits very very very fast which enables us to a wide range of tasks from watching movies, playing [CS:GO] or training [neural-networks].

So what all things do we need apart from an adder or a mathematical calculator to do these complex tasks. Clearly adding, multiplying and dividing numbers won't be able to scan your finger print and let you access your computer. Let's try to answer this question of completeness and find the set of tools required to do all computation required by us.

## Finite State Automata

We humans do very complex string matching which enables us to do a variety of interesting tasks, one of which is reading this very blog. It's interesting that our brain not only does text-matching in trivial ways, but also in some non-trivial and non-intuitive ways. Let's try to read this [piece of text].
>Aoccdrnig to a study at an Elingsh uinervtisy, it deosn't mttaer in waht oredr the ltteers in a wrod are, the olny iprmoetnt tihng is taht the frist and lsat ltteer be at the rghit pclae. The rset can be a toatl mses and you can sitll raed it wouthit porbelm. Tihs is bcuseae the huamn mnid deos not raed ervey lteter by istlef, but the wrod as a wlohe.

There are varied application of text-matching ranging from [DNA Profiling], [Google Search] which powers the web and [Parsers] which lets us write and compile/interpret these wonderful pieces of code that we write.

[Regex] matching has become the bread and butter of computer science. Let's try to get an insight on how computers do it.

The origins of regex matching takes us to [Finite State Automata].

Definitions first :
> A finite-state machine (FSM) or finite-state automaton (FSA, plural: automata), finite automaton, or simply a state machine, is a mathematical model of computation. It is an abstract machine that can be in exactly one of a finite number of states at any given time. The FSM can change from one state to another in response to some external inputs; the change from one state to another is called a transition. An FSM is defined by a list of its states, its initial state, and the conditions for each transition

<div align="center" >
    <img src="https://www.centerstagedanceal.com/uploads/8/9/0/2/89023116/thi6twewgc_orig.jpg" />
</div>

Let's look at an example to understand this :

<div align="center" >
    <img src="https://upload.wikimedia.org/wikipedia/commons/9/9e/Turnstile_state_machine_colored.svg" />
</div>

This machine can be on one of two states **Locked** and **Unlocked**. The initial state is **Locked**. When we push the locked state, nothing happens, which is represented by a self-loop arrow from **Locked** state to itself. Whereas when we insert the coin, we move to the **Unlocked** state and stay there as long we insert more coins we can stay there. When we push, we go back to the **Locked** state.

Simple enough right ? But what does this have to do with Regular Expressions ?

Let's look at a simple regular expression. `p+qr+`. This accepts strings starting with any number of `p`'s ending with any number of `r`'s and has exactly one `q` in between.

Example of accepted strings are :
- pqr
- ppqr
- pqrrrrr

Unaccepted strings are :
- qr
- pqqr
- pq

<div align="center" >
    <img src="FSM-Regex.svg" />
</div>

The state machine goes to the **accepted** state **D** when it finds a string which matches the regex.

It can be mathematically proven that all regular expressions can be converted to a finite state-automate and there are many libraries that do these very well like the one discussed in [Regex to automata].

#### So, is finite state machine the end of the world ?
Clearly not! If these finite state machines could solve all our needs then why haven't we still colonized Mars ?

Let's try to build a regular expression for accepting strings like `aabb`, `aaabbb`, `aaaabbbb` etc. More specifically of the form a<sup>n</sup> b<sup>n</sup>.

<div align="center" >
    <img src="Non-Regex-FSM.svg" />
</div>

Is the above FSM a correct representation of the machine that accepts a<sup>n</sup> b<sup>n</sup>. Nope! It accepts strings of the form `a+b+`.

We want the number of **a**'s to equal number of **b**'s. But how do we count using an FSM ?
Are we not smart enough enough to build one yet or there is an inherent limitation in Finite State Automata that it cannot do these kinds of computations.

Using the [Pumping Lemma] and after hours of disappointment we can prove that this is indeed beyond the reach of FSM's.

Intuition says that we are not able to keep track of the count and we need some way to do that. We need something like a variable(s) to store information. This brings us to our next model of computing : Push Down Automata.

## Push Down Automata

## Turing Machines

## Quantum Computing

## Other Models

## Appendix

Contains some of the interesting topics that I have explored during this blog.
- [Primality Test](https://en.wikipedia.org/wiki/Primality_test#Probabilistic_tests). I really liked the [Heuristic tests] section where this is discussed :
>These are tests that seem to work well in practice, but are unproven and therefore are not, technically speaking, algorithms at all. The Fermat test and the Fibonacci test are simple examples, and they are very effective when combined. John Selfridge has conjectured that if p is an odd number, and p ≡ ±2 (mod 5), then p will be prime if both of the following hold:
2<sup>(p−1)</sup> ≡ 1 (mod p)
f<sub>p+1</sub> ≡ 0 (mod p)
where f<sub>k</sub> is the k-th Fibonacci number.

[Hardy-Ramanujan number]: https://en.wikipedia.org/wiki/1729_(number)
[test the primality]: https://en.wikipedia.org/wiki/Primality_test
[calculator]: https://en.wikipedia.org/wiki/Calculator
[CS:GO]: http://blog.counter-strike.net/
[neural-networks]: https://en.wikipedia.org/wiki/Artificial_neural_network
[Heuristic tests]: https://en.wikipedia.org/wiki/Primality_test#Heuristic_tests
[piece of text]: https://www.smart-jokes.org/english-university-study.html
[DNA Profiling]: https://en.wikipedia.org/wiki/DNA_profiling
[Google Search]: https://google.com
[Parsers]: https://en.wikipedia.org/wiki/Parsing
[Regex]: https://en.wikipedia.org/wiki/Regular_expression
[Finite State Automata]: https://en.wikipedia.org/wiki/Finite-state_machine
[Regex to automata]: https://stackoverflow.com/questions/36853077/convert-finite-state-machine-to-regular-expression
[Pumping Lemma]: https://en.wikipedia.org/wiki/Pumping_lemma_for_regular_languages
