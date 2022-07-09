# FIRST WRITING GIG TEMPLATE
<p align="center">
  <a href="" rel="noopener">
 <img src="https://docs.reach.sh/assets/logo.png" alt="Project logo"></a>
</p>
<h3 align="center">The Price Is Right </h3>

<div align="center">


</div>

---

<p align="center"> Tutorial for The Price is Right
    <br> 
</p>


In this tutorial we would go through all the steps for creating a decentralized application or probably your first decentralized application on the algorand blockchain using REACH.

If you have minimum knowledge in web development (i.e javascript, python e.t.c) and is new to the blockchain/algorand space this is the best and fun place to start.
## Introduction

Lets start with some few questions to get proper footing


- What is blockchain?
- What is a decentralized application (DAPP)
- What is Reach

1 - What is blockchain : Simply put, a blockchain is a special kind of database. According to cigionline.org, the term blockchain refers to the whole network of distributed ledger technologies. According to Oxford Dictionaries, a ledger is “a book or other collection of financial accounts of a particular type.” It can be a computer file that records transactions. A ledger is actually the foundation of accounting and is as old as writing and money. Now imagine a whole suite of incorruptible digital ledgers of economic transactions that can be programmed to record and track not only financial transactions but also virtually everything of value. It is the work of a blockchain developer to build, maintain, and design blockchain applications and systems. Overall, it seeks to use the unique features of blockchain technology to solve problems and create opportunities.

2 - What is a decentralized application (DAPP) : A DAPP is basically any web application slapped on a blockchain, it is s an application that can operate autonomously, typically through the use of smart contracts, that runs on a decentralized computing, blockchain or other distributed ledger system. Like traditional applications, DApps provide some function or utility to its users. However, unlike traditional applications, DApps operate without human intervention and are not owned by any one entity, rather DApps distribute tokens that represent ownership.

3 - What is Reach : Reach is a domain-specific language for building decentralized applications (DApps). Reach provides a programming language and specialized compiler for developing decentralized applications. Developers can build applications twice as fast using Reach compared to current methods while automatically verifying their code’s correctness. Reach is so beautiful because it does so much for the developer in very little time with guaranteed security, reach checks all the theorems in the backend and validates if any of the players cheats or isn’t being fair making it the best blockchain platform from my own perspective based on speed, convenience and security.

Now that we have a proper understanding of blockchain development and what reach does lets go ahead and prepare all the tools needed to create your first decentralized application, but before that you must be asking  "What is going to be the function of this DAPP in this tutorial" and the answer is We are going to create a guessing game with a wager in which 2 players have to guess a random number from 1 to 10 and the winner gets the wager.

Pretty simple right, now lets set up our tools needed to create this app

## Installation and Configurations

These setup methods are for Mac OS users for windows users you can check out this [link](https://docs.reach.sh/guide/windows/#guide-windows)

- You will need to install `make` which comes with every mac and also install `Docker `

- To verify if every thing is installed properly run the following commands on your terminal
```bash
make --version
```
```bash
docker --version
```
```bash
docker-compose --version
```
If all this runs succesfully without any errors you are good to go to the next step

- Lets create a directory for this project. I would recommend using 
```bash
mkdir -p ~/reach/the-price-is-right && cd ~/reach/the-price-is-right
```
- Next lets install reach using the command below
```bash
curl https://docs.reach.sh/reach -o reach ; chmod +x reach
```
Run ```./reach version``` on your terminal to verify if reach was installed properly

- For the next step make sure your docker is open, then navigate to the directory we just created i.e `reach/the-price-is-right` then open it on your vs code, once in your vs code open the folder ans create 2 files namely `index.py` and `index.rsh` once done open an integrated terminal and run the following command to install the images
```bash
./reach update
```
this is done on your first time using reach beacause reach needs those images to function ideally.

Now that we have finally installed all our requirements we can dive into the fun part.

## Setup

At the end of this tutorial you will be able to create a console app on the algorand developer network using python and reach rpc server and also a react app that has been deployed and hosted with a link to the demo.

Lets start with the `index.rsh` file which is the backend file and also the smart contract of this app we are about to create together.In this game there are 2 players Alice and Bob where Alice is the deployer or the contract and Bob attaches to this contract, all these would make more sense as we proceed in this tutorial. I advice you to write the codes out rather than just copying and pasting. Now Let's Begin.

```js
1 "reach 0.1";
2 
3 const [ isOutcome, B_WINS, DRAW, A_WINS ] = makeEnum(3);
4
5 const winner = (getResult1,hand1, hand2) => {
6      const x = getResult1;
7  if (hand1 == x && hand2 != x){
8    return A_WINS;
9  }
10  else if(hand1 != x  && hand2 == x){
11    return B_WINS;
12  }
13  else if (hand1 == x && hand2 == x){
14    return DRAW;
15  }
16  else  return DRAW;
17
18 }
```
- Line 1 tells the compiler that this is a reach file.
- Line 3 this defines enumerations for the outcome of the game.
- Line 5 to 18 defines a function that comuputes the winner of the game using their inputs and the random number as arguments
```js
21 const combineRandom = (randomAlice, randomBob) => {
22  const result = randomAlice + randomBob;
24  return result;
25 };
```
-Line 21 to 25 is a function that computes the raandom number, the idea is that Both players generate a random number from 1 to 5 and the addition of those random numbers equals the `result` i.e. the total random number

```js
28 const payWinner = (outcome, wager, Alice, Bob) => {
29  if (outcome == DRAW) {
30    each([Alice, Bob], () => {
31      interact.seeOutcome(outcome)
32    });
33    transfer(wager).to(Alice);
34    transfer(wager).to(Bob);
35  }
36  else if(outcome == A_WINS) {
37    transfer(2*wager).to(Alice);
38    each([Alice, Bob], () => {
39      interact.seeOutcome(outcome);
40    });
41  }
42  else {
43    transfer(2*wager).to(Bob);
44    each([Alice, Bob], () => {
45      interact.seeOutcome(outcome);
46    });
47  }
48 }
```
- Line 28 to 48 defines a function that uses the output of the `outcome` function to pay the winner of the game or in a case of a draw the contract pays back the wager to each of the players
- On line 29 to 35 the if statement checks if the outcome is a draw and both alice and bob interacts with the `seeOutcome` function which allows both of them to see the outcome of the game i.e. You win, You lose, Draw and online 33 and 34 the contract transfers their wager to their addresses.
- Same thing on line 36 to 41 , the else if statement check if Alice wins and transfers the whole wager to Alice and the same thing for line 42 to 48 but in this case Bob wins and gets the wager. N.B : At this step these functions have not been called yet but they will be later on in the code. Stay Tuned.

```js
52 const Player = {
53  ...hasRandom,
54  getHand: Fun([], UInt),
55  seeOutcome: Fun([UInt], Null),
56  informTimeout: Fun([], Null),
57  informNewRound: Fun([], Null),
58  getRandom: Fun([], UInt),  
59 };
```
- Line 52 to 59 is used to tell reach that this is the general player interface i.e the defining functions created in the frontend which both players have access to interact with.
- Line 54 ` getHand: Fun([], UInt),` is a function that gets an unsigned integer from the frontend, this integer is the guess of each player. 
- Line 55 `seeOutcome: Fun([UInt], Null),`  is a function that returns an unsigned integer to the frontend , and this integer is the output of the seeOutcome function which allows both players to see the outcome of the game on their screens.
- Line 56 `informTimeout: Fun([], Null),` is used for a timeout interact interface which both players can interact with
- Line 57 `informNewRound: Fun([], Null),` is a new round interact to signify the end of a round if no player guesses the right number
- Line 58 `getRandom: Fun([], UInt),` is a function that gets the random number for each player from the frontend and is then used as an argument in function `combineRandom ` to get the total random number for the game.

```js
64 export const main = Reach.App(() => {
65 //Alice interface
66  const Alice = Participant('Alice', {
67    ...Player,
68    wager: UInt, 
69    deadline: UInt,
70    waitingForAttacher: Fun([], Null)
71  });
72 //Bob interface
73  const Bob   = Participant('Bob', {
74    ...Player,
75    acceptWager: Fun([UInt], Null),
76  });
77  init();
```
- Line 64 to 76 is used to define the participants and assigning the appropriate funtions to the participants.
- Line 66 to 71 defines the alice participant interface where alice has access to the `Player` function and also the wager, for setting the wager of the game, the deadline for accepting that wager, and the `waitingForAttacher: Fun([], Null)` function which represents that Alice has deployed the contract and is waiting for the attacher i.e Bob
- Line 73 to 76 defines bob's interface with the `Player` function and the `acceptWager` where bob either accepts or declines the wager then attaches to the contract.
- Line 77 is used to initialize the application and finalizes all the available participants.
Next we would be looking at the frontend console app using python i.e our `index.py` file 
```py
1 # flake8: noqa
2 
3 import time
4 import random
5 from threading import Thread
6 from reach_rpc import mk_rpc
7 
8 def main():
9    rpc, rpc_callbacks = mk_rpc()
10
11    starting_balance = rpc('/stdlib/parseCurrency', 100)
12    acc_alice        = rpc('/stdlib/newTestAccount', starting_balance)
13    acc_bob          = rpc('/stdlib/newTestAccount', starting_balance)

```
- Line  3 to 6 is used to import the modules used in the frontend, including the rpc server on line 6 used in connecting a python frontend to a reach backend.
- Line 8 is the begining of the main function while line 9 are the functions from the reach_rpc library needed for connecting to the backend and you have to install this library first before using it in your `index.py` file. Run the following on your integrated terminal 
```bash
pip3 install --upgrade reach-rpc-client
```  
- Once done you can use the rpc client server in your frontend .
Line 11 to 13 is used to create the participants account with their starting balance as indicated on line 11.
```py
15    def fmt(x):
16        return rpc('/stdlib/formatCurrency', x, 4)
17
18    def get_balance(w):
19        return fmt(rpc('/stdlib/balanceOf', w))
20
21    before_alice = get_balance(acc_alice)
22    before_bob   = get_balance(acc_bob)
23
24    ctc_alice    = rpc('/acc/contract', acc_alice)
```
- Line 15 to 16 is a function used to format the balance of the test accounts to 4 decimal places.
- Line 18 to 19 is a function used to get the balance of the 2 test accounts
- Line 21 to 22 is used to get the initial balance of Alice and Bob the 2 players.
- On line 24 Alice deploys the contract and waits for Bob to attach to it.
```py
27  OUTCOME      = ['Bob wins', 'Draw', 'Alice wins,']
28    
29
30    def player(who):
31        def getHand():
32            hand = int(input(who + " ENTER A NUMBER FROM 0 TO 10: "))
33            print('%s played %s' % (who, hand))
34            return hand
35        def getResult():
36           print("WELCOME TO PRICE IS RIGHT\nYOU HAVE 3 TRIALS TO GUESS THE RIGHT PRICE ")
37            time.sleep(1)
38            num =random.randrange(1,5)
39            print("THIS IS THE RANDOM NUMBER CHOOSEN BY THE MACHINE " + str(num) + "\nNOTE THIS IS VISIBLE  SOLELY FOR DEBUGGING PURPOSES." )
40            return num
41        def newRound():
42            print("NEW ROUND")
```
- Line 27 is used to create a list which is mapped in the backend to display the outcome of the game
- Line 30 is the begining of the player function which inhabits all the abilities of the players line 31 to 34 is a function each player can interact with to input thier guess and it returns the number to the backend.
- Line 35 to 40 is used to generate the random number from 1 to 5 for each of the participants 
- Line 41 to 42 is used to indicate a new round if the last round had no winner.

```py
45 def informTimeout():
46            print('%s observed a timeout' % who)
47
48        def seeOutcome(n):
49            print('%s saw outcome %s'
50                  % (who, OUTCOME[rpc('/stdlib/bigNumberToNumber', n)]))
51
52        return {'stdlib.hasRandom': True,
53                'getHand':          getHand,
54                'getResult':        getResult,
55                'newRound':         newRound,
56                'informTimeout':    informTimeout,
57                'seeOutcome':       seeOutcome,
58                }
```
- Line 45 to 46 is used when to mimic a timeout 
- Line 48 to 49 is a function that uses the rpc function to return a number from the back end which is used to map the `OUTCOME` list on line 25, and this displays the out come at the end of the game.
- Line 52 to 58 is used to return the outputs of all these functions and it signifies the end of the frontend functions.
Now lets return back to the `index.rsh` file
```js
77  const informTimeout = () => {
78    each([Alice, Bob], () => {
79      interact.informTimeout();
80    });
81  };
82 //Alice wager and the deadline for the timeout
83  Alice.only(() => {
84    const wager = declassify(interact.wager);
85    const deadline = declassify(interact.deadline);
86    const randomAlice = declassify(interact.getRandom());
87  });
88  Alice.publish(wager, deadline, randomAlice)
89    .pay(wager);
90  commit();
91
92  Alice.interact.waitingForAttacher();
```
- Line 77 to 81 is the function that allows alice and bob to interact with the timeout function.
- Line 83 to 87 is Alice interacting with his/her functions  i.e the wager function, deadline function for the timeout, and a random number from 1 to 5 which is created randomly by alice's machine which is later on added to bob's random number to generate the total random number of the game, this helps to make the game as fair as possible making it impossible to cheat.
- Line 88 is used to broadcast/publish the following inputs by alice to the blockchain so it can be seen that alice made a wager, the deadline and the random number making all that known to the smart contract.
- Line 89 is where alice pays the wager into the contract.
- Line 90 signifies the end of the current consensus step.
- Line 92 is an empty function which was later implemented in the react frontend of this project. But to run this console app it is advicable to comment this line out.
```js
94 //Bob accepting or rejecting the wager
95  Bob.only(() => {
96    interact.acceptWager(wager);
97    const randomBob = declassify(interact.getRandom());
98  });
99  Bob.publish(randomBob)
100  .pay(wager)
101    .timeout(relativeTime(deadline), () => closeTo(Alice, informTimeout));
102
103 //The random number
104  const random = combineRandom(randomAlice, randomBob);
```
- In line 94 to 98 bob accepts the wager and interacts with the `getRandom` function to generate his/her own random number which is a number in the range of 1 to 5.
- On line 99 Bob publishes his random number.
- On line 100 Bob pays a the wager
- Line 101 is Bob ineracting with the timeout function.
- The variable `random` on line 104 is used the value of the combineRandom function with alice and bob's random number as its arguments.

```js
106 //While loop that loops 3 times  if the users inputs arent correct
107 var [stage, hand1, hand2 ] = [3,0,0];
108  invariant( balance() == 2 * wager);
109
110  while ((hand1!= random && hand2 != random) && (stage > 0)) {
111    commit();
112
113    Bob.interact.informNewRound();
114    Alice.interact.informNewRound();
115
116    Alice.only(() => {
117      const _handAlice = interact.getHand();
118      
119      const [_commitAlice, _saltAlice] = makeCommitment(interact, _handAlice);
120      const commitAlice = declassify(_commitAlice);
121      });
122    Alice.publish(commitAlice)
123      .timeout(relativeTime(deadline), () => closeTo(Bob, informTimeout));
124    commit();
125
126    unknowable(Bob, Alice(_handAlice, _saltAlice));
```
- Now this ia the tricky part, in reach using a while loop this is the synatax below
```js
var LHS = INIT_EXPR;
DEFINE_BLOCK; // optional
invariant(INVARIANT_EXPR, ?INVARIANT_MSG);
while( COND_EXPR ) BLOCK
```
where `LHS` is a valid left-hand side of an identifier definition where the expression `INIT_EXPR` is the right-hand side, and  `DEFINE_BLOCK` is an optional block that may define bindings that use the `LHS` values which are bound inside the rest of the while and its tail, and  `INVARIANT_EXPR` is an expression, called the loop invariant, that must be true before and after every execution of the block `BLOCK`—it may be specified multiple times, `INVARIANT_MSG` is an optional bytes argument, which is included in any reported violation, and if `COND_EXPR` is true, then the block executes, and if not, then the loop terminates and control transfers to the continuation of the while statement. The identifiers bound by LHS are bound within `DEFINE_BLOCK`, `INVARIANT_EXPR`, `COND_EXPR`, `BLOCK`, and the tail of the while statement.

- On line 107 is the var which is a variable that the values can change unlike a const which is a constant and must remain that. `Stage` is used to loop 3 times- its like a counter and  `hand1` and `hand2` is currently 0 but we will refer back to it.
- Line 108 is the invariant which is a statement that must be true throughout the code, i.e before and after the while loop, if this is false at any time during compilation it would return a theorem violation error.
- Line 110 is the begining of the while loop and using the following conditions the while loop continues until either of the conditions are met, the idea is that the players keep on playing until either of them get the correct answer or until the maximum stage is hir, i.e 3 meaning they have 3 trials to guess the right answer else no one wins and the contract gives them back their wager and triggers them to play again.
- On Line 116 to 124 Alice interacts with the get hand function and the input is stores in `_handAlice` which is then passed through a makeCommitment function which returns a commit and salt for the input where salt is the result of calling interact.random(), and  commitment is the digest of salt and _handAlice. This helps to make the input of Alice hidden before revealing at the same time when Bob interacts with the same function making it impossible to cheat.
- Line 126 is used to verify that alice guess is unknown to bob.
```js
127    Bob.only(() => {
128      const handBob = declassify(interact.getHand());
129      
130    });
131    Bob.publish(handBob)
132      .timeout(relativeTime(deadline), () => closeTo(Alice, informTimeout));
133    commit();
134
135    Alice.only(() => {
136      const saltAlice = declassify(_saltAlice);
137      const handAlice = declassify(_handAlice);
138    });
139    Alice.publish(saltAlice, handAlice)
140      .timeout(relativeTime(deadline), () => closeTo(Bob, informTimeout));
141    checkCommitment(commitAlice, saltAlice, handAlice);
```
- On line 127 to 130 Bob interacts with the `getHand` function i.e inputing his/her guess.
- And the next line bob publishes his input, but line 132 is used so bobs input is been displayed at the same time alice input was displayed to avoid any form of unfairness.
- Line 135 to 140 is used to declassify alice salt and digest, publishes it and displays it at the same time bob displays his/hers.
- Line 141 is used to verify if that commitment is the digest of saltAlice and handAlice.
```js
143 [stage, hand1, hand2] = [stage-1, handAlice, handBob];
144    continue;
145
146  }
147  //Using the winner function with arguments of the users inputs and the random number to get the winner
148  const outcome = winner(random, hand1, hand2); 
149
150 //Uses the outcome to pay the winner
151  payWinner(outcome,wager, Alice, Bob);
152
153  commit();
154
155 });
```
- On Line 143 `Stage` is going to be decremented everytime the while loop continues, `hand1` is going to be equal to `handAlice` and `hand2` is going to be equal to `handBob` everytime the while loop is on, this way it solidifies the feature of the players having 3 trials to get the right guess.
- Line 144 is a continue statement which is a terminator statement, so it must have an empty tail.
- On Line 148 the `winner` function is been called with the correct arguments to determine the outcome of the game.
- Line 151 is used to call the `payWinner` function with the valid arguments to pay the wager according to the outcome of the game.
- And line 153 is the final commit for the reach smart contract.
This is the end of the `index.rsh` file now lets finialize the `index.py` user interface.
```py
70     def play_alice():
71        
72        rpc_callbacks(
73            '/backend/Alice',
74            ctc_alice,
75            dict(wager=rpc('/stdlib/parseCurrency', 5), deadline=10,  **player('Alice')))
76
77    alice = Thread(target=play_alice)
78    alice.start()
79
80    def play_bob():
81        def acceptWager(amt):
82            print('Bob accepts the wager of %s' % fmt(amt))
83
84        ctc_bob = rpc('/acc/contract', acc_bob, rpc('/ctc/getInfo', ctc_alice))
85        rpc_callbacks(
86            '/backend/Bob',
87            ctc_bob,
88            dict(acceptWager=acceptWager, **player('Bob')))
89        rpc('/forget/ctc', ctc_bob)
```
- Line 70 to 78 is the `play_alice` function where alice interacts with the reach backend using the rpc callback function and indicates the wager, the deadline and the player interface. Using a `thread` we are able to mimic the `Promise` functionality in javascript in python.
- Line 80 to 89 is the `play_bob` function that does practically the same thing as `play_alice` but instead of indicating a wager accepts the wager and attaches to the contract deployed by alice.
```py
82    bob = Thread(target=play_bob)
83    bob.start()
84
85    alice.join()
86    bob.join()
87
88    after_alice = get_balance(acc_alice)
89    after_bob   = get_balance(acc_bob)
90
91    print('Alice went from %s to %s' % (before_alice, after_alice))
92    print('  Bob went from %s to %s' % (before_bob,   after_bob))
93
94    rpc('/forget/acc', acc_alice, acc_bob)
95    rpc('/forget/ctc', ctc_alice)
96
97
98 if __name__ == '__main__':
99    main()
```
- On Line 82 Bob creates his own thread annd on line 83 starts it
- On line 85 Alice joins the thread first and bob joins last meaning that they both have to fully finish interacting with their functions before the thread ends.
- On line 88 to 89 we get the balances of alice and bob after the game
- Line 91 and 92 prints their balances.
- Line 94 and 95 is used to forget the contract.
- And line 98 to 99 ensures the main function is called.

# Finalization 
The `index.rsh` file should look like this
```js
"reach 0.1";
//Outcome array
const [ isOutcome, B_WINS, DRAW, A_WINS ] = makeEnum(3); 

//This computes the winner of the game
const winner = (getResult1,hand1, hand2) => {
  const x = getResult1;
  if (hand1 == x && hand2 != x){
    return A_WINS;
  }
  else if(hand1 != x  && hand2 == x){
    return B_WINS;
  }
  else if (hand1 == x && hand2 == x){
    return DRAW;
  }
  else  return DRAW;

};
// Generates the random number
const combineRandom = (randomAlice, randomBob) => {
  const result = randomAlice + randomBob;
  return result;
};


// Makes the required payment to the winner
const payWinner = (outcome, wager, Alice, Bob) => {
  if (outcome == DRAW) {
    each([Alice, Bob], () => {
      interact.seeOutcome(outcome)
    });
    transfer(wager).to(Alice);
    transfer(wager).to(Bob);
  }
  else if(outcome == A_WINS) {
    transfer(2*wager).to(Alice);
    each([Alice, Bob], () => {
      interact.seeOutcome(outcome);
    });
  }
  else {
    transfer(2*wager).to(Bob);
    each([Alice, Bob], () => {
      interact.seeOutcome(outcome);
    });
  }
}


//Player abilities
const Player = {
  ...hasRandom,
  getHand: Fun([], UInt),
  seeOutcome: Fun([UInt], Null),
  informTimeout: Fun([], Null),
  informNewRound: Fun([], Null),
  getRandom: Fun([], UInt),
};


export const main = Reach.App(() => {
//Alice interface
  const Alice = Participant('Alice', {
    ...Player,
    wager: UInt, 
    deadline: UInt,
    waitingForAttacher: Fun([], Null)
  });
//Bob interface
  const Bob   = Participant('Bob', {
    ...Player,
    acceptWager: Fun([UInt], Null),
  });
  init();

  const informTimeout = () => {
    each([Alice, Bob], () => {
      interact.informTimeout();
    });
  };
//Alice wager and the deadline for the timeout
  Alice.only(() => {
    const wager = declassify(interact.wager);
    const deadline = declassify(interact.deadline);
    const randomAlice = declassify(interact.getRandom());
  });
  Alice.publish(wager, deadline, randomAlice)
    .pay(wager);
  commit();

 

//Bob accepting or rejecting the wager
  Bob.only(() => {
    interact.acceptWager(wager);
    const randomBob = declassify(interact.getRandom());
  });
  Bob.publish(randomBob)
  .pay(wager)
    .timeout(relativeTime(deadline), () => closeTo(Alice, informTimeout));

//The random number
  const random = combineRandom(randomAlice, randomBob);

//While loop that loops 3 times  if the users inputs arent correct
  var [stage, hand1, hand2 ] = [3,0,0];
  invariant( balance() == 2 * wager);

  while ((hand1!= random && hand2 != random) && (stage > 0)) {
    commit();

    Bob.interact.informNewRound();
    Alice.interact.informNewRound();

    Alice.only(() => {
      const _handAlice = interact.getHand();
      
      const [_commitAlice, _saltAlice] = makeCommitment(interact, _handAlice);
      const commitAlice = declassify(_commitAlice);
      });
    Alice.publish(commitAlice)
      .timeout(relativeTime(deadline), () => closeTo(Bob, informTimeout));
    commit();

    unknowable(Bob, Alice(_handAlice, _saltAlice));
    Bob.only(() => {
      const handBob = declassify(interact.getHand());
      
    });
    Bob.publish(handBob)
      .timeout(relativeTime(deadline), () => closeTo(Alice, informTimeout));
    commit();

    Alice.only(() => {
      const saltAlice = declassify(_saltAlice);
      const handAlice = declassify(_handAlice);
    });
    Alice.publish(saltAlice, handAlice)
      .timeout(relativeTime(deadline), () => closeTo(Bob, informTimeout));
    checkCommitment(commitAlice, saltAlice, handAlice);
    
    [stage, hand1, hand2] = [stage-1, handAlice, handBob];
    continue;

  }
  //Using the winner function with arguments of the users inputs and the random number to get the winner
  const outcome = winner(random, hand1, hand2); 

//Uses the outcome to pay the winner
  payWinner(outcome,wager, Alice, Bob);

  commit();

});
```
And the `index.py` file should look like this
```py
# flake8: noqa

import time
import random
from threading import Thread
from reach_rpc import mk_rpc

def main():
    rpc, rpc_callbacks = mk_rpc()

    starting_balance = rpc('/stdlib/parseCurrency', 100)
    acc_alice        = rpc('/stdlib/newTestAccount', starting_balance)
    acc_bob          = rpc('/stdlib/newTestAccount', starting_balance)

    def fmt(x):
        return rpc('/stdlib/formatCurrency', x, 4)

    def get_balance(w):
        return fmt(rpc('/stdlib/balanceOf', w))

    before_alice = get_balance(acc_alice)
    before_bob   = get_balance(acc_bob)

    ctc_alice    = rpc('/acc/contract', acc_alice)

    
    OUTCOME      = ['Bob wins', 'Draw', 'Alice wins,']
    

    def player(who):
        def getHand():
            hand = int(input(who + " ENTER A NUMBER FROM 0 TO 10: "))
            print('%s played %s' % (who, hand))
            return hand
        def getRandom():
            print("WELCOME TO PRICE IS RIGHT\nYOU HAVE 3 TRIALS TO GUESS THE RIGHT PRICE ")
            time.sleep(1)
            num =random.randrange(1,5)
            print("THIS IS THE RANDOM NUMBER CHOOSEN BY THE MACHINE " + str(num) + "\nNOTE THIS IS VISIBLE  SOLELY FOR DEBUGGING PURPOSES." )
            return num
        def newRound():
            print("NEW ROUND")
       

        def informTimeout():
            print('%s observed a timeout' % who)

        def seeOutcome(n):
            print('%s saw outcome %s'
                  % (who, OUTCOME[rpc('/stdlib/bigNumberToNumber', n)]))

        return {'stdlib.hasRandom': True,
                'getHand':          getHand,
                'getRandom':        getRandom,
                'newRound':         newRound,
                'informTimeout':    informTimeout,
                'seeOutcome':       seeOutcome,
                }

    def play_alice():
        
        rpc_callbacks(
            '/backend/Alice',
            ctc_alice,
            dict(wager=rpc('/stdlib/parseCurrency', 5), deadline=10,  **player('Alice')))

    alice = Thread(target=play_alice)
    alice.start()

    def play_bob():
        def acceptWager(amt):
            print('Bob accepts the wager of %s' % fmt(amt))

        ctc_bob = rpc('/acc/contract', acc_bob, rpc('/ctc/getInfo', ctc_alice))
        rpc_callbacks(
            '/backend/Bob',
            ctc_bob,
            dict(acceptWager=acceptWager, **player('Bob')))
        rpc('/forget/ctc', ctc_bob)
        

    bob = Thread(target=play_bob)
    bob.start()

    alice.join()
    bob.join()

    after_alice = get_balance(acc_alice)
    after_bob   = get_balance(acc_bob)

    print('Alice went from %s to %s' % (before_alice, after_alice))
    print('  Bob went from %s to %s' % (before_bob,   after_bob))

    rpc('/forget/acc', acc_alice, acc_bob)
    rpc('/forget/ctc', ctc_alice)


if __name__ == '__main__':
    main()
```
- Now lets compile the `index.rsh file` using the following command on your integrated terminal 
```bash
./reach compile
```
The output should look like this
```bash
Verifying knowledge assertions
Verifying for generic connector
  Verifying when ALL participants are honest
  Verifying when NO participants are honest
Checked 87 theorems; No failures!
```
Then we would go ahead and run the `index.py` with the reach backend using 
```bash
./reach rpc-run python3 -u ./index.py
```
The output should look like this
```bash
Warning! Using development RPC key: REACH_RPC_KEY=opensesame.
Warning! The current TLS certificate is only suitable for development purposes.
Verifying knowledge assertions
Verifying for generic connector
  Verifying when ALL participants are honest
  Verifying when NO participants are honest
Checked 87 theorems; No failures!
Creating 2022-07-05t10-49-23z-ix6j_reach-app-unique-number-game_run ... done

> app
> node --experimental-modules --unhandled-rejections=strict index.mjs


*** Warning! TLS verification disabled! ***

 This is highly insecure in Real Life™ applications and must
 only be permitted under controlled conditions (such as
 during development).

WELCOME TO PRICE IS RIGHT
YOU HAVE 3 TRIALS TO GUESS THE RIGHT PRICE 
THIS IS THE RANDOM NUMBER CHOOSEN BY THE MACHINE 2
NOTE THIS IS VISIBLE  SOLELY FOR DEBUGGING PURPOSES.
Bob accepts the wager of 5
WELCOME TO PRICE IS RIGHT
YOU HAVE 3 TRIALS TO GUESS THE RIGHT PRICE 
THIS IS THE RANDOM NUMBER CHOOSEN BY THE MACHINE 1
NOTE THIS IS VISIBLE  SOLELY FOR DEBUGGING PURPOSES.
Alice ENTER A NUMBER FROM 0 TO 10: 4
Alice played 4
Bob ENTER A NUMBER FROM 0 TO 10: 3
Bob played 3
Alice saw outcome Bob wins
Bob saw outcome Bob wins
Alice went from 100 to 94.992
  Bob went from 100 to 104.997
```
In this test scenerio we made bob to win the game.
Now we have finally created our The price is right console decentralized application Yay.
For a link to the test repo Click [Here](https://github.com/terrykeng/REACH-HACK/tree/main/The%20Price%20Is%20Right-Reach%20Hack/test)
Now we will do a brief explaination of how to build the frontend using react js

# Further Learning
If you want to implement this  using react, vue, angular
below is the app.js file for the game logic
```js
import * as backend from './build/index.main.mjs';
import { loadStdlib } from '@reach-sh/stdlib';
import { ALGO_MyAlgoConnect as MyAlgoConnect } from '@reach-sh/stdlib';

import './App.css';
import { views } from './helpers/constants.js';
import { useEffect, useState } from 'react';

//views
import { 
  ConnectAccount,
  DeployOrAttach,
  SetWager,
  Deploying,
  WaitForAttacher,
  AcceptWager,
  Attaching,
  WaitForTurn,
  PlayTurn,
  Timeout,
  SeeWinner,
  PasteContractInfo
} from './views/';

const reach = loadStdlib('ALGO');
reach.setWalletFallback(reach.walletFallback( { providerEnv: 'TestNet', MyAlgoConnect } ));
const { standardUnit } = reach;

function App() {
  const [ view, setView ] = useState(views.CONNECT_ACCOUNT);
  const [ guess, setGuess ] = useState(undefined);
  const [ round, setRound ] = useState(0);
  const [ outcome, setOutcome ] = useState();
  const [ getHand, setGetHand ] = useState(false);
  const [ playedTurn, setPlayedTurn ] = useState(false);
  const [ account, setAccount ] = useState({});
  const [ isAlice, setIsAlice ] = useState(true);
  const [ resolver, setResolver ] = useState();
  const [ contractInfo, setContractInfo ] = useState("");
  const [ wager, setWager ] = useState();

  const helperFunctions = {
    connect: async (secret, mnemonic = false) => {
      let result = ""
      try {
        const account = mnemonic ? await reach.newAccountFromMnemonic(secret) : await reach.getDefaultAccount();
        setAccount(account);
        setView(views.DEPLOY_OR_ATTACH);
        result = 'success';
      } catch (error) {
        result = 'failed';
      }
      return result;
    },

    setAsDeployer: (deployer = true) => {
      if(deployer){
        setIsAlice(true);
        setView(views.SET_WAGER);
      }
      else{
        setIsAlice(false);
        setView(views.PASTE_CONTRACT_INFO);
      }
    },

    deploy: async (wager) => {
      const contract = account.contract(backend);
      const deadline = {ETH: 10, ALGO: 100, CFX: 1000}[reach.connector];
      Alice.wager = reach.parseCurrency(wager);
      Alice.deadline = deadline;
      backend.Alice(contract, Alice);
      setView(views.DEPLOYING);
      setContractInfo( JSON.stringify(await contract.getInfo(), null, 2) );
    },

    attach: (contractInfo) => {
      const contract = account.contract(backend, JSON.parse(contractInfo));
      backend.Bob(contract, Bob)
    },

    guess: (hand) => {
      setGuess(hand)
    },

    played: () => {
      setPlayedTurn(true);
      setView(views.WAIT_FOR_TURN)
    },

    playAgain: () => {
      setView(views.DEPLOY_OR_ATTACH);
      setRound(0);
    }

  };

  const Player = {
    random: () => reach.hasRandom.random(),

    informNewRound: () => {
      setRound(round => round + 1);
      setView(views.PLAY_TURN);
      setGuess(undefined);
      setPlayedTurn(false);
      setResolver();
    },
  
    getRandom: () => {
      const random = Math.floor(Math.random()*5);
      console.log("random", random);
      return random;
    },

    seeOutcome: (outcomeHex) => {
      const outcome = parseInt(outcomeHex);
      setOutcome(outcome)
      setView(views.SEE_WINNER)
    },

    informTimeout: () => {
      setView(views.TIME_OUT);
    },

    getHand: async () => {
      setGetHand(true);
      return new Promise(resolve => {
        setResolver({
          resolve: (hand) => {
            resolve(hand);
          },
        })
      })
    }
  }

  const Alice = {
    ...Player,

    wager: 0,

    deadline: 0,
  
    setWagerAndDeadline: ( wager, deadline) => {
      this.wager = wager;
      this.deadline = deadline;
    },
  
    waitingForAttacher: () => {
      setView(views.WAIT_FOR_ATTACHER);
    },
  }

  const Bob = {
    ...Player,

    acceptWager: async (wager) => {
      setView(views.ACCEPT_WAGER);
      setWager(reach.formatCurrency(wager, 4));
      return new Promise((resolve) => {
        setResolver({
          resolve: () => {
            setView(views.ATTACHING);
            resolve();
          },
        })
      });
    },
  }

  useEffect(()=>{
    if(getHand && playedTurn){
      resolver.resolve(guess);
      setGetHand(false);
    }
  }, [getHand, playedTurn, guess, resolver])


  return (
    <div className="App">

      <div className='topnav'>
        <h1>Price Is Right</h1>
      </div>
      
      {
        view === views.CONNECT_ACCOUNT &&
        <ConnectAccount connect={helperFunctions.connect}/>
      }

      {
        view === views.DEPLOY_OR_ATTACH &&
        <DeployOrAttach setAsDeployer={helperFunctions.setAsDeployer}/>
      }

      {
        view === views.SET_WAGER &&
        <SetWager deploy={helperFunctions.deploy}/>
      }

      {
        view === views.DEPLOYING &&
        <Deploying />
      }

      {
        view === views.WAIT_FOR_ATTACHER &&
        <WaitForAttacher contractInfo={contractInfo}/>
      }

      {
        view === views.PASTE_CONTRACT_INFO && 
        <PasteContractInfo attach={helperFunctions.attach}/>
      }

      {
        view === views.ACCEPT_WAGER &&
        <AcceptWager wager={wager} standardUnit={standardUnit} accept={resolver.resolve} decline={() => setView(views.DEPLOY_OR_ATTACH)}/>
      }

      {
        view === views.ATTACHING &&
        <Attaching />
      }

      {
        view === views.WAIT_FOR_TURN &&
        <>
          {
            guess !== undefined && <h3 className='guess'>You played {guess}</h3>
          }
          <WaitForTurn />
        </>
        
      }

      {
        view === views.PLAY_TURN && 
        <PlayTurn 
          guess={helperFunctions.guess} 
          played={helperFunctions.played}
          round={round}
        />
      }

      {
        view === views.TIME_OUT &&
        <Timeout />
      }

      {
        view === views.SEE_WINNER &&
        <SeeWinner outcome={outcome} isAlice={isAlice} playAgain={helperFunctions.playAgain}/>
      }
    </div>
  );
}

export default App;
```
For the link to the full repo check out the [link](https://github.com/terrykeng/REACH-HACK/tree/main/The%20Price%20Is%20Right-Reach%20Hack).

For the demo of the game check out this [link](https://the-price-is-right.netlify.app).

# Conclusion 
If you made it here it means you have completed the price is right reach tutorial and you have create your first guessing game decentralized application.
Our discord community is open to everyone, if you have any questions concerning reach please reach out to us on the discord [community](https://discord.gg/AZsgcXu)




