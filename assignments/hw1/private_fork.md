The [repository](https://github.com/ucy-coast/cs452-sp24/) for the assignment is public and Github does not allow the creation of private forks for public repositories.

The correct way of creating a private fork by duplicating the repo is documented [here](https://help.github.com/articles/duplicating-a-repository/).

For this assignment the commands are:

 1. Create a bare clone of the repository.
    (This is temporary and will be removed so just do it wherever.)
    ```bash
    git clone --bare git@github.com:ucy-coast/cs452-sp24.git
    ```

 2. [Create a new private repository on Github](https://help.github.com/articles/creating-a-new-repository/) and name it `cs452-sp24`.

 3. Mirror-push your bare clone to your new `cs452-sp24` repository.
    > Replace `<your_username>` with your actual Github username in the url below.
    
    ```bash
    cd cs452-sp24.git
    git push --mirror git@github.com:<your_username>/cs452-sp24.git
    ```

 4. Remove the temporary local repository you created in step 1.
    ```bash
    cd ..
    rm -rf cs452-sp24.git
    ```
    
 5. You can now clone your `cs452-sp24` repository on your machine (in my case in the `workspace` folder).
    ```bash
    cd ~/workspace
    git clone git@github.com:<your_username>/cs452-sp24.git
    ```
   
 6. If you want, add the original repo as remote to fetch (potential) future changes.
    Make sure you also disable push on the remote (as you are not allowed to push to it anyway).
    ```bash
    git remote add upstream git@github.com:ucy-coast/cs452-sp24.git
    git remote set-url --push upstream DISABLE
    ```
    You can list all your remotes with `git remote -v`. You should see:
    ```
    origin	git@github.com:<your_username>/cs452-sp24.git (fetch)
    origin	git@github.com:<your_username>/cs452-sp24.git (push)
    upstream	git@github.com:ucy-coast/cs452-sp24.git (fetch)
    upstream	DISABLE (push)
    ```
    > When you push, do so on `origin` with `git push origin`.
   
    > When you want to pull changes from `upstream` you can just fetch the remote and rebase on top of your work.
    ```bash
      git fetch upstream
      git rebase upstream/main
      ```
      And solve the conflicts if any
