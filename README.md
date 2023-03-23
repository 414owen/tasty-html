tasty-html
==========

HTML test reporter for the Tasty test framework.


## Example

Here's how your `test.hs` might look like:

```haskell
import Test.Tasty
import Test.Tasty.SmallCheck as SC
import Test.Tasty.QuickCheck as QC
import Test.Tasty.HUnit
import Test.Tasty.Runners.Html

import Data.List
import Data.Ord

main = defaultMainWithIngredients (htmlRunner:defaultIngredients) tests

tests :: TestTree
tests = testGroup "Tests" [properties, unitTests]

properties :: TestTree
properties = testGroup "Properties" [scProps, qcProps]

scProps = testGroup "(checked by SmallCheck)"
  [ SC.testProperty "sort == sort . reverse" $
      \list -> sort (list :: [Int]) == sort (reverse list)
  , SC.testProperty "Fermat's little theorem" $
      \x -> ((x :: Integer)^7 - x) `mod` 7 == 0
  -- the following property does not hold
  , SC.testProperty "Fermat's last theorem" $
      \x y z n ->
        (n :: Integer) >= 3 SC.==> x^n + y^n /= (z^n :: Integer)
  ]

qcProps = testGroup "(checked by QuickCheck)"
  [ QC.testProperty "sort == sort . reverse" $
      \list -> sort (list :: [Int]) == sort (reverse list)
  , QC.testProperty "Fermat's little theorem" $
      \x -> ((x :: Integer)^7 - x) `mod` 7 == 0
  -- the following property does not hold
  , QC.testProperty "Fermat's last theorem" $
      \x y z n ->
        (n :: Integer) >= 3 QC.==> x^n + y^n /= (z^n :: Integer)
  ]

unitTests = testGroup "Unit tests"
  [ testCase "List comparison (different length)" $
      [1, 2, 3] `compare` [1,2] @?= GT

  -- the following test does not hold
  , testCase "List comparison (same length)" $
      [1, 2, 3] `compare` [1,2,2] @?= LT
  ]
```

To produce the HTML output, run the test program with the `--html` option,
giving it the html file path:

```
./test --html results.html
```

Here is the output of the above program rendered to HTML:

![](https://user-images.githubusercontent.com/1714287/226591356-ee9d55cf-cc7e-454c-a3c6-e3f6fb3ac35a.png)

(Note that whether QuickCheck finds a counterexample to the third property is
determined by chance.)

## Hacking

```
$ git clone --recursive https://github.com/feuerbach/tasty-html
$ cabal test tasty-html-pass --test-options='--html pass.html'
$ cabal test tasty-html-fail --test-options='--html fail.html'
$ firefox pass.html fail.html
```
