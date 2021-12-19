## My script to config Git inside virtual machine
* In this specific script 'name', 'email' and 'editor' can be changed
* Remember to not include <> when changing values
    ```
    User=("name=<ENTER GIT-USERNAME> email=<ENTER USER-EMAIL@GITHUB>")

    Core=("editor=<ENTER DESIRED EDITOR>")
    ```

* For me, it looks like this:

    ```
    #!/bin/bash

    cd ~
    sudo apt install git
    git --version
    rm ~/.gitconfig

    User=("name=nawrazaltai email=nawraz.altai@chasacademy.se")

    echo [user] >> ~/.gitconfig
    for i in $User; do
    echo $i >> ~/.gitconfig
    done

    Core=("editor=nano")

    echo [core] >> ~/.gitconfig
    for i in $Core; do
    echo $i >> ~/.gitconfig
    done
    ```

## Git push from virtual machine with PAT
* First step is to create a new directory and move in to it
    ```
    ninja@altai:~$ mkdir ubuntu-server
    ninja@altai:~$ cd ubuntu-server/
    ```
* Next step is to initialize an empty Git repository
    ```
    ninja@altai:~/ubuntu-server$ git init
    Initialized empty Git repository in /home/ninja/ubuntu-server/.git/
    ```
* Create a new file inside the repository
    ```
    ninja@altai:~/ubuntu-server$ touch pythonfile.py
    ```
* Add and commit the new file
    ```
    ninja@altai:~/ubuntu-server$ git add .
    ninja@altai:~/ubuntu-server$ git commit -m "first commit" pythonfile.py
    [master (root-commit) fd27e43] first commit
    1 file changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 pythonfile.py
    ninja@altai:~/ubuntu-server$
    ```
* To push the file to Github I did the following:
    * Logged in to Github and created a new repository named: Ubuntu
    * I did not create a README.md file in the repository
    * Copied the generated link
    
* Back to the server I entered the link to my Github-repository
    ```
    ninja@altai:~/ubuntu-server$ git remote add origin https://github.com/nawrazaltai/Ubuntu.git
    ninja@altai:~/ubuntu-server$
    ```
* Next I set the branch
    ```
    ninja@altai:~/ubuntu-server$ git branch -M main
    ninja@altai:~/ubuntu-server$
    ```
* Pushing the file to Github
    ```
    ninja@altai:~/ubuntu-server$ git push -u origin main
    ```
* I got prompted to enter my Github username   
    ```
    Username for 'https://github.com': nawrazaltai
    ```
* Then I got prompted to enter my password
    ```
    Password for 'https://nawrazaltai@github.com':
    ```
    * For the password I created a Personal Access Token on Github
    * Github > Settings > Developer settings > Personal access token > Generate new token
    * In the Note column I wrote the repo-name: Ubuntu
    * I checked all the marks and set the expiration date to 7 days
    * Once the token was generated I copied it

* Back to pushing the file
    * when asked for the password, here I pasted the PAT as password
    ```
    Password for 'https://nawrazaltai@github.com': xxx_5RgmAXWlFxxv3T5QwdMu8EMqLeukhXXXXXXX
    ...
    Enumerating objects: 3, done.
    Counting objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 217 bytes | 217.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To https://github.com/nawrazaltai/Ubuntu.git
    * [new branch]      main -> main
    Branch 'main' set up to track remote branch 'main' from 'origin'.
    ninja@altai:~/ubuntu-server$
    ```
* Finally I checked the status of my repo to make sure the push was correct
    ```
    ninja@altai:~/ubuntu-server$ git status
    On branch main
    Your branch is up to date with 'origin/main'.

    nothing to commit, working tree clean
    ninja@altai:~/ubuntu-server$
    ```