---
created: 20250611
---

# cmd-tree

#zettel

summary::

## refs

### up

### down

## content

To print a directory tree structure in a Solaris environment, you can use the find command along with the -print option, or you can use the tree command if it's installed. 

Below are several methods to achieve this: 
1. Using find 

If tree is not installed, you can use the find command to create a tree-like structure: 
bash
 
 
 
1
find /path/to/directory -print | sed -e 's;[^/]*/;/;g' -e 's;/;|;g' -e 's;.|-- ;|-- ;g'
 
 
Explanation: 

    The find command lists all files and directories in the specified path.
    The sed command processes the output to format it in a tree-like style.
     

For example, to print a tree structure starting from the current directory, you can use: 
bash
 
 
 
1
find . -print | sed -e 's;[^/]*/;/;g' -e 's;/;|;g' -e 's;.|-- ;|-- ;g'
 
 
2. Using the tree Command 

If you have the tree command installed (it may not be installed by default in Solaris but can be added), you can simply run: 
bash
 
 
 
1
tree /path/to/directory
 
 

If the tree package is not installed and you want to install it, you can often find packages in the Solaris package repository, or you can compile it from source if needed.  
Example Usage of tree Command: 
bash
 
 
 
1
tree
 
 

This will print the directory tree of the current directory. 
3. Installing tree on Solaris 

If you need to install tree, you may need to use a package manager like pkgutil (if available on your Solaris system) or compile it from source. For example, if you have the ability to download packages, you might do something like this: 

    Download the tree package for Solaris (typically from Sunfreeware or another repository).
    Install the package using pkgadd.
     

Example of find Command Output 

Here is a simplified example of what the output might look like when using find and sed: 
plaintext
 
 
 
1
2
3
4
5
6
7
8
9
.
|-- dir1
|   |-- file1.txt
|   |-- file2.txt
|-- dir2
|   |-- file3.txt
|   `-- subdir
|       `-- file4.txt
`-- file5.txt
 
 
Conclusion 

To print a directory tree in Solaris: 

    Use the find command with sed for a basic recursive view.
    Use the tree command if available for a more visually appealing tree structure.
    Consider installing the tree utility if it’s not already on your system, as it provides a more straightforward way to view directory structures.
     
