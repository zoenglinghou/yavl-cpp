Summary
-------

YAVL-CPP is a C++ library check if a given YAML file follows an expected
schema. For example, you can use this to check if a configuration file
has the tree structure that your program expects to have.

This library checks if the input YAML has the tree structure (i.e.,
keys, values, types, nesting of keys) that the specification expects.
See example below.

To be clear, this is not a tool to check if the input file is a valid
YAML; any YAML parser will do that.

The tree specification file is also written in YAML.

This library saves you the hassle of writing code to emit error messages
about what is wrong about the input file. Your code can be written
assuming that it only ever works with input that conforms to an expected
schema.

Structures such as lists of lists, maps of lists, lists etc can be
nicely checked. :)

Example Tree Specification
--------------------------

    map:
      HEADER:
        map:
          name: [string: ]
          version: [string: ]
          size: [enum: [big, small]]
          pieces:
            map:
              a:
                list: [string: ]
              b:
                list: [uint64: ]

Notice that HEADER.pieces.a is list nested within the map HEADER.pieces.
You can have arbitrary levels of nesting, and also do things like maps
within lists.

Note: one of the items in the
[TODO](./TODO) is
to simplify this syntax.

Valid YAML
----------

    HEADER:
      name: myname
      version: 1.02
      size: big
      pieces:
        a: [hello, world]
        b: [100, 200]

Invalid YAML
------------

    HEADER:
      name: myname
      version: 1.02
      size: xbig
      pieces:
        a: [hello, world]
        b: [x100, 200]

With the invalid YAML, you\'ll get an errors that \'xbig\' is not
allowed and that \'x100\' could not be converted to a \'long long\'.

Dependencies
------------

Yavl-cpp uses the excellent \[<http://code.google.com/p/yaml-cpp/>
yaml-cpp\] library. Go check out yaml-cpp just for the heck of it. It\'s
one of the best designed libraries out there (IMHO). Simple, and to the
point implementation and documentaion.

Compilation
-----------

Using cmake and conan:

\$ cmake -S . -B build/debug -DCMAKE\_BUILD\_TYPE=Debug
-DCMAKE\_INSTALL\_PREFIX=. \$ cmake \--build build/debug

To install: \$ cmake \--build build/debug \-- install \$ tree dist/
dist/ ├── bin │ └── checker └── lib └── libyavl\_cpp.a

2 directories, 2 files

OR,

Nothing fancy. Just compile in yavl.cpp and all the cpp files of
yaml-cpp with your program. I believe that libraries should pollute your
build process as little as possible (thankfully, so does yaml-cpp).

Here is how you would compile the example checker program,
example-code/checker.cpp. YAML\_CPP\_DIR is where you untarred the
yaml-cpp source and YAVL\_CPP\_DIR is where you untarred the yavl-cpp
source.

    g++ -I$YAML_CPP_DIR/include -I$YAVL_CPP_DIR/src \
      $YAML_CPP_DIR/src/*.cpp \
      $YAVL_CPP_DIR/src/yavl.cpp \
      $YAVL_CPP_DIR/example-code/checker.cpp -o checker

And run it like this:

    checker $YAVL_CPP_DIR/example-specs/gr3.yaml $YAVL_CPP_DIR/example-specs/y0.gr3.yaml

Developer Notes
---------------

What I found interesting about this exercise was how short I was able to
make the checker program by a judicious selection of:

-   problem space restriction
-   treespec language is itself YAML

Problem space restriction gave the most bang. Instead of solving a very
general problem, I chose to solve a certain subset of the problem. I
think it\'s quite a large subset, so hopefully many people will find
this library useful.

By using YAML to encode the treespec, I saved myself the hassle of
writing a parser, and the user of the library the hassle of learning yet
another DSL.

eof
