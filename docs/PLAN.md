# Plan For ZCLAP

## Syntax

There will be 2 ways to construct commandline arguments, one called `Builder` Syntax and one called `Structure` syntax, the `Structure` syntax is the same as the `derive` syntax for the rust crate in inspiration.

### Structure

```zig
const zclap = @import("zclap");

const Args = struct {
    help: bool = @field({
        .short = 'h',
        .long = "help",
        .arg_type = ArgType.Bool,
        .required = false,
        .action = null,
    });

    name: ?[]const u8 = @field({
        .short = null,
        .long = "name",
        .arg_type = ArgType.String,
        .required = true,
        .action = null,
    });

    age: ?i32 = @field({
        .short = null,
        .long = "age",
        .arg_type = ArgType.Int,
        .required = true,
        .action = null,
    });

    verbosity: usize = @field({
        .short = 'v',
        .long = "verbose",
        .arg_type = ArgType.Bool,
        .required = false,
        .action = ArgAction.Count,
    });
};
```

### Builder

```zig
const std = @import("std");
const zclap = @import("zclap");

pub fn main() void {
    var allocator = std.heap.page_allocator;
    var builder = zclap.ArgBuilder.init(&allocator);

    builder
        .arg("name", null, "name", zclap.ArgType.String, true, null, "Optional name to operate on")
        .arg("config", 'c', "config", zclap.ArgType.String, false, null, "Sets a custom config file")
        .arg("debug", 'd', "debug", zclap.ArgType.Bool, false, zclap.ArgAction.Count, "Turn debugging information on")
        .arg("help", 'h', "help", zclap.ArgType.Bool, false, zclap.ArgAction.SetTrue, "Print help");

    const args_info = builder.build();

    const args = zclap.parse(args_info, std.process.args()) catch |err| {
        std.debug.print("Error parsing arguments: {}\n", .{err});
        return;
    };

    if (args.help) {
        zclap.printHelp(args_info);
        return;
    }

    if (args.name) |name| {
        std.debug.print("Name: {}\n", .{name});
    } else {
        std.debug.print("Name is required\n", .{});
    }

    if (args.config) |config| {
        std.debug.print("Config file: {}\n", .{config});
    }

    std.debug.print("Debug mode count: {}\n", .{args.debug});
}

```

### Final Details for Syntax

Builder syntax will be the reccomended default, and first to be developed, also the `--help/-h` option is automatically added and generated unless its disabled explicitly. The Help metadata is pulled from the `build.zig.zon` also a `-V/--version` flag is added which prints the program name and version, if the metadata is there that is and its not explicitly disabled.



## Data Types

- Boolean

- UInt

- Int

- Float

- String

- Char


