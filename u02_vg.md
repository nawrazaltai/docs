# Script to config Git, Git-push from VM with PAT and Git post-receive hook.  
##### User information:
* Keyboard-layout: Swedish
* Username: ninja
* Server-name: altai
* Client machine prompt: Π
* Server prompt: $


### My script to config Git inside virtual machine
* The script is based on me (nawrazaltai) as the default user,
* With that said, any user can use the script and change to their preferred configuration.
 
    ```    
    #!/bin/bash

    cd ~
    sudo apt install git
    git --version
    echo " "
    echo " "
    rm ~/.gitconfig

    while true; do
    User=("name=nawrazaltai" "email=nawraz.altai@chasacademy.se")
    Core=("editor=nano")
    echo "Your default Git-settings are set to:"
    echo "=========================================="
    echo "${User[0]}"
    echo "${User[1]}"
    echo "${Core[0]}"
    echo "=========================================="
    echo " "
    read -p "If you want to keep these settings press 1, if you want to change the settings press 2: " Choice
    echo " "

    if [ $Choice == 1 ]; then
        echo "[user]" >> ~/.gitconfig
        for i in ${User[@]}; do
        echo $i >> ~/.gitconfig
        done
        echo " " >> ~/.gitconfig
        echo "[core]" >> ~/.gitconfig
        for i in ${Core[@]}; do
        echo $i >> ~/.gitconfig
        done
    break
    elif [ $Choice == 2 ]; then
        read -p "Please enter your Git-username: " gitname
        read -p "Please enter your email on Github: " gitemail
        read -p "Please enter your desired editor: " giteditor
        Newuser=("name=$gitname" "email=$gitemail")
        Newcore=("editor=$giteditor")

        echo "[user]" >> ~/.gitconfig
        for i in ${Newuser[@]}; do
        echo $i >> ~/.gitconfig
        done
        echo " " >> ~/.gitconfig
        echo "[core]" >> ~/.gitconfig
        for i in ${Newcore[@]}; do
        echo $i >> ~/.gitconfig
        echo " "
        echo "Your Git settings are now updated!"
        done
    break
    else
    echo "Wrong input, please try again..."
    echo " "
        continue
    fi
    done
    ```    

### Git push from virtual machine with PAT
* First step is to create a new directory on the server and move in to it
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
    * I did not create a README.md file in the repository.
    * Finally copied the generated link for this specific repository.
    
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
    * For the password I had to create a Personal Access Token on Github.com
    * Github > Settings > Developer settings > Personal access token > Generate new token.
    * In the Note column I wrote the repo-name: Ubuntu
    * I checked all the marks and set the expiration date to 7 days.
    * Once the token was generated I copied it

* Back to the server to push the file
    * when asked for the password, here I pasted the PAT as password
    
    ```
    Password for 'https://nawrazaltai@github.com': xxx_5RgmAXWlFxxv3T5QwdMu8EMqLeukhXXXXXXX
    ...
    Enumerating objects: 3, done.
    Counting objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 217 bytes | 217.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To https://github.com/nawrazaltai/Ubuntu.git
    [new branch]      main -> main
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
    
## Git post-recieve hook
##### Guides followed: 
* https://daveceddia.com/deploy-git-repo-to-server/
* https://towardsdatascience.com/how-to-create-a-git-hook-to-push-to-your-server-and-github-repo-fe51f59122dd
------------------------------------------------------------------------------

* Start by connecting to the server via ssh
    ```
    Π ssh -p 33000 ninja@192.168.10.185
    ...
    ninja@altai:~$
    ```
* Once connected, I created a new directory (named **python-quiz**) under the current user
    ```
    ninja@altai:~$ pwd
    /home/ninja
    ninja@altai:~$ mkdir python-quiz
    ninja@altai:~$
    ```
    
* Moved in to the python-quiz directory and initialize an empty Git-repo
    ```
    ninja@altai:~$ cd python-quiz/
    ninja@altai:~/python-quiz$
    ninja@altai:~/python-quiz$ git init --bare
    Initialized empty Git repository in /home/ninja/python-quiz/
    ninja@altai:~/python-quiz$
    ```
    
* Moved back to the user catalog and create the following directories (**/var/receive/python-quiz/output**)
    ```
    ninja@altai:~$ mkdir var
    ninja@altai:~$ cd var
    ninja@altai:~/var$ mkdir receive
    ninja@altai:~/var$ cd receive
    ninja@altai:~/var/receive$ mkdir python-quiz
    ninja@altai:~/var/receive$ cd python-quiz/
    ninja@altai:~/var/receive/python-quiz$ mkdir output
    ninja@altai:~/var/receive/python-quiz/
    ```

* On the **client** machine, checked the current remote config
    ```
    Π git remote -v
    origin  https://github.com/juiceghost/python-quiz (fetch)
    origin  https://github.com/juiceghost/python-quiz (push)
    Π 
    ```
* Then added new remote (named **staging**) for the server and the Github repository
    ```
    Π git remote add staging "[ninja@192.168.10.185:33000]:/home/ninja/python-quiz"
    ```
* Checked the remotes and made sure **staging** was added for this repository
    ```
    Π git remote -v
    origin  https://github.com/juiceghost/python-quiz (fetch)
    origin  https://github.com/juiceghost/python-quiz (push)
    staging [ninja@192.168.10.185:33000]:/home/ninja/python-quiz (fetch)
    staging [ninja@192.168.10.185:33000]:/home/ninja/python-quiz (push)
    Π
    ```
* On the server under reponame/hooks, created a new post-receive file     
    ```
    ninja@altai:~$ cd ~/python-quiz/hooks/
    ninja@altai:~/python-quiz/hooks$
    ninja@altai:~/python-quiz/hooks$ touch post-receive
    ninja@altai:~/python-quiz/hooks$
    ```

* With nano typed the following inside the post-receive file
    ```
    #!/bin/sh

    # Check out the files
    echo "My post-receive script"
    git --work-tree=/home/ninja/var/receive/python-quiz/output --git-dir=/home/ninja/python-quiz checkout -f main
    echo "The push was successful"
    ```

* Changed the rights and made the post-receive file executable
* Made sure the rights changed
    ```
    ninja@altai:~/python-quiz/hooks$ chmod +x post-receive
    ninja@altai:~/python-quiz/hooks$ ll
    total 64
    ...
    -rwxr-xr-x 1 ninja ninja  199 Dec 23 20:29 post-receive*
    ninja@altai:~/python-quiz/hooks$
    ```
    
* Made a change in my handle.py file in the repo at client side
* Then git- add, commit and push the handle.py file to from the local repo to **staging** at the remote server
    ```
    Π git status
    On branch main
    Your branch is up to date with 'origin/main'.

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
        modified:   ninja.py
    no changes added to commit (use "git add" and/or "git commit -a")
    
    Π git add ninja.py
    Π git commit -m "Testing the post-receive hook" ninja.py
    [main 2fae0c2] Testing the post-receive hook
    1 file changed, 1 insertion(+), 1 deletion(-)
    Π git push staging main
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 8 threads
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 311 bytes | 311.00 KiB/s, done.
    Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
    remote: My post-receive script
    remote: Switched to branch 'main'
    remote: The push was successful
    To 192.168.10.185:33000]:/home/ninja/python-quiz
    899015d..2fae0c2  main -> main
    ```

* After the push I checked my output directory at the **server** to make sure the push was successful
    ```
    ninja@altai:~$ cd var/receive/python-quiz/output/
    ninja@altai:~/var/receive/python-quiz/output$ ll
    total 156
    drwxr-xr-x 5 ninja ninja 4096 Dec 23 21:12 ./
    drwxr-xr-x 3 ninja ninja 4096 Dec 23 19:55 ../
    -rw-r--r-- 1 ninja ninja   11 Dec 23 21:12 .gitignore
    -rw-r--r-- 1 ninja ninja  341 Dec 23 21:12 README.md
    drwxr-xr-x 2 ninja ninja 4096 Dec 23 21:12 __pycache__/
    -rw-r--r-- 1 ninja ninja 5315 Dec 23 21:12 abbe.py
    ...
    -rw-r--r-- 1 ninja ninja 4198 Dec 23 21:12 main.py
    -rw-r--r-- 1 ninja ninja 1550 Dec 23 21:12 ninja.py
    -rw-r--r-- 1 ninja ninja 5366 Dec 23 21:12 yac.py
    ...
    ninja@altai:~/var/receive/python-quiz/output$
    ```
