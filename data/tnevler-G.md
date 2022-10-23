# Report
## Gas Optimizations ## 

### [G-01]: The increment in for loop post condition can be made unchecked 
**Context:**

1. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L165
2. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L195
3. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274
4. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496
5. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623
6. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L701
7. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L929
8. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75
9. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100
10. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154
11. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177
12. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674
13. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711
14. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L778
15. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L831
16. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L878
17. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L951
18. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90
19. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L163

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-02]: Use calldata instead of memory
**Context:**

1. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261
2. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L467
3. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L468
4. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L469
5. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L618
6. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L688 
7. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L881
8. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54
9. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134
10. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L280
11. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L281
12. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L313
13. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L314
14. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L355
15. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L380
16. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L409
17. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L434
18. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L462
19. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L495
20. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L534
21. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L564
22. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L596
23. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L645
24. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L646
25. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L656
26. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L702
27. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L703
28. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L704
29. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L745
30. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L746
31. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L764
32. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L765
33. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L766
34. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L817
35. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L818
36. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L819
37. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L820
38. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L865
39. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L866 
40. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L867
41. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L152
42. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L153

**Recommendation:**

If a reference type function parameter is read-only, it is recommended to use calldata instead of memory because this provides significant gas savings.