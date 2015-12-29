### match-when - Pattern matching for modern JavaScript [![Circle CI](https://circleci.com/gh/FGRibreau/match-when/tree/master.svg?style=svg)](https://circleci.com/gh/FGRibreau/match-when/tree/master)

Adding pattern matching to JavaScript (ES6+) through two new special *keywords*\* `match` and `when`. The main goals are **safety** and **shortness**.
There is a lot more to do but after some late work, that's all for tonight!

#### Usage

The setup is pretty simple, simply require the library with `match` and `when` and you are ready to go!

```js
const {match, when} = require('match-when');
```

or globally

```js
require('match-when/register'); // `match` and `when` are now globally available
```

Now let's see how we would write a factorial function:

```js
function fact(n){
  return match({
    [when(0)]: 1,
    [when()]: (n) => n * fact(n-1)
  })(n);
}

fact(10); // 3628800
```

Clear and simple right?

<p align="center">
<img style="width:100%" src="https://cloud.githubusercontent.com/assets/138050/12031158/0e37afda-ae09-11e5-9462-873b45cbb2b4.gif">
</p>

Note that `when()` is a catch-all pattern and should always be the last condition. If you forget it `match()` will throw a `MissingCatchAllPattern` exception.

##### High Order Functions

`match` works well with high order functions like `map`, `filter` (and so on) too:

```js
[2, 4, 1, 2].map(match({
  [when(1)]: "one",
  [when(2)]: "two",
  [when()]: "many"
}));

// [ 'two', 'many', 'one', 'two' ]
```

##### Arrays


It also works with **arrays**:

```js
function length(list){
  return match({
    [when([])]: 0,
    [when()]: ([head, ...tail]) => 1 + length(tail)
  })(list);
}

length([1, 1, 1]); // 3
```

##### OR

Sadly JavaScript does not offer us a way to overload operators so we're stuck with `when.or`:

```js
function parseArgument(arg){
  return match({
    [when.or("-h", "--help")]: () => displayHelp,
    [when.or("-v", "--version")]: () => displayVersion,
    [when()]: (whatever) => unknownArgument.bind(null, whatever)
  })(arg);
}

parseArgument(process.argv.slice(1)); // displayHelp || displayVersion || (binded)unknownArgument
```

##### AND

```js
const protocols = repositories.map(match({
  [when.and({useSSH:true}, {useSSH: true})]: 'git+ssh:',
  [when.and({useSSH:true}, {useHTTP: true})]: 'git+http:',
  [when.and({useSSH:true}, {useHTTPS: true})]: 'git+https:',
  [when()]: 'unsupported:'
}))
```


##### Regular Expressions

match-when supports [regular expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) as well:

```js
['hey.com', 'fg@plop.com', 'fg+plop@plop.com', 'wat'].filter(match({
  [when(/\S+@\S+\.\S+/)]: false, // **seems** to be a valid email (unsafe regex for doc purpose only)
  [when()]: true // the email could be invalid, return it
}));

// ['hey.com', 'wat']
```


### Supported patterns:


- `{ x1: pattern1, ..., xn: patternn }` - matches any object with property names `x1` to `xn` matching patterns `pattern1` to `patternn`, respectively. Only the own properties of the pattern are used.
- `[pattern0, ..., patternn]` - matches any object with property names 0 to n matching patterns `pattern0` to `patternn`, respectively.
- `/pattern/flags` - matches any values than pass the regular expression test
- `when.or(pattern0, pattern1, ...)` - matches if one `pattern` matches.

### Todo:

I will accept PR with their associated tests for the following features:

- `match.and(pattern, ...)` - matches if every pattern matches.
- support `range(x, y)`
- try and maybe support `match(input, {patterns...})` syntax instead of `match({patterns...})(input)`?

[todo-list inspired by pattern-match from dherman](https://github.com/dherman/pattern-match#patterns).

\* *well, of course, they are not keywords but simple functions*

## iAdvize

<p align="center">
<a target="_blank" href="https://vimeo.com/121470910"><img style="width:100%" src="https://i.vimeocdn.com/video/509763980.png?mw=638&mh=1080&q=70"></a>
</p>

I work at [iAdvize](iadvize.com) as a Lead Developer and Architect. iAdvize is the **leading real-time customer engagement platform in Europe** and is used in 40 different countries. We are one of the french startup with the [fastest growth](http://www.iadvize.com/fr/wp-content/uploads/sites/2/2014/11/CP-Fast-50.pdf) and one of [the **greatest place to work** in **France**](https://vimeo.com/122438055).

We are looking for a [**NodeJS backend developer**](http://smrtr.io/FqP79g), a [Scala backend developer](http://smrtr.io/FqP79g), a [**JavaScript frontend developer**](http://smrtr.io/wR-y4Q), a [Full-stack Developer](http://smrtr.io/SGhrew) and a [DevOps System Engineer](http://smrtr.io/OIFFMQ) in Paris or Nantes. **[Send me a tweet](https://twitter.com/FGRibreau) if you have any questions**!

## [The Story](http://blog.fgribreau.com/2015/12/match-when-pattern-matching-for-modern.html)

## [Changelog](/CHANGELOG.md)
