---
theme : "black"
transition : "default"
---

# ファンクターと関数合成

---

## ファンクターとは

箱の中にある値を移す(map)もの

---

## ファンクターである条件

1. `fmap id = id`
1. `fmap (f . g) = fmap f . fmap g`

```haskell
id :: a -> a
id x = x
```

---

## ファンクターを作ってみる

型クラス Functor を定義し、リストをそのインスタンスにする

```haskell
class  Functor f  where
  fmap :: (a -> b) -> f a -> f b

instance Functor [] where
  fmap f [] = []
  fmap f (x:xs) = f x : (fmap f xs)
```

これがファンクター則を満す確認する

--

## `fmap id = id`

まずは一つめの `fmap id = id` が満たされていることを確する

```haskell
fmap f [] = []
fmap f (x:xs) = f x : (fmap f xs)
```

```haskell
fmap id [1, 2, 3]
= fmap id (1 : [2, 3])
= id 1 : fmap id [2, 3]
= 1 : fmap id (2 : [3])
= 1 : (id 2) : fmap id [3]
= 1 : 2 : fmap id (3 : [])
= 1 : 2 : (id 3) : fmap id []
= 1 : 2 : 3 : []
= [1, 2, 3]
= id [1, 2, 3]
```

--

## `fmap (f . g) = fmap f . fmap g`

```haskell
f :: Int -> Int
f x = x * 2

g :: Int -> Int
g x = x + 3
```
```haskell
fmap (f . g) [1, 2, 3]
= fmap ((\x -> x * 2) . (\x -> x + 3)) [1, 2, 3]
= fmap (\x -> (x + 3) * 2) [1, 2, 3]
= [8, 10, 12]
```

```haskell
fmap f . fmap g [1, 2, 3]
= fmap (\x -> x * 2) . fmap (\x -> x + 3) [1, 2, 3]
= fmap (\x -> x * 2) [4, 5, 6]
= [8, 10, 12]
```

---

## 関数合成

```haskell
(.) :: (b -> c) -> (a -> b) -> (a -> c)
(.) f g x = f (g x)
```

```haskell
f x = x * 2
g x = x + 3

(f . g) 10 = 26
```

--

## 関数合成の型をよく見てみる

```haskell
(.) :: (b -> c) -> (a -> b) -> (a -> c)
```

`(->)` という型コンストラクタがあると考えると

```haskell
(.) :: ((->) b c) -> ((->) a b) -> ((->) a c)
```

と見れる。
`R = (->)` a とおいてみる。

```haskell
(.) :: (b -> c) -> R b -> R c
```

--

## 関数合成とファンクター

fmap の型と比べると同じ形をしていることがわかる

```haskell
(.)   :: (b -> c) -> R b -> R c
fmap  :: (a -> b) -> f a -> f b
```

つまり `(.) = fmap`

---

## Haskell のファンクター

```haskell
Prelude> :i Functor
class Functor (f :: * -> *) where
  fmap :: (a -> b) -> f a -> f b
  (<$) :: a -> f b -> f a
  {-# MINIMAL fmap #-}
        -- Defined in ‘GHC.Base’
instance Functor (Either a) -- Defined in ‘Data.Either’
instance Functor [] -- Defined in ‘GHC.Base’
instance Functor Maybe -- Defined in ‘GHC.Base’
instance Functor IO -- Defined in ‘GHC.Base’
instance Functor ((->) r) -- Defined in ‘GHC.Base’
instance Functor ((,) a) -- Defined in ‘GHC.Base’
```
