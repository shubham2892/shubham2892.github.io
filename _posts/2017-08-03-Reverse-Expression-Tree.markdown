

The problem is to reverse the expression tree. For example if you are given a expression tree:

`(AB)(CD)E(FGH)I`

then the desired output would be `I(HGF)E(DC)(BA)`.

Notice that each sub-expression is reversed within themselves and the order of each main sub-expression is also reversed.

Looking at the given and desired expression gives a hint that solution would require the use of recursion(Each sub-expression is individually reversed) and stacks(Expression tree duh!). Let's tackle the problem in parts.

Has problem involved only to reverse the outermost sub-expressions and not the sub-expressions themselves, simple solution would be to populate the stack with sub-expressions and popping each sub-expression to append at the end of a new string. The difficult part would be to detect each outer sub-expression. For example given the expression `((AB)(CD)E(FGH)I)`, the outermost sub-expressions would be:

  `(AB)` `(CD)`  `E`  `(FGH)`  `I`

```c++
string reverse_expression_simple_version(string input){
  // To store each sub-expression
    stack<string> expressions;
  // Keep track of open and close parenthesis
    stack<int> parenthesis;
    int start = 0;
    int end = input.length() -1;
    while (start <= end) {
        if (input[start] == '(') {
            // Push the position of open parenthesis on the stack
            parenthesis.push(start + 1);
        } else if (input[start] == ')') {
          // Checking for parenthesis size 1 ensures that we only get outer most sub-expressions and not sub-expressions of sub-expression
            if (parenthesis.size() == 1) {
              // get the starting index of corresponding closing bracket
                int parenthesis_start = parenthesis.top();
              // push the expression on the stack
                expressions.push(input.substr(parenthesis_start,(start - index_start + 1)));
            }
            parenthesis.pop();
        } else {
          // To handle the case of single character sub-expression like E, I in above example
            if (parenthesis.empty()) {
                expressions.push(string(1, input[start]));
            }
        }
        start++;
    }
    string final_string = "";
    while (!expressions.empty()) {
      // Pop expressions from the stack and append it to the end of final_string
      // This will result in reverse of original sub expression
        final_string += expressions.top();
        expressions.pop();
    }
    return final_string;
}
```

Output from above program will be `I(FGH)E(CD)(AB)`.

To tackle the part where each sub-expression is also reversed individually, we can call the above function recursively for each of the sub-expression. How do we accomodate that in the above function. While we are pushing the sub-expression on to the stack, let's recursively call the function with just the sub-expression.

```C++
string reverse_expression(string input, int start, int end) {
    stack<string> expressions;
    stack<int> parenthesis;
    while (start <= end) {
        if (input[start] == '(') {
            parenthesis.push(start + 1);
        } else if (input[start] == ')') {
            if (parenthesis.size() == 1) {
                int index_start = parenthesis.top();
                // This is where the magic happens, we are calling the function recursively with sub-expression as output and  storing the result in stack which will be used to form  the main recursive expression

                string reversed_string = reverse_expression(input, index_start, start - 1);
                expressions.push("(" + reversed_string + ")");
            }
            parenthesis.pop();
        } else {
            if (parenthesis.empty()) {
                expressions.push(string(1, input[start]));
            }
        }
        start++;
    }
    string final_string = "";
    while (!expressions.empty()) {
        final_string += expressions.top();
        expressions.pop();
    }
    return final_string;
}
```

The output from the above expression would be `I(HGF)E(DC)(BA)`.

Any input on improving the solution would be highly appreciated.
