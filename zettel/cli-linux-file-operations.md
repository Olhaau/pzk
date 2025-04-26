---
created: 20250426
summary:
---

# cli-linux-file-operations

## content

### Rename each file in a folder

```bash
for file in docs-*; do
    mv "$file" "${file#docs-}"
done
```

Only lower case and replace spaces:

```bash
for file in *; do
    # Extract the new name: lowercase and replace spaces with dashes
    new_name=$(echo "$file" | tr '[:upper:]' '[:lower:]' | tr ' ' '-')
    
    # Rename the file if the new name is different
    if [[ "$file" != "$new_name" ]]; then
        mv "$file" "$new_name"
    fi
done
```

## refs

### up

### down

