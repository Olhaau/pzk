---
created: 20250428
summary:
---

# powershell-file-rename

## content

```powershell
Get-ChildItem -Filter "image-*.png" | ForEach-Object {
    # Get the creation time in a format suitable for the filename
    $creationTime = $_.CreationTime.ToString("yyyyMMdd-HHmmss")
    
    # Construct the new file name
    $newFileName = "image-$creationTime.png"
    
    # Check if a file with the new name already exists
    $count = 1
    while (Test-Path $newFileName) {
        $newFileName = "image-$creationTime-$count.png"
        $count++
    }
    
    # Rename the file
    Rename-Item $_.FullName -NewName $newFileName
}
```


## refs

### up

### down

