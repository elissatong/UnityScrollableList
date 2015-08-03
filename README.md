# UnityScrollableList
Using Unity 5.0+ OSX version, uGUI elements

In this project, we have a scrollable list of items, recycling a set number of GameObjects when scrolling to last of the list or back to the top of the list. 

PROBLEM:
When creating a scrollable list of 100's or 1000's of items, calling GameObject.Instantiate() for this many items in runtime is slow and bloats up memory. 

DESIGN:
Reuse existing GameObjects for the list. If scrolling to the bottom of the list, move the topmost offscreen item to the bottom of the list. Then when scrolling back to the top of the list, move the bottommost offscreen item back to the top. Continue looping those items depending if you scroll pass the top or pass the bottom.

Sample hierarchy of a vertical scrollable list will look like:

ScrollView: Scroll Rect, Mask, Image
--> ScrollBar
--> Content: Vertical Layout Group, Content Size Fitter (Vertical Fit: min size)
-->--> itemPrefab00: Layout Element (Min Height set)
-->--> itemPrefab01
-->--> itemPrefab02
-->--> itemPrefab03
-->--> itemPrefab04
-->--> itemPrefab05
-->--> itemPrefab06
-->--> itemPrefab07
-->--> itemPrefab08

IMPLEMENTATION:

1. The number of GameObjects itemPrefab's created will be the number needed to cover up the entire scrollable list (+x more items to fit for different possible resolutions).
2. The ScrollBar ranges from 1.0f (topmost position) to 0.0f (bottommost position). 
3. When scrolling and the ScrollBar hits < 0.1f (tweakable), then we begin to move the topmost offscreen GameObject to the bottom using RectTransform.SetAsLastSibling(), continue moving topmost GameObjects to the bottom until you reach the last item in the list.
4. When scrolling and the ScrollBar hits > 0.9f (tweakable) and there has been GameObjects moved, then we move the bottom offscreen GameObject back to the top using RectTransform.SetAsFirstSibling(), and continue until we're back to the first item of the list.
5. There are three tracking integer variables:
a) nextItemToMove
b) nextItemIndex
c) previousItemIndex
