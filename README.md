# Automated Correction of Incorrectly Recognised Text

## Authors -  Ananya Mathur (2020CS50416), Ishaan Govil (2020CS50497)

### INTRODUCTION
In this assignment to tackle the problem of misidentification of characters by the OCR system, we implement a search based algorithm which helps us to identify the misplaced characters in the words in a level wise fashion similar to iterative deepening, based on how much error is present in that word. The words would be replaced based on the cost function, on the basis whether the cost of the sentence is getting reduced after replacement, following local updation. 

---

### CORE IDEAS 
1. Precomputation of conf_matrix_inv - using the given conf_matrix in order to prevent exhaustive repeated search of each entry of conf_matrix for each character replacement.
2. Hill climbing search/Greedy updation - At each step of our algorithm, we are exploring a particular finite subset of the whole possible state space(based on evrey word) and then updating the word if we encounter a word that lowers our total cost greedily, similar to that in hill climbing search, instead of maintaining a list/frontier of possible correct sentences
3. Iterative Deepening - A level wise DFS is performed in our algorithm, similar to iterative deepening, where we increase the no.of replacements we are aiming for, in each word that we are exploring. I.e if we are at a level 2 DFS, then we have considered changes to 2 characters of the word being explored.
4. Depth first exhaustive search - A Level 1 and Level 2 exhaustive DFS is performed on each word, in the start of our algorithm,(this is possible due to the less number of possible state space in Level 2 DFS of a word) to ensure that all the words having minimal errors are surely corrected
5. Depth first non-exhaustive/random search - A fully exhaustive Level 3, Level 4 and Level 5 DFS is not possible on each word due to the large state space and so we randomize this process, selecting different indices at random so that, we may also deal with the words having errors in the last few characters.
6. Greedy word selection - To determine the word we are going to explore incase of non-exhaustive random DFS, we are going to make use of the heuristic cost function, and for each word of appropriate length, going to consider the cost of some phrases of the overall sentence of which that word is a part of. Finally we are going to sum up the costs of all these sentences for each word (our heuristic), and consider the word for which the sum of costs/heuristic is maximum.
7. Change backtracking - Some words might be replaced because of the local optimality check, as well as because of the initial mistakes present in the complete sentence, so a backtracking of change is essential to check if the original words were better for the overall sentence. 
8. Single updation - If a word has been changed in a level 3,4 or a level 5 DFS then we are marking it as changed, and are not going to explore it further. This is done in order to ensure that exploration for all possible words has taken place, in the given time frame, and the search space is not getting constrained to just one word. And after all the words have been explored, we again start exploring all the words again in the new loop.

---

### ALGORITHM
1. Initially we precompute an inverse configuration matrix, computed using the given conf_matrix which stores all the characters that can come in place of the misplaced character, assuming that character is misplaced. Initially all the words are set to not changed state.
2. Then, we begin in a level wise manner, based on the no. of errors a word might have. We first begin by invoking the function singular_change_exhaustive. This function locally explores all possible one character replacements in the words using local cost optimality in words along with local updation to find the optimal state.
3. Next, the function singular_change_complete is invoked. This function checks for global optimality of all one character replacements in all the words in the current best solution.
4. The function double_change_exhaustive is invoked next, which performs exhaustive DFS of level 2 for each word of the sentence and stores optimal solution similar to local search.
5. Singular_change_exhaustive is invoked again, which performs a 1 level local cost DFS on each word and local updation, after a level 2 DFS updation has taken place
6. The function backtrack_change is invoked. This function backtracks the changed words to check if a word has been mistakenly replaced during local optimisation taking place in the level 1 DFS.
7. The Level 3, Level 4 DFS function - triple_quad_change_random is called multiple times before the Level 5 DFS function. This level 3,4 DFS function is non exhaustive depth first random search, which explores states based on a certain heuristic of largest three sentence cost in the current state. It explores both three character and four character replacement states based on the heuristic and randomisation.
8. Finally the Level 5 DFS function penta_change_random is called, which is also non-exhaustive breadth first random search, based on the same heuristic and checks for five character replacements randomly to generate a more optimal state.
9. The functions concerned with Level 3, Level 4 DFS and Level 5 DFS are called repeatedly(that is the steps 7 and 8 are repeated), in order to perform random search on the most incorrect word determined by out heuristic function (update_costs). These functions are non-exhaustive and randomly select the indices, and then explore all possible character replacements for these indices. This is done to ensure that all the corrections are done, as some states have been left unexplored due to the random nature of our Level 3,4 and Level 5 DFS function. The no. of times this loop is run, can be fine tuned based on the total amount of time available to us. Also, at the end of each loop we are again initializing all the words to not changed so that they can be explored further in the next loop.
