# JavaScript Regenerated Talk

[https://css-tricks.com/guest-posting/](https://css-tricks.com/guest-posting/)

- What are my current problems?
	- Build step, large bundles, accessibility, too many dependencies to keep up to date.
	- Not having the language that models the problems I’m seeing and solving.
- Why generator functions?
	- Why functional programming?
- Why React works?
	- React taught me that Components are amazing, not necessarily that just React is amazing.
	- React’s limitations.
- What problems can be solved with generator functions?
- Let’s implement a generator function for animation, or parsing.
- How does an HTML renderer with generator functions work?
- Learn pieces that can be used in many domains.
	- Ingredients that can be recombined into many recipes.
- Producing client side JavaScript from the backend on the fly.
- What’s the future?
- Where can I learn more?

## Code examples

Can define the function beforehand:

```js
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}

sum(1, 2, 3, 4, 5) // 15
```

Can define it after:

```js
sum(1, 2, 3, 4, 5) // 15

function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}
```

## Message oriented generator functions

A generator function can send messages out:

```js
const messageA = 'a';
const messageB = 'b';

function* Sender() {
  yield messageA;
  yield messageB;
  yield messageB;
  yield messageA;
}

Array.from(Sender()) // ['a', 'b', 'b', 'a']
```

The order that messages are sent matters:

```js
const messageA = 'a';
const messageB = 'b';

function* Sender() {
  yield messageB;
  yield messageA;
  yield messageA;
  yield messageA;
}

Array.from(Sender()) // ['b', 'a', 'a', 'a']
```

To sent a message `"abc"`, run `yield "abc";`

To receive a message, run `const received = yield "abc";`

By default, `undefined` is replied.

A receiver iterates through a generator, receiving messages. It can reply to each message if it wishes.

A reply is asynchronous, sending a message and receiving a reply don’t have to be at the same point in time.

An example of a receiver is `Array.from()`. It pushes each message onto a new array and returns said array.

---- 

## Glossary

- Send a message
- Receive a message reply
- Return a final message
- Message processor accepts certain messages to solve a problem
- A message can be a primitive: string, number, symbol
- A message can be a collection: array, set, map, object
- A message can be another generator function

```js
function* SomeGenerator() { // name: SomeGenerator
  yield 3.14; // number
  yield "abc"; // string
  yield Symbol("important"); // symbol
  yield [2, 4, 6]; // array of messages
  yield OtherGenerator; // another generator function
  return 100; // final message
}

function* OtherGenerator() {
  …
}

Array.from(Sender()) // ['b', 'a', 'a', 'a']
```


---- 

## Making an HTML renderer

### HTML generators

- Send a string to be presented
	- Any HTML unsafe characters will be escaped
- Send a known-to-be-safe HTML string to be presented
	- The string will be left as raw
- Send another generation function
	- This generation function also yields elements to be presented
- Send a Promise resolving to one of the above
	- The processing is suspended here until the Promise resolves
	- If the Promise rejects, then the error handler is used

### HTML processor

- Receives a string to be presented
	- If it contains HTML unsafe characters, escape them
	- Write it to the stream
- Receives a known-to-be-safe HTML string to be presented
	- Write it to the stream
- Receives another generation function
	- Call the generator function, and process its yielded elements
- Receive a Promise resolving to one of the above
	- After the Promise resolves, write it to the stream
	- If the Promise rejects, then the error handler is called and its result written to the stream

---- 

## Making Declarative Graphics



---- 

## Making a Parser

### Parser generators

- Send a string to be matched
	- If it doesn’t match, then this parser exits
- Send a regular expression to be matched
	- Replies with an array of matches
	- If it doesn’t match, then this parser exits
- Send another generation function
	- This generation function also yields elements to be matched
	- Replies with the return value of the generator function
	- If it doesn’t match, then this parser exits
- Send an array of strings, regular expressions, or generator functions
	- Replies with the first element to match
	- If none match, then this parser exits

### Parser processor

- Receives a string to be matched
	- Checks the received string against the start of our input string
	- If it doesn’t match, then fails
	- The input string has its beginning trimmed up to what matched
- Receives a regular expression to be matched
	- Matches the regular expression against the start of the input string
	- If it doesn’t match, then fails
	- Replies with the array of matches
	- The input string has its beginning trimmed up to what matched
- Receives a generation function
	- Call the generator function, and process its yielded elements
	- If it doesn’t match, then fails
	- Replies with the return value of the generator function
- Receives an array of strings, regular expressions, or generator functions
	- Loops through each element
	- If an element matches, then reply with its matched value
	- If none match, then fails

---- 

## HTTP Server Generator

```js
function *Health() {
  yield GET`/health`;
  yield json({ success: true });
}

function* ListAlbums() {
  yield json({ total: 2, items: [{ title: 'Album A' }, { title: 'Album B' }] });
}

function* AlbumDetails() {
  const [ albumID ] = yield GET`/albums/${String}`;
  yield MustBeAuthenticated;
  yield json;

  const details = yield AlbumRepo.readDetails(albumID);
  yield json(details);
}
```

---- 

Mapping from countries to capital cities:

```js
function* Countries() {
  yield 'Argentina';
  yield 'Belgium';
  yield 'Australia';
  yield 'Iraq';
}

lookupCapitalCities(Countries())

function lookupCapitalCities(genCountries) {
  const countriesToCapitalsMap = new Map(…);

  for (country of genCountries()) {
    const capitalCity = countriesToCapitalsMap.get(country);
    
  }
}
```

```js
function* Countries() {
  const capital1 = yield 'Argentina';
  // 'Buenos Aires'

  const capital2 = yield 'Belgium';
  // 'Brussels'

  const capital3 = yield 'Australia';
  // 'Canberra'

  const capital4 = yield 'Iraq';
  // 'Baghdad'

  return [capital1, capital2, capital3, capital4];
}

Array.from(lookupCapitalCities(Countries())) // ['Buenos Aires', 'Brussels', 'Canberra', 'Baghdad']
```
