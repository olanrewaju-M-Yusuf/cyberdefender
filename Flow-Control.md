# Fork

## Description

Split the input data up based on the specified delimiter and run all subsequent operations on each branch separately.For example, to decode multiple Base64 strings, enter them all on separate lines then add the 'Fork' and 'From Base64' operations to the recipe. Each string will be decoded separately.

## Example

![image](https://user-images.githubusercontent.com/57447333/87941629-539ca700-ca93-11ea-8a41-c1910c7745af.png)

## Explanation

The operation splits the input on '\n'(split delimiter), it then runs fromBase64 on [dGhl, cXVpY2s=, YnJvd24=, Zm94, anVtcGVk, b3Zlcg==, dGhl, c2xvdw==, ZG9n] each separately. Any operation you place after fromBase64 will run on [the, quick, brown, fox, jumped, over, the, slow, dog] separately also. The operation joins the output on ' '(merge delimiter).

# Merge

## Description 

Consolidate all branches back into a single trunk. The opposite of Fork.

## Example

![image](https://user-images.githubusercontent.com/57447333/87942678-deca6c80-ca94-11ea-8bc2-c5404a33961d.png)

## Explanation

When you apply a fork, it splits the input and all subsequent operations run separately on each split part. Merge reverts the fork, so every operation after a merge operates on the whole input.

# Subsection

## Description

Select a part of the input data using a regular expression (regex), and run all subsequent operations on each match separately. You can use up to one capture group, where the recipe will only be run on the data in the capture group. If there's more than one capture group, only the first one will be operated on.

## Example

![image](https://user-images.githubusercontent.com/57447333/87945353-84330f80-ca98-11ea-8981-0a7d51e71251.png)

## Explanation

In this case, the subsection regex(\d+) forces the subsequent operations to operate on the decimal numbers (similar to fork). Also similar to fork, to operate on the whole input again you will have to include a merge.

# Register

## Description

Extract data from the input and store it in registers which can then be passed into subsequent operations as arguments. Regular expression capture groups are used to select the data to extract.<br><br>To use registers in arguments, refer to them using the notation <code>$Rn</code> where n is the register number, starting at 0.<br><br>For example:<br>Input: <code>Test</code><br>Extractor: <code>(.*)</code><br>Argument: <code>$R0</code> becomes <code>Test</code><br><br>Registers can be escaped in arguments using a backslash. e.g. <code>\\$R0</code> would become <code>$R0</code> rather than <code>Test</code>.

## Example

![image](https://user-images.githubusercontent.com/57447333/87947492-500d1e00-ca9b-11ea-8253-159d5db208bd.png)

## Explanation

In this case, the extractor regex(\d+) matches on the number 1234. It then saves 1234 into register 0(R0) and you can then use that register in future operations as an argument.

# Label

## Description

Provides a location for conditional and fixed jumps to redirect execution to.

## Example

![image](https://user-images.githubusercontent.com/57447333/87950097-c3645f00-ca9e-11ea-9796-ba55fd46cf03.png)

## Explanation

The label provides a labelled location for the jump instructions to jump to.

# Jump

## Description

Jump forwards or backwards to the specified Label.

## Example

![image](https://user-images.githubusercontent.com/57447333/87950097-c3645f00-ca9e-11ea-9796-ba55fd46cf03.png)

## Explanation

The jump instruction jumps backwards until its maximum jump limit is reached (in this case it is 9). The result is 10 because it adds 1 and then does the comparison. This is equivalent to just chaining ten ADD 1 operations in the recipe.

# Conditional Jump

## Description

Conditionally jump forwards or backwards to the specified Label based on whether the data matches the specified regular expression.

## Example

![image](https://user-images.githubusercontent.com/57447333/87951649-b8123300-caa0-11ea-8412-463cf7e4560d.png)

## Explanation

The conditional jump operation jumps to the specified label based on whether the current output matches the specified regex. You can also invert this regex so that the conditional jump will jump to the label if the current output does not match the regex. In this example, it adds 1 to 0 until it does not match [0-8] hence it stops at 9.

# Return

## Description

End execution of operations at this point in the recipe.

## Example

![image](https://user-images.githubusercontent.com/57447333/87953938-9c5c5c00-caa3-11ea-93db-26309c227316.png)

## Explanation

This example isn't as immediately clear as the others, I will transform this into some abstract assembly to show it is working.

```
main:
    mov output, 0
    jmp start

exit:
    ret

start:
    add output, 1
    match output, [0-8]
    jnm exit
    jmp start
```

jnm means jump not match(jumps if the regex does not match). In this case ret = return, I am not using ret in the conventional sense but it is there to terminate the recipe when it is executed. The Return operation inside CyberChef stops the execution of all operations currently running.

# Comment

## Description 

Provides a place to write comments within the flow of the recipe. This operation has no computational effect.

## Example

![image](https://user-images.githubusercontent.com/57447333/87955871-37563580-caa6-11ea-858c-9dcce5a2a871.png)

## Explanation

This serves the same purpose as comments in programming, it is an aid to help yourself and others understand how your logic works.