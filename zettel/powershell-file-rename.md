---
created: 20250428
summary:
---

# powershell-file-rename

## content

```powershell
Get-ChildItem -Filter "image-*.png" | ForEach-Object {
    # Get the current timestamp
    $timestamp = (Get-Date).ToString("yyyyMMdd-HHmmss")
    
    # Construct the new file name
    $newFileName = "image-$timestamp.png"
    
    # Check if the file with the new name already exists
    $count = 1
    while (Test-Path $newFileName) {
        $newFileName = "image-$timestamp-$count.png"
        $count++
    }
    
    # Rename the file
    Rename-Item $_.FullName -NewName $newFileName
}
```


## refs

### up

### down

