# Questions
## How are the tables selected when entering metric data?
###
1. sort dimensions by key
2. append the following into a string <dim1name>|<dim1value>|<dim2name>|<dim2value>|etc. last char is a |
3. then take the sha1 in hex as the whole thing
4. hash is in the table, table chosen is first char of the hash