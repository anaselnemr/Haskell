# Trigram Text Generator — Concepts of Programming Languages (GUC)

A small Haskell project that builds a **trigram language model** from a fixed text corpus and generates new sentences using conditional probabilities over word triples. Coursework for *Concepts of Programming Languages* at the German University in Cairo (GUC), ~Jan 2022.

## What it does

Given a corpus of strings (`docs` in `DataFile.hs` — short articles about Belgian municipalities plus a karyotype entry), the program:

1. **Tokenizes** each string into words, splitting on whitespace and treating punctuation (`! # $ % & , . : ; ? @ \` | ~`) as its own tokens.
2. Extracts every **unique bigram** `(w1, w2)` and **unique trigram** `(w1, w2, w3)` that appears in the corpus.
3. Counts the **frequency** of each bigram and trigram.
4. Derives a **conditional probability** for every trigram: `P(w3 | w1, w2) = freq(w1,w2,w3) / freq(w1,w2)`.
5. Generates the **next word** after a seed phrase by looking up the seed's last bigram, collecting all trigrams whose probability exceeds `0.03`, and picking one at random.
6. Repeats step 5 to **generate text** of any requested length.

## Topics Covered

- Pattern-matching and guarded equations
- Recursion with explicit accumulators (no `where`/`let` — uses helper functions with extra args)
- List manipulation (`map`, `foldr`, `(!!)`, `last`)
- Tuples (`(String, String)` bigrams, `(String, String, String)` trigrams)
- Type-class constraints (`Eq`, `Num`, `Ord`, `Fractional`)
- Module imports and separation (`DataFile` provides the corpus + RNG, `Team43` provides the model)
- Side effects via `unsafePerformIO` for randomness

## File Index

| File | Role |
|---|---|
| `Team43.hs` | Main module — tokenizer, n-gram extraction, frequency tables, probability computation, and the generator pipeline (`generateText`). |
| `DataFile.hs` | Corpus (`docs`), punctuation set (`punct`), and `randomZeroToX` random-index helper using `System.Random` + `unsafePerformIO`. |

## Pipeline (top-level functions in `Team43.hs`)

| Function | Signature | Purpose |
|---|---|---|
| `wordToken` | `String -> [String]` | Split one string into words + punctuation tokens. |
| `wordTokenList` | `[String] -> [String]` | Tokenize an entire corpus, flattened. |
| `uniqueBigrams` | `[String] -> [(String,String)]` | Distinct adjacent-word pairs. |
| `uniqueTrigrams` | `[String] -> [(String,String,String)]` | Distinct adjacent-word triples. |
| `bigramsFreq` | `[String] -> [((String,String), a)]` | Frequency table of bigrams. |
| `trigramsFreq` | `[String] -> [((String,String,String), a)]` | Frequency table of trigrams. |
| `genProbPairs` | trigram-freqs → bigram-freqs → trigram-probabilities | Conditional probability for every trigram. |
| `generateNextWord` | seed → trigram-probs → next word | Random word above the 0.03 probability threshold. |
| `generateText` | `String -> Int -> String` | Generate `n` more words after the seed. |

## How to Run

You need [GHC](https://www.haskell.org/ghc/) installed.

```bash
# Load both files into GHCi and try the generator interactively
ghci Team43.hs

# In the GHCi prompt:
ghci> generateText "the man" 5
"the man is a municipality in"
```

Or compile to an executable (you'll need to add a `main` first — none ships in this repo since the assignment was REPL-driven):

```bash
ghc Team43.hs -o trigram
./trigram
```

Notes:
- The corpus inside `DataFile.hs` is hard-coded; edit the `docs` list to retrain on different text.
- `generateNextWord` calls `error "Sorry, it is not possible to infer from current database"` if no trigram following the seed's last bigram clears the 0.03 probability threshold — pick a seed that actually appears in `docs`.

## Coursework Context

Submitted for *Concepts of Programming Languages* (CPL) at the German University in Cairo (GUC), Spring 2022. The "Team43" prefix in the main file is the assignment team identifier.

## Authors

- Anas ElNemr
- Ahmed Eltawel

## Acknowledgements

Friendly mirror of [github.com/ahmedeltawel/Haskell](https://github.com/ahmedeltawel/Haskell) — joint coursework, both copies preserved.
