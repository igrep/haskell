##継続

> 文献を紐解くと、 継続とは「これから行われるであろう計算をパッケージ化したもの」とある。

出典: [なんでも継続](http://practical-scheme.net/docs/cont-j.html)

* [Representing Monads](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.43.8213)

継続は一般的な概念であるがここではHaskellの継続渡しスタイルとCont Monadについて説明する

```haskell
newtype Cont r a = Cont { runCont :: (a -> r) -> r }
```

###call/cc

```haskell
callCC :: ((a -> Cont r b) -> Cont r a) -> ContT r a
callCC f = Cont $ \c -> runCont (f (\a -> Cont $ \_ -> c a)) c
```

####例) 無限ループからの脱出

```haskell
import Control.Monad.Cont

main :: IO ()
main = do
  putStrLn "Start"
  withBreak $ \break ->
    forM_ [1..] $ \i -> do
      liftIO . putStrLn $ "Loop: " ++ show i
      when (i == 5) $ break ()
  putStrLn "End"
  where
    withBreak = flip runContT pure . callCC
```

* [無限ループから抜け出すプログラム](http://qiita.com/lotz/items/a1ff5725e918e216940e)

###shift/reset
* [shift/reset プログラミング入門](http://pllab.is.ocha.ac.jp/~asai/cw2011tutorial/main-j.pdf)

```haskell
-- shift/reset for the Cont monad
shift :: ((a -> Cont s r) -> Cont r r) -> Cont r a
shift e = Cont $ \k -> e (return . k) `runCont` id
 
reset :: Cont a a -> Cont r a 
reset e = return $ e `runCont` id
```

出典: [MonadCont done right](https://www.haskell.org/haskellwiki/MonadCont_done_right)

###継続渡しスタイル
* [CPS というプログラミングスタイルの導入の話](http://yuzumikan15.hatenablog.com/entry/2015/04/24/094610)
* [The Mother of all Monads](http://blog.sigfpe.com/2008/12/mother-of-all-monads.html)
* [Control.Monad.Cont](https://hackage.haskell.org/package/mtl/docs/Control-Monad-Cont.html)
* [Haskell/Continuation passing style](http://en.wikibooks.org/wiki/Haskell/Continuation_passing_style)
* [Compiling With CPS](http://jozefg.bitbucket.org/posts/2015-04-30-cps.html)
* [Haskell で継続渡しスタイル (CPS)](http://jutememo.blogspot.jp/2011/05/haskell-cps.html)
* [The mtl-c package](https://hackage.haskell.org/package/mtl-c)
* [Resource Management in Haskell](http://aherrmann.github.io/programming/2016/01/04/resource-management-in-haskell/)

```haskell
-- Another junior Haskell programmer
fac 0 = 1
fac n = n * fac (n-1)

-- Continuation-passing Haskell programmer
facCps k 0 = k 1
facCps k n = facCps (k . (n *)) (n-1)

fac = facCps id
```

出典: [The Evolution of a Haskell Programmer](http://www.willamette.edu/~fruehr/haskell/evolution.html)

###CPSからContモナドへ
* [継続モナドによるリソース管理](http://qiita.com/tanakh/items/81fc1a0d9ae0af3865cb)

```haskell
type Radius = Double
type Area   = Double

suspendArea :: Radius -> (Area -> r) -> r
suspendArea r = \k -> k (pi * r * r)

type Height = Double
type Volume = Double
suspendExtrude :: Area -> Height -> (Volume -> r) -> r
suspendExtrued a h = \k -> k (a * h)

suspendVolume :: Radius -> Height -> (Volume -> r) -> r
suspendVolume r h = \k ->
                        suspendArea r $ \area ->
                            (suspendExtrude area h) k
```

ここに2つのCPSを組み合わせるパターンを見いだせる

```haskell
chain :: ((a -> r) -> r) -> (a -> ((b -> r) -> r)) -> ((b -> r) -> r)
chain f g = \k ->
                f $ \a ->
                    (g a) k
```

これは`(a -> r) -> r`をモナドとした見た時`(>>=)`になる

```haskell
newtype Cont r a = Cont {runCont :: (a -> r) -> r}

instance Functor (Cont r) where
    fmap f c = Cont $ \k -> runCont c (k . f)

instance Applicative (Cont r) where
    pure x = Cont $ \k -> k x
    m <*> k = Cont $ \c ->
                  runCont m $ \f ->
                      runCont k $ \a ->
                          c (f a)

instance Monad (Cont r) where
    return = pure
    m >>= k = Cont $ \c ->
                  runCont m $ \a ->
                      runCont (k a) $ \b ->
                          c b
```
参考: [Continuation Passing Style in Haskell](http://begriffs.com/posts/2015-06-03-haskell-continuations.html)

###継続による計算の効率化
継続を使って短絡評価が実装できる  
参考: [Continuation Passing Style in Haskell](http://begriffs.com/posts/2015-06-03-haskell-continuations.html)

```haskell
prod :: (Eq a, Num a) => [a] -> a
prod l = (`runCont` id) $ callCC (\k -> loop k l)
    where
    loop _ []     = return 1
    loop k (0:_)  = k 0
    loop k (x:xs) = do
        n <- loop k xs
        return (n * xs)
```

末尾再帰

```haskell
fact :: Int -> (Int -> r) -> r
fact 0 f = f 1
fact n f = fact (n-1) (f . (n*))

-- 一般に
toc :: a -> (b -> r) -> r
toc 基底部
toc a f = let g b = 処理
          in toc a (f . g)
```

###米田埋め込み

> The Yoneda embedding is familiar in category theory. The continuation passing transform is familiar in computer programming.
> They’re the same thing! Why doesn’t anyone ever say so?

出典: [The Continuation Passing Transform and the Yoneda Embedding](https://golem.ph.utexas.edu/category/2008/01/the_continuation_passing_trans.html)

CPSへの変換は米田埋め込みに対応している

```haskell
cps :: forall a b r. (a -> b) -> ((b -> r) -> (a -> r))
cps = flip (.)
```

出典:[CPS（継続渡し方式）変換をJavaScriptで説明してみるべ、ナーニ、たいしたことねーべよ](http://d.hatena.ne.jp/m-hiyama/20080116/1200468797)

###論理学での継続

> CPS変換は、二重否定による古典論理の直観主義論理への埋め込みにあたる。

出典: [継続渡しスタイル - Wikipedia](http://ja.wikipedia.org/wiki/%E7%B6%99%E7%B6%9A%E6%B8%A1%E3%81%97%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB)

* [Curry-Howard Isomorphism](http://www.kmonos.net/wlog/61.html#_0538060508)
