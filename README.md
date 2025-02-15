### 1. Shebang and Initial Setup

`#!/bin/bash`
This line defines the **shebang** which tells the system that the script should be run using the **Bash** shell. It ensures that the script is executed in the correct environment.

### 2. Argument Check for Validity

`if [[ $# != 2 ]]
then
  echo "backup.sh target_directory_name destination_directory_name"
  exit
fi`

This section checks if exactly **two arguments** are passed when running the script. The arguments should be the **source** directory and the destination directory. If the user does not provide these arguments, an error message is displayed and the script exits.

### 3. Directory Existence Validation

`if [[ $# != 2 ]]
then
  echo "backup.sh target_directory_name destination_directory_name"
  exit
fi`

Here, the script checks whether the directories provided as arguments actually exist using the `-d ` flag. If either of the directories is invalid (non-existent), the script will output an error message and terminate.

### 4. Assign Arguments to Variables

`targetDirectory=$1
destinationDirectory=$2`

The script stores the provided **source directory** `($1)` and **destination directory** `($2)` into variables for easy reference in the subsequent steps of the script.

### 5. Echo Target and Destination Directories

`echo "Target Directory: $targetDirectory"
echo "Destination directory: $destinationDirectory"`

 This section prints out the values of the **source directory** and **destination directory** to the terminal so the user can confirm that the correct paths have been set.

### 6. Capture Current Timestamp

`currentTS=$(date +%s)`

This command gets the **current timestamp** in seconds since the Unix epoch and stores it in the **currentTS** variable. This timestamp will be used to name the backup file, ensuring each backup has a unique name.

### 7. Set Backup File Name

`backupFileName="backup-[$currentTS].tar.gz"`

This line constructs the **backup file** name using the `currentTS `variable. The name is prefixed with "backup-" and includes the timestamp to ensure the backup file is unique. The `.tar.gz` extension is used because the file will be compressed using `tar`.

### 8. Save Original Directory Path

`origAbsPath=$(pwd)`

The script stores the absolute path of the current working directory (`pwd`) in the variable `origAbsPath`. This is necessary because the script will change directories, and you’ll need to return to the original directory later.

### 9. Navigate to Destination Directory

### 9. Navigate to Destination Directory

```
cd "$destinationDirectory"
destDirAbsPath=$(pwd)
```

This command changes the working directory to the destination directory provided earlier. The script then stores the absolute path of the destination directory (`pwd`) in the `destDirAbsPath` variable.

### 10. Navigate to Source Directory

```
cd "$origAbsPath"
cd "$targetDirectory"
```

The script first navigates back to the original directory (`origAbsPath`) and then changes to the target directory (`targetDirectory`) to begin the backup process.

### 11. Calculate Timestamp for 24 Hours Ago

`yesterdayTS=$(($currentTS - 24 * 60 * 60))`

This line calculates the timestamp for exactly 24 hours ago by subtracting the equivalent of 24 hours (in seconds) from the current timestamp (`currentTS`). The result is stored in the `yesterdayTS` variable.

### 12. Declare Backup Files Array

`declare -a toBackup`

 The script declares an empty array `toBackup` that will later hold the names of files that need to be backed up.

### 13. Loop Through Files and Check Last Modified Date

```
for file in $(ls)
do
  file_last_modified_date=$(date -r "$file" +%s)
  if [[ $file_last_modified_date -gt $yesterdayTS ]]
  then
    toBackup+=($file)
  fi
done
```

This loop goes through each file in the target directory. For each file, the script uses date **-r "$file" +%s** to get its last modified date in seconds. It compares this timestamp with `yesterdayTS`. If the file was modified in the last 24 hours, it adds the file to the `toBackup` array.

### 14. Create Backup Archive

tar -czvf "$backupFileName" "${toBackup[@]}"

This command uses tar to create a compressed archive of the files stored in the `toBackup` array. The `-czvf `options create a **gzip-compressed tar** file (.tar.gz). The backup file is named according to the timestamp format defined earlier.

### 15. Move Backup to Destination Directory

`mv "$backupFileName" "$destDirAbsPath"`

Finally, the script moves the backup file ($backupFileName) to the **destination directory** (`$destDirAbsPath`) where it can be securely stored.
