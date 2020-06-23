# Codebreaker
Simple text-based Mastermind with an implementation of Knuth's solver. Can play manually or auto-solve.

## Version
- v1.0 - June 22, 2020
- macOS, Linux, Windows
- [MIT License](LICENSE)
- By Abe Pralle

## Building
1. Install the [Rogue](https://github.com/AbePralle/Rogue) language.
2. Run `rogo` in this folder to compile and run **Codebreaker**, then choose `play` or `solve`.
3. You can also run `rogo play` or `rogo solve`.

## Example Output

    > rogo
    > Build/Codebreaker-macOS
    1296 solution candidates
    Guess 1: 1122
             +
             Removing 1039 possible solutions that would not give the same score for the current guess.
             256 possible solutions remain.
    Guess 2: 1344

             Removing 239 possible solutions that would not give the same score for the current guess.
             16 possible solutions remain.
    Guess 3: 5525
             +++
             Removing 12 possible solutions that would not give the same score for the current guess.
             3 possible solutions remain.
    Guess 4: 1516
             --
             Removing 2 possible solutions that would not give the same score for the current guess.
             1 possible solution remains.
    Guess 5: 5625
             ++++
    Game won!

## Algorithm

One description of Knuth's algorithm is available on [Wikipedia](https://en.wikipedia.org/wiki/Mastermind_(board_game)#Worst_case:_Five-guess_algorithm).

Here's the algorithm in pseudocode.

    solution = XXXX  # 1111..6666
    U = all 1,296 possible patterns (6^4)  # set of all Unused guesses
    S = U.cloned   # possible Solutions
    next_guess = 1122
    while (next_guess != solution)
      U.remove( next_guess )
      if (S.contains(next_guess)) S.remove(next_guess)  # not all guesses are possible solutions due to algorithm
      score = solution.score_guess( next_guess )
      if (score.is_win) escapeWhile

      S.remove( any element where e.score_guess(next_guess) != score )

      next_guess = find_next_best_guess
    endWhile

    method find_next_best_guess
      hit_count = {}
      best_guesses = []
      best_score = 0

      forEach (pattern in U)
        forEach (guess in S)
          ++hit_count[ pattern.score(guess) ]
        endForEach

        score = S.count - hit_count.max
        if (score > best_score)      best_score = score; best_guesses = [pattern]
        elseIf (score == best_score) best_guesses.add( pattern )
      endForEach

      # Look for a best guess that also occurs in possible solutions.
      # The best guess may not be a possible solution but it will help us solve
      # the code the fastest.
      forEach (guess in best_guesses)
        if (S.contains(guess)) return guess
      endForEach

      return best_guesses.first

