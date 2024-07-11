### Push Code to GitHub​​

1. Open a terminal (ex. Windows Powershell or Mac Terminal) and login to USS.

1. Navigate to the same primary folder/directory of your Github repository.

1. Add the files you want to push to Github ( `.` captures everything below your directory):
```
git add .
```

1. Run the following to confirm the file list from the `add` command:
```
git status
```

1. Commit the changes with the following command:
```
git commit -m "initial migration"
```

1. Run `git status` again.  It should be empty because the files have been committed.
​
1. Push the code to GitHub using the following command: 
```
git push origin main
```