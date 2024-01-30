# MinimumQuotaFinder
Co-created by [@Luesewr](https://github.com/Luesewr) and [@riceisacereal](https://github.com/riceisacereal)\
[Thunderstore](https://thunderstore.io/c/lethal-company/p/PotatoCoders/MinimumQuotaFinder/) | [Github](https://github.com/riceisacereal/MinimumQuotaFinder)

This mod calculates and highlights the minimum total value of scraps that you can sell to still reach the quota. **The default key to toggle the highlighting is `H`**, but this can be changed in the settings.

The same in essence as: [ScrapCalculator](https://thunderstore.io/c/lethal-company/p/granny/ScrapCalculator/)\
Scrap auto-selling mods: [SellMyScrap](https://thunderstore.io/c/lethal-company/p/Zehs/SellMyScrap/), [SellFromTerminal](https://thunderstore.io/c/lethal-company/p/stormytuna/SellFromTerminal/)

## ✨ 𝓦𝓱𝓪𝓽 𝓸𝓾𝓻 𝓶𝓸𝓭 𝓭𝓸𝓮𝓼 𝓫𝓮𝓽𝓽𝓮𝓻
- Always finds the most optimal answer quickly (5ms to 1s depending on quota and number of items) using an [optimized algorithm](#explanation-of-algorithm).
- Cool highlighting shader

## How the mod works
- **Press H to toggle the highlighting/recalculate**
  - The keybind can be changed in the settings.
- When you're on the company moon, all scrap in the environment is taken into account (including scrap on the counter), unless **its y-value is under -30** (somehow dropped over the railings). Otherwise only scrap within the ship is considered.
  - Pros: You can take items outside the ship on the company moon and they will still be included in the calculation.
- Calculation assumes that you are selling when the company is buying at 100% (it's too complicated otherwise due to rounding per batch requiring you to sell in a specific order of batches, and we don't want to go down that road).
- This is a client-side mod, which means that there is a small chance of different items being highlighted for different people. We have several methods in place to prevent this from happening, but you never know.
  - If this happens, report an issue and tell us how it happened.

## Explanation of Algorithm
### The problem
The problem this mod tries to solve - finding the most optimal combination of scrap that is the closest to the quota, is a variant of the [subset sum problem](https://en.wikipedia.org/wiki/Subset_sum_problem) and has a computational complexity of [NP-Hard](https://en.wikipedia.org/wiki/NP-hardness), which in a very generalized way of saying means that there is no easy formula to get the answer right away and that we have to find it by "trying every combination".

The number of possible combinations for all the scrap you have grows [exponentially](https://math.stackexchange.com/a/3788314), by the time you have 20 pieces of scrap you will have to check over 1 million combinations, and 1 billion when you have 30 pieces of scrap. When you're up to 5k+ quota it will take half your lifetime to check all combinations with even if you check 1 million combinations per second.

Algorithms for NP-hard problems utilize ways to [ignore certain combinations](https://en.wikipedia.org/wiki/Decision_tree_pruning), and [storing the results of calculations that are repeated multiple times](https://en.wikipedia.org/wiki/Memoization). An example of the former would be, if you have a quota of 1000, and we know that the highest value a scrap can have is 210, we could ignore any combinations that have less than 5 pieces of scrap, since they would never reach the quota (4 * 210 = 840 < 1000).

### The Algorithm
The algorithm we've implemented is very similar to the solution to the [0-1 knapsack problem](https://en.wikipedia.org/wiki/Knapsack_problem#0-1_knapsack_problem) which uses dynamic programming and memoization. The only difference is that the knapsack problem tries to find the maximum <= a threshold, and since we need the minimum of >= quota, we ran the knapsack solution on a threshold of (total value of scrap owned - quota) to find what we should *exclude*. (Thanks to [this](https://stackoverflow.com/a/17181101) Stackoverflow answer for the idea.)

### Computation Time Scaling
The 2 factors that determine how long it takes to calculate an answer are the value of the quota, the number of scrap, and the total value of that scrap. The algorithm goes through a table the size of (total value of scrap owned - quota) * number of scrap, so the bigger the difference between your quota and the total value of all your scrap, and the more scrap you have, the longer it takes to calculate.

### Performance Impact
To prevent the game from freezing while doing large calculations, the mod makes use of [coroutines](https://docs.unity3d.com/Manual/Coroutines.html). These coroutines spread out the calculations over multiple frames and limit the number of computations ran per frame for a smoother user experience. 

## Credits
This mod was built using the [BepInEx mod template](https://docs.bepinex.dev/articles/dev_guide/plugin_tutorial/2_plugin_start.html). Part of [ShipLoot mod](https://github.com/tinyhoot/ShipLoot)'s code was taken as a starting point. [InputUtils](https://thunderstore.io/c/lethal-company/p/Rune580/LethalCompany_InputUtils/) was used to make the keybinding. This [Wireframe Shader](https://gist.github.com/baba-s/14ecab2df06188a083e66ab00b2c9499) was used as a basis for the custom wireframe shader.

## Installation
[Thunderstore link](https://thunderstore.io/c/lethal-company/p/PotatoCoders/MinimumQuotaFinder/)
