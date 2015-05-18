##理想のLazy IOを求めて
* [IO inside](https://www.haskell.org/haskellwiki/IO_inside)

[**The problem**](http://www.scs.stanford.edu/14sp-cs240h/slides/pipes-slides.html#(3))

```haskell
replicateM :: Monad m => Int -> m a -> m [a]
mapM :: Monad m => (a -> m b) -> [a] -> m [b]
sequence :: Monad m => [m a] -> m [a]
```

* Does not work on infinite lists
* You can't consume any results until everything has been processed
* You have to run the entire computation, even if you don't need every result
* This wastes memory by buffering every result

```haskell
main = do
    xs <- sequence . repeat $ getLine
    mapM_ putStrLn xs
    -- 期待通りに動かない例
```

* [How to build library-agnostic streaming sources](http://www.haskellforall.com/2014/11/how-to-build-library-agnostic-streaming.html)

###History
* ✕ Lazy IO
* Deprecated [enumerator](https://hackage.haskell.org/package/enumerator)
* [iteratee](https://hackage.haskell.org/package/iteratee)
* [iterIO](https://hackage.haskell.org/package/iterIO)
* [machines](http://hackage.haskell.org/package/machines)
* [Iteratees](https://ro-che.info/ccc/15)

###io-streams
* [io-streams](http://hackage.haskell.org/package/io-streams)
* <http://yunomu.hatenablog.jp/entry/2013/09/22/160859>

----

> Conduits were created for the Yesod web framework. My understanding is that they were designed to be blazingly fast. Early versions of the library were highly stateful.
> 
> Pipes focus on elegance. They have just one type instead of several, form monad (transformer) and category instances, and are very "functional" in design.

出典: <http://stackoverflow.com/questions/9983840/what-are-the-pros-and-cons-of-enumerators-vs-conduits-vs-pipes>

###Pipes
* [Pipes.Tutorial](https://hackage.haskell.org/package/pipes/docs/Pipes-Tutorial.html)
* [Pipes](http://www.scs.stanford.edu/14sp-cs240h/slides/pipes-slides.html)
* [Pipes - London Haskell](https://www.youtube.com/watch?v=2jdJGdA7AYs)

###Conduit
* <https://hackage.haskell.org/package/conduit>
* [Conduit Overview](https://www.fpcomplete.com/user/snoyberg/library-documentation/conduit-overview)
* [Simpler conduit library based on monadic folds](http://newartisans.com/2014/06/simpler-conduit-library/)
* [５分で分かるコンジット](http://melpon.org/yesodbookjp/conduit)
* [ConduitとHaskellでネットワークプロキシサーバを作る](http://tanakh.jp/posts/2012-07-01-conduit-0.5.html)
* [Conduitの使い方](http://qiita.com/siphilia_rn/items/f3d8d83496a8eab65274)

###例外処理
* [Haskell での例外処理](http://d.hatena.ne.jp/kazu-yamamoto/20120604/1338802792)
* [Haskellでの例外処理(その2)](http://d.hatena.ne.jp/kazu-yamamoto/20120605/1338871044)
* [Exceptions Best Practices](https://www.fpcomplete.com/user/commercial/content/exceptions-best-practices)