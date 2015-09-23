##環境構築
* [The Haskell User Experience](http://rickdzekman.com/thoughts/the-haskell-user-experience/)

```bash
$ brew install ghc cabal-install
```

* [OS X](http://ghcformacosx.github.io/)
* [Windows](https://github.com/fpco/minghc)
* [Linux](https://www.haskell.org/downloads/linux)

----

* [hlint](https://hackage.haskell.org/package/hlint)
* [tonsky/FiraCode](https://github.com/tonsky/FiraCode)

##Example

**エラトステネスの篩**

```haskell
primes = sieve [2..]
sieve (p:xs) = p : sieve [x | x <- xs, x `mod` p /= 0]

-- ghci> primes
-- [2,3,5,7,11,13,17,19,23,..
```

* [The Genuine Sieve of Eratosthenes](http://www.cs.hmc.edu/~oneill/papers/Sieve-JFP.pdf)
* [Haskell programmers are liars](http://www.garrisonjensen.com/2015/05/13/haskell-programs-are-lies.html)
* [An Optimal Haskell Quicksort](https://gautamcgoel.wordpress.com/2015/08/27/an-optimal-haskell-quicksort/)

##Haskell入門

> 関数プログラミングを習得するには，これまで命令プログラミングで培った技術はいったん忘れ，真っ白な気持ちで臨む必要があります。関数型の山を登るためには，命令型の山を降りなければなりません。

出典: [第1章　関数プログラミングは難しくない！](http://gihyo.jp/dev/feature/01/functional-prog/0001)

* [Try Haskell](https://tryhaskell.org/)
* [Introduction to functional programming with Haskell](https://ocramz.github.io/haskell/tutorials/2015/08/21/haskell-tut-v1.html)
* [ウォークスルー Haskell](http://walk.wgag.net/haskell/)
* [Haskell 超入門](http://qiita.com/7shi/items/145f1234f8ec2af923ef)
* [99 Haskell](http://www.99haskell.org/)
* [Haskellチートシート(翻訳)](http://qiita.com/techno-tanoC/items/1fa1c65db08da2440fc4)
* [PLEAC-Haskell](http://pleac.sourceforge.net/pleac_haskell/index.html)
* [Real World Haskell](http://book.realworldhaskell.org/read/)
* [What I Wish I Knew When Learning Haskell](http://www.stephendiehl.com/what/) [[ja](https://github.com/Kinokkory/wiwinwlh-jp/wiki)]
* [参照透過性とは何だったのか](http://www.slideshare.net/RuiccRail/haskell-day2012)
* [VimでHaskellしよう](http://qiita.com/yukiasai/items/92a2b343b6519b38f402)

##Editor
* [Leksah](http://leksah.org/)
* [Haskell for Mac](http://haskellformac.com/)
* [EclipseFP](http://eclipsefp.github.io/)
* [Switching from Vim to Atom (A Haskeller's Perspective)](http://edsko.net/2015/03/07/vim-to-atom/)

##基本
* [Base で定義されている演算子の一覧](http://hackage.haskell.org/package/acme-operators/docs/Acme-Operators-Base.html)
* [順列列挙関数（素朴な実装）](http://qiita.com/nobsun/items/babd28fe81ba3b9f304f)

###型と関数
```haskell
-- 型の宣言
fac :: Int -> Int
-- 関数の実装
fac 0 = 1
fac n = n * fac (n-1)
```

* [Partial Function Considered Harmful](http://tanakh.jp/posts/2011-12-25-partial-function-considered-harmful.html)

###パターンマッチ
* [Pattern matching](http://mbps.hatenablog.com/entry/2014/10/04/010000)
* [PATTERN MATCHING: WOT'S... UH THE DEAL?](http://www.stackbuilders.com/news/pattern-matching-wot-s-uh-the-deal)

###多変数関数とカリー化
* [Curry](https://ro-che.info/ccc/10)
* [Loop School - Curring](http://school.looprecur.com/?video=122330958)

```haskell
curry :: ((a, b) -> c) -> a -> b -> c
uncurry :: (a -> b -> c) -> (a, b) -> c
```

> Haskell 初心者は括弧ばかりの Lisp のようなコードを書く。中級者になると、($) が多くなる。上級者(言い過ぎか？)になると、($) が消えて、(.) が多くなる。

出典: [関数合成の妙技](http://d.hatena.ne.jp/kazu-yamamoto/20100702/1278036842)

###ポイントフリー
必要のないところでポイントワイズに書く必要はないが、あまりポイントフリーに拘りすぎないのも大事

* [ポイントフリースタイル入門](http://d.hatena.ne.jp/melpon/20111031/1320024473)
* [Haskellのフクロウ ((.)$(.))](http://uid0130.blogspot.jp/2014/11/haskell_17.html)
* [Blunt](https://blunt.herokuapp.com/)
  * [pointfree](https://hackage.haskell.org/package/pointfree)

##遅延評価
* [Schrödinger's cat](https://ro-che.info/ccc/4)
* [Résumé](https://ro-che.info/ccc/11)

> One very interesting comparison is that lazy evaluation is to the CPU what garbage collection is to memory.

出典: <http://scott.sauyet.com/Javascript/Talk/2014/01/FuncProgTalk/#slide-40>

**遅延評価を利用したメモ化の実装例**

```haskell
fibs :: [Integer]
fibs = map fib [0..]

fib :: Int -> Integer
fib 0 = 1
fib 1 = 1
fib n = fibs !! (n-1) + fibs !! (n-2)
```

* [The Incomplete Guide to Lazy Evaluation (in Haskell)](https://hackhands.com/guide-lazy-evaluation-haskell/)
* [If Haskell were strict, what would the laziness be like?](http://nikita-volkov.github.io/if-haskell-were-strict/)
* [How to force a list](https://ro-che.info/articles/2015-05-28-force-list)
* [Thinking with Laziness](http://begriffs.com/posts/2015-06-17-thinking-with-laziness.html)
* [How Laziness Works](http://two-wrongs.com/how-laziness-works)
* [Haskell memoization](http://lukahorvat.github.io/programming/2014/11/18/haskell-memoization/)

###Others
* [Generalizing function composition](http://jaspervdj.be/posts/2014-10-17-generalizing-function-composition.html)
* [Haskell Symposium 2012. Koen Claessen: Shrinking and showing functions](https://www.youtube.com/watch?v=CH8UQJiv9Q4)
* [Making a fast curry Push/enter vs eval/apply for higher-order languages](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.134.9317&rep=rep1&type=pdf)
* [The Essence of the Iterator Pattern](https://www.cs.ox.ac.uk/jeremy.gibbons/publications/iterator.pdf)
* [A tutorial on the universality and expressiveness of fold](http://www.cs.nott.ac.uk/~gmh/fold.pdf)
* [A Pragmatic Case for Static Typing with Brian Hurt](https://vimeo.com/72870631)
* [Haskell Functions Take One Argument](http://tonymorris.github.io/blog/posts/haskell-functions-take-one-argument/)