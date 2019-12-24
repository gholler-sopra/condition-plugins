# Conditional

## Description

Conditional is a Guavus Enterprise Accelerator that serves as a control flow plugin and allows conditional execution within
pipelines. 

The conditions are specified as expressions and the 
variables could include the following things: Values specified as runtime arguments of
the pipeline, tokens from accelerators prior to the condition, and global
that includes global information about the pipeline, like stage, pipeline,
logical start time and plugin.

The Conditional accelerator specifies a boolean expression to be evaluated.
During a pipeline execution, the specified condition expression is
evaluated, resulting in a boolean value (either true or false).
Depending on the result of the evaluation, the downstream pipeline
connected either along the true path or along the false path is
executed. At any point in time, only one path is executed.


## A few examples for immediate reference Expression :

* Checks if the `runtime` argument `filepath` contains `input`.
```
 runtime['filepath'] =~ ".*input_.*"
```

* Checks two `runtime` arguments `a` and `b`.
```
 runtime['a'] > runtime['b']
```

* Checks `output` record count of `File` plugin from `token` with `runtime` value `count`.
```
 token['File']['output'] > runtime['count']
```

* Checks `error` record count of `Data Quality plugin` from `token` with `runtime` value `max_error`
```
token['Data Quality']['error'] <= runtime['max_error']
(token['File']['output'] > runtime['count']) and (runtime['a'] > runtime['b'])
(token['File']['output'] > runtime['count']) || (runtime['a'] > runtime['b'])
token['File']['output'] < runtime['count'] && token['File']['error'] < 1
```

* Checks if a `runtime` argument `value` is provided and it's not `null` and plugin `File` `error` count is less than 1.
```
 !isnull(runtime['value']) && token['File']['error'] < 1
```

* Takes the `max` of plugin `File1` `output` count and `File` `output` count and checks if it's greater than `runtime` argument that is specified as a macro.
```
 math:max(toDouble(token['File1']['output']), toDouble(token['File2']['output'])) > runtime[$variable]
```

## A few examples for immediate reference Variables:

* Runtime Variables
```runtime['<argument-name>']```

It is a map variable that holds runtime arguments specified for the pipeline containing an instance of the conditional accelerator. If the conditional expression specifies a runtime argument that is not present, then deployment or validation of the pipeline would be terminated with an exception.

All arguments should be specified within a quote '

* Token Variables

   ```token['<plugin-name>']['input'|'output'|'error']```

It is a multi-map variable that contains three metrics for each accelerator that precedes the instance of a conditional accelerator in DAG.

* Global Variables

    ```global['pipeline'|'namespace'|'logical_start_time'|'plugin'])```

It is a map variable containing general information from the pipeline.

## A few examples for immediate reference Macros:

* CDAP Macros can be specified within a condition expression. When a macro is specified in a condition, the data pipeline system will substitute a macro with the value specified at the runtime before the expression is compiled and executed. This provides a way for lazy substitution for the parts of the expression. Following are some of the examples for reference:

  ```runtime['$variable1'] > token['$stage']['$variable2']```

* Macros $variable1, $stage and $variable2 are substituted before the compilation of expression. Let's assume the value associated with the macro $variable1 is input, $stage is Data Quality and $variable2 is output. Then the expression is converted at runtime to

  ```runtime['input'] > token['Data Quality']['output']```

Then, there should exist another runtime argument named input that should be a numeric value. The same holds true for the accelerator Data Quality that emits value for output.

## A few examples for immediate reference Operators:

| Type | Description |
| :------------ | :-------- |
| Boolean and or & | You can use the operator '&&' or you can use 'and' to specify composing conditions, e.g. cond1 and cond2 and cond1 && cond2 |
| Boolean not or ! | You can use the operator '!' or you can use the operator 'Not' to not specify composing conditions, e.g. !cond and not cond |
| Boolean & | The bitwise operator '&' is used as follows !cond and not cond |
| Ternary conditional ?: | You can use the ternary conditional operator condition ? if_true : if_false operator or you can use the abbreviation value ?: if_false which returns the value if its evaluation is defined, non-null and non-false.The condition will evaluate to false when it refers to an undefined variable or null. e.g. val1 ? val1 : val2 and val1 ?: val2 , Where val1 and val2 could be true or false. |
| Equality == or eq | You can use the operator '==' or you can use the operator 'eq'. For example val1 == val2 and val1 eq val2 null is only ever equal to null, that is if you compare null to any non-null value, the result is false. |
| InEquality != or ne | You can use the operator '!='  or you can use the operator 'ne'. For exampleval1 != val2 and val1 ne val2 |
| Less Than < or lt | You can use the operator '<'  or you can use the operator 'lt'. For example val1 < val2 and val1 lt val2 |
| Less Than or Equal To <= or le | The usual <= operator can be used as well as the abbreviation lt. For example val1 <= val2 and val1 le val2 |
| Greater Than > or gt | You can use the operator '>' or you can use the operator 'gt'. For example val1 > val2 and val1 gt val2 |
| Greater Than or Equal To >= or ge | The usual >= operator can be used as well as the abbreviation gt. For example val1 >= val2 and val1 ge val2 |
| In or Match =~ | The =~ operator can be used to check if a string matches a regular expression. For example "abcdef" =~ "abc.* returns true. It also checks whether any collection, set or map (on keys) contains a value or not; in that case, it behaves as the "in" operator. Note that the arrays and user classes exposing a public 'contains' method will allow their instances to behave as the right-hand side operands of this operator. "a" =~ ["a","b","c","d","e",f"] returns true |
| Not-In or Not-Match !~ | The !~ operator can be used to check if a string does not match a regular expression. For example "abcdef" !~ "abc.* returns false. It also checks whether any collection, set or map (on keys) does not contain a value; in that case, it behaves as "not in" operator. Note that arrays and user classes exposing a public 'contains' method will allow their instances to behave as the right-hand side operands of this operator. "a" !~ ["a","b","c","d","e",f"] returns true |
| Starts With =^ | The =^ operator is a short-hand for the 'startsWith' method. For example, "abcdef" =^ "abc" returns true |
| Not Starts With !^ | This is the negation of the 'starts with' operator. a !^ "abc" is equivalent to !(a =^ "abc") |
| Ends With =$ | The =$ operator is a short-hand for the 'endsWith' method. For example, "abcdef" =$ "def" returns true |
| Not Ends With !$ | This is the negation of the 'ends with' operator. a !$ "abc" is equivalent to !(a =$ "abc") |
| If/Then | Traditional if ... then could be used, but they have to return either true or false, e.g. `if ((runtime['x'] * 2) == token['DQ']['output']) { false; } else { true; } |



For more detailed information visit

* Constructing Expressions (https://github.com/hydrator/condition-plugins/wiki/Expression)
  * About Variables (https://github.com/hydrator/condition-plugins/wiki/Variables)
  * Allowed Operators (https://github.com/hydrator/condition-plugins/wiki/Operators)
* Using Macros (https://github.com/hydrator/condition-plugins/wiki/Macros)
