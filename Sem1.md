Yes. **The biggest avoidable time cost is in KDTree’s bulk construction.** I haven’t changed any project files.

1. **Indexed access remains in `buildBalanced()`** — [KDTree.java (line 93)](/Users/adambernolak/Code/skola/1-sem/aus2/semka1/src/KDTree/KDTree.java:93)
    
    ```
    data.subList(from, to).sort(...);
    node.data = data.get(middle);
    ```
    
    `data` is one large `LinkedList`. Each `get(middle)` walks from an end of that entire list—even when processing a tiny subtree.
    
    Since every index eventually becomes a selected median, these reads alone total **Θ(n²)**. Accessing sublists adds further traversal overhead.
    
    If you want to keep linked lists, sorting and partitioning **separate lists for each subtree using iterators** would avoid those global indexed reads.
    
2. **Every subtree is sorted again** — same method.
    
    Even after fixing indexed access, repeatedly sorting each subtree gives **O(n log² n)** worst-case construction for a fixed number of dimensions. More advanced median selection can reduce that, but it introduces additional implementation complexity.
    
    You also sort by dimension `0` before calling `buildBalanced()`, then sort the root range by the same comparator again. That second root sort is redundant.
    
3. **Individual KDTree insertion can create a chain** — [KDTree.java (line 118)](/Users/adambernolak/Code/skola/1-sem/aus2/semka1/src/KDTree/KDTree.java:118)
    
    There is no balancing during individual insertion. For example, inserting `(1,1), (2,2), (3,3), ...` creates an increasingly long path.
    
    For fixed dimensions, one insertion can become **O(n)**, and building through repeated insertions can become **O(n²)**. This is a design tradeoff; your balanced bulk constructor avoids the initial imbalance.
    
4. **QuadTree can keep splitting without separating points** — [QuadTree.java (line 112)](/Users/adambernolak/Code/skola/1-sem/aus2/semka1/src/QuadTree/QuadTree.java:112)
    
    Coincident points, or integer intervals that cannot shrink further, can cause repeated splitting until `maxDepth`. Each split creates four nodes, their bounds, and their lists.
    
    Detecting when further subdivision cannot help would avoid that work.
    
5. **QuadTree’s `getAll()` performs unnecessary spatial checks** — [QuadTree.java (line 218)](/Users/adambernolak/Code/skola/1-sem/aus2/semka1/src/QuadTree/QuadTree.java:218)
    
    Calling `find(root.bounds)` checks node intersections and obtains bounds for every stored object. A direct traversal could simply collect their contents.
    
    This improves constant costs and allocations; traversal remains linear in visited nodes plus returned objects.
    

Your queue loops using `removeFirst()` and `add()` are fine: those operations take **O(1)** on `LinkedList`.

I also found **two correctness issues**, confirmed with small diagnostic runs:

- **QuadTree’s east/west quadrants are swapped** in [getQuadrant() (line 131)](/Users/adambernolak/Code/skola/1-sem/aus2/semka1/src/QuadTree/QuadTree.java:131), relative to their bounds in `split()`. With bounds `0..10`, inserting `(2,8)` and `(8,2)`, then searching exactly for `(2,8)`, returns an empty list.
- **KDTree can accept duplicates after bulk construction.** Bulk sorting uses other dimensions to break ties, while individual insertion always goes left when the splitting coordinate is equal. Bulk inserting `(1,1), (1,2), (1,3)` and then inserting `(1,3)` again increases the size to `4`.