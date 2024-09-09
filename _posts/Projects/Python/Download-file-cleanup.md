---
layout: post
title:  "Snort Room Walkthrough"
date:   2024-03-01
author: C. Casquatch
comments: false
tag: ['python', 'files', 'sort']
---

> How many times can you say the word download in one post.

#### This code sorts the download folder into several directories with all downloaded files currently within the downloads folder.
# photo1
##### Make sure to replace "path_to_your_downloads_folder" with the actual path to your Downloads folder before running the script. Also, feel free to modify the folder_mapping dictionary to include additional file extensions and corresponding folder names as needed.

```python
import os
import shutil


def sort_downloads_folder(downloads_folder_path):
    # Dictionary to map file extensions to folder names
    folder_mapping = {
        '.txt': 'documents',
        '.pdf': 'pdf',
        '.png': 'images',
        '.jpg': 'images',
        '.jpeg': 'images',
        '.heic': 'images',
        '.mov': 'video_audio',
        '.docx': 'documents',
        '.xlsx': 'documents',
        '.3mf': 'printing',
        '.stl': 'printing',
        '.obj': 'printing',
        '.gcode': 'printing',
        '.bgcode': 'printing',
        '.zip': 'compressed',
        '.dmg': 'apps',
        '.iso': 'apps',
        '.7z': 'compressed',
        # Add more file extensions and corresponding folder names as needed
    }

    # Create folders if they don't exist
    for folder_name in folder_mapping.values():
        folder_path = os.path.join(downloads_folder_path, folder_name)
        if not os.path.exists(folder_path):
            os.makedirs(folder_path)

    # Iterate through files in Downloads folder
    for filename in os.listdir(downloads_folder_path):
        if filename != 'sorted_files':  # Skip the folder if it already exists
            file_path = os.path.join(downloads_folder_path, filename)
            if os.path.isfile(file_path):  # Check if it's a file
                file_extension = os.path.splitext(filename)[1].lower()  # Get file extension
                if file_extension in folder_mapping:  # Check if extension is in mapping
                    destination_folder = folder_mapping[file_extension]
                    destination_path = os.path.join(downloads_folder_path, destination_folder)
                    shutil.move(file_path, destination_path)  # Move the file to corresponding folder


if __name__ == "__main__":
    downloads_folder_path = "path_to_your_downloads_folder"  # Replace with your Downloads folder path
    sort_downloads_folder(downloads_folder_path) # Replace with your Downloads folder path
    print("Files sorted successfully!")
```
