# Tacit ideas / Plans / Formatting

Tacit will come with a builtin formatter.

The formatter will help with complex nested expressions:
- Single space
- Double space
- Newline
- Comma

```
-- Original
1 2 3 4 5 + + + +

-- Formatted
+,
  1
  +,
    2  
    3  4 5 +  + -- First + uses space, second plus uses 
```

If your code already contains a "higher" separator, it will be left there, but "lower" separators will get upgraded by the formatter.
