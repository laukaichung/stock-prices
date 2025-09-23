You must add the gitignore file.

The output nothing added to commit but untracked files present (use "git add" to track) indicates that your workflow is failing because you have not told Git to track the newly created file nasdaq_full_tickers.json before trying to commit it. 
Even though your workflow adds aapl.json in a later step, your curl command downloads nasdaq_full_tickers.json, which is an "untracked file" that causes the git commit command to fail if it attempts to commit all files at once. The default behavior of git commit is to fail when there are untracked files. 
The fix: add a .gitignore file
You don't need to track the downloaded nasdaq_full_tickers.json file in your repository, only the resulting aapl.json file. The simplest solution is to tell Git to ignore the large downloaded file by creating a .gitignore file.
Create a file named .gitignore in the root of your repository.
Add the filename to the .gitignore file. 
Step 1: Create a .gitignore file
Add a new file named .gitignore to your repository via the GitHub web interface or locally. 
Step 2: Add the filename to the .gitignore file
Inside the .gitignore file, add the following line:
nasdaq_full_tickers.json
This tells Git to disregard this file, preventing it from showing up in your status or causing issues with your commit steps. 
Updated workflow script
While the .gitignore is the main fix, you can make your workflow more robust by explicitly committing only the files you intend to update. This prevents the workflow from failing if other untracked files somehow appear. 
Replace your current Commit and push updated file step with the following: 
yaml
    - name: Commit and push updated file
      run: |
        git config user.email "actions@github.com"
        git config user.name "GitHub Actions"
        git add aapl.json
        if git diff-index --quiet HEAD --; then
          echo "No changes to commit."
        else
          git commit -m "Daily update of AAPL price"
          git push
        fi
Use code with caution.

What this updated script does:
git add aapl.json: Explicitly stages only the aapl.json file for the next commit. This is the correct way to handle your file.
if git diff-index --quiet HEAD --: This command checks if there are any pending changes to be committed.
It exits with a code of 0 if there are no changes, and 1 if there are changes.
echo "No changes to commit.": If there are no changes, the workflow prints this message and exits the step gracefully, without error.
git commit -m ... and git push: If there are changes, the commit and push commands run as intended. 
This modified script is more resilient and will not fail if there's no change to commit, nor will it be stopped by untracked files like nasdaq_full_tickers.json.
