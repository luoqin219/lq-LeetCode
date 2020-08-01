# Techinical Interview Techniques

Most of notes blow are extracted and summarized from ***《Cracking the Coding Interview》*** By **Fayle Laakmann Mcdowell** (6th edition). A big thank you to this BRILLIANT book.

## What you need to do step by step

### 1. Listen Carefully

Try to pick up the *unique* information such as **sorted** array, the code would be **run repeatedly** on a **server** and so on. (running code repeatedly may remind you to use *cache*)

### 2. Draw an Example

- Draw a **specific** example with real numbers or strings.
- Make the example **sufficiently large**. (e.g. do not draw a tree with only one root and two leaves).
- Do **not** use a **special case**. (e.g. the example shown above is not only a balanced tree but also a perfect tree where every node other than the leaves has two children.)

### 3. State a Brute Force Algorithm

Just try to give it as an initial terrible solution using the example you drew and explain what the space & time complexity is to your interviewers. After this, dive into improvements.

### 4. Optimize

- Look for any **unused** information. (e.g. the array given as input has been sorted)
- Use fresh examples
- Solve it "**incorrectly**". Having an inefficient solution can help you find an efficient solution while having an incorret solution might help you find a corect solution.
- Make time and vs. space **tradeoff**. Storing extra state about the problem can help you optimize the runtime somtimes.
- **Precompute** information. Is there a way that you can recognize the data (sorting etc.) or compute some values upfront that will help save time in the long run?
- Use a **Hash Table**.
- Think about the **best conceivable runtime**.

### 5. Walk Through

Get a feel for the structure of the code. Know what the variables are and when they change.

Pseudocode would be good sometiems.

### 6. Implement

Write beautiful codes:

- **Modularized** code. If you need to use a matrix initialized to {{1, 2, 3}, {4, 5, 6}, ...}, don't waste your time writing this initialization code. Just pretend you have a function initIncrementalMatrix(int size). Fill in the details later if you need to.
- **Error Checks**. A good compromise here is to add a *todo* and then just explain out loud what you'd like to test.
- Use other classes/structs where appropriate. If you need to return a list of start and end points from a function, you could do this as a two-dimensional array. It's better though to do this as a list of **StartEndPair** objects.
- Good **variable names**. 

### 7. Test

1. Start with a "**Conceptual**" test. Just reading and analyzing each line of code and check if the code does what you think it should do.
2. Weird looking code. Double check that line of code that says x = length - 2. Investigate that for loop that starts at i = 1. While you undoubtedy did this for a reason, it's really easy to get it just slightly wrong.
3. Hot spots. You've coded long enough to know what things are likely to cause problems. Base cases in recursive code. Integer division. Null nodes in binary trees. The start and end of iteration through a linked list. Double check that stuff.
4. Small test cases. Don't use that nice, big 8-element array from the algorithm part. Instead, use a 3 or 4 element array.
5. Special cases. Test your code against null or single element values, the extreme cases, and other special cases. 

