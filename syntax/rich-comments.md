# Pentagram ideas / Syntax / Rich comments

Normal text is captured:
```
-- Some text
```

Any character except `space` causes rich comments to be disabled:
```
--- Disabled
--Disabled
```

Run code:
```
-- @code-variable = 1
-- @code-method /= x =/ x 1 +
```

Switch back to text from inside code:
```
-- @title: My title
-- @aside:
--   Can be a whole new
--   block of text
-- @4pt small-size: Can also be parameterized
```

_Uses everything in scope and unprefixes `doc-`_

Execute code in line:
```
-- For @[strong: inline] markup
-- @[emph: Start] only also works
```

Refer to names in scope (creates link, does not execute):
```
-- Use [+] and [-]
```

Nested comments inside code inside comments is OK:
```
-- @x = 1 -- Assign
```

_To use this syntax outside of comments (for text-heavy code), use a special file extension_
