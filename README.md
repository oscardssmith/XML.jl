<h1 align="center">XML.jl</h1>

<p align="center">Read and write XML in pure Julia.  THIS IS A WORK IN PROGRESS.</p>

<br><br>

## Quickstart

```julia
using XML

doc = XML.document("file.xml")

# The document's "prolog"
doc.children[1:end-1]

# `getindex` can be used to navigate into child elements.
doc[end]  # this is the root node.  Also retreived via `XML.root(doc)`


doc[end][4][2]

write("newfile.xml", doc)
```

## Internals

XML.jl puts all XML content into the following struct:

```julia
# The kind of Node
@enum(NodeType,
    DOCUMENT,           # children[1:end-1] == prolog, children[end] == root,
    DOCTYPE,            # <!DOCTYPE content >
    DECLARATION,        # <?xml attributes>
    COMMENT,            # <!-- content -->
    CDATA,              # <![CDATA[content]]>
    ELEMENT,            # <tag attributes>
    ELEMENTSELFCLOSED,  # <tag attributes/>
    TEXT                # I'm something that is between t '>' and a starting tag '<'
)

Base.@kwdef mutable struct Node
    nodetype::NodeType  # see above
    tag::String = ""    # a node's tag, used for DECLARATION, ELEMENT, and ELEMENTSELFCLOSED
    attributes::OrderedDict{String, String} = OrderedDict{String,String}() # a node's attributes e.g. `id="some id"`
    children::Vector{Node} = Node[]  # child elements of ELEMENT
    content::String = ""  # used for DOCTYPE, COMMENT, CDATA, and TEXT
    depth::Int = -1  # e.g. doc[1][2][3] would have a depth of 3
end
```
