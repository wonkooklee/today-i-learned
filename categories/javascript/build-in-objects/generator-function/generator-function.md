# Why can you use generator functions for?

## Keywords
- Lazy evaluation: 지연 평가

> 컴퓨터 프로그래밍에서 느긋한 계산법(Lazy evaluation)은 계산의 결과값이 필요할 때까지 계산을 늦추는 기법이다. 두 가지 관련된 항목들이 있는데 지연 계산법과 최소 계산법이다.

## 1.

Generators give you lazy evaluation. You use them by iterating over them, either explicitly with 'for' or implicitly by passing it to any function or construct that iterates. You can think of generators as returning multiple items, as if they return a list, but instead of returning them all at once they return them one-by-one, and the generator function is paused until the next item is requested.

제너레이터 함수는 지연된 평가(lazy evaluation)을 제공한다. `for`문 같은 명시적 반복문 또는 순회하는 생성자 또는 함수를 이용해서 반복 작업을 수행한다. 제너레이터가 다양한 요소를 반환하는 것이라 생각할 수도 있는데, 대신에 한 번에 반환하는 대신 다음 항목에 대한 요청이 들어올때까지 하나씩 일시 반환을 보류하는 것이라고 생각할 수 있다.

Generators are good for calculating large sets of results (in particular calculations involving loops themselves) where you don't know if you are going to need all results, or where you don't want to allocate the memory for all results at the same time. Or for situations where the generator uses another generator, or consumes some other resource, and it's more convenient if that happened as late as possible.

제너레이터는 많은 양의 데이터를 산출해야 하지만 (특히 자체적으로 루프를 계산하는), 메모리에 할당하기에는 마땅치 않을 경우 사용한다. 또는 제너레이터가 다른 제너레이터를 사용하여 다른 리소스를 소비하는 상황이 가능한 늦게 발생되도록 편의를 제공한다.

Another use for generators (that is really the same) is to replace callbacks with iteration. In some situations you want a function to do a lot of work and occasionally report back to the caller. Traditionally you'd use a callback function for this. You pass this callback to the work-function and it would periodically call this callback. The generator approach is that the work-function (now a generator) knows nothing about the callback, and merely yields whenever it wants to report something. The caller, instead of writing a separate callback and passing that to the work-function, does all the reporting work in a little 'for' loop around the generator.

For example, say you wrote a 'filesystem search' program. You could perform the search in its entirety, collect the results and then display them one at a time. All of the results would have to be collected before you showed the first, and all of the results would be in memory at the same time. Or you could display the results while you find them, which would be more memory efficient and much friendlier towards the user. The latter could be done by passing the result-printing function to the filesystem-search function, or it could be done by just making the search function a generator and iterating over the result.

If you want to see an example of the latter two approaches, see os.path.walk() (the old filesystem-walking function with callback) and os.walk() (the new filesystem-walking generator.) Of course, if you really wanted to collect all results in a list, the generator approach is trivial to convert to the big-list approach:

```js
big_list = list(the_generator)
```

<br />

## 2.

One of the reasons to use generator is to make the solution clearer for some kind of solutions.

The other is to treat results one at a time, avoiding building huge lists of results that you would process separated anyway.

If you have a fibonacci-up-to-n function like this:

```js
# function version
def fibon(n):
    a = b = 1
    result = []
    for i in xrange(n):
        result.append(a)
        a, b = b, a + b
    return result
```
You can more easily write the function as this:

```js
# generator version
def fibon(n):
    a = b = 1
    for i in xrange(n):
        yield a
        a, b = b, a + b
```
The function is clearer. And if you use the function like this:

```js
for x in fibon(1000000):
    print x,
```
in this example, if using the generator version, the whole 1000000 item list won't be created at all, just one value at a time. That would not be the case when using the list version, where a list would be created first.

<br /><br /><br />

## References
- [What can you use generator functions for? - Stackoverflow](https://stackoverflow.com/questions/102535/what-can-you-use-generator-functions-for)