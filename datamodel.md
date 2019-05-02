# Data model

Install the tools for managing object and fact types (as root):

```bash
pip3 install act-types
```

Setup basic types

```bash
act-types \
    --userid 1 \
    --act-baseurl http://localhost:8888 \
    --default-object-types \
    --default-fact-types \
    --default-meta-fact-types \
    add
```
