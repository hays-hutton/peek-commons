# GFM Sample

A paragraph with **bold**, *italic*, `inline code`, and a [link](https://example.com).

## Table

| Feature      | Status | Notes            |
|:-------------|:------:|-----------------:|
| Tables       |  yes   | aligned columns  |
| Task lists   |  yes   | see below        |
| Code blocks  |  yes   | highlighted      |

## Task list

- [x] render this file
- [ ] leave this unchecked

## Code

```zig
const std = @import("std");

pub fn main() void {
    std.debug.print("hello from peek\n", .{});
}
```

> A blockquote to round things out.

![relative image](./missing-image.png)
