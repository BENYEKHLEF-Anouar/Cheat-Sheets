 cd Webprojects
git clone https://github.com/your-username/your-repo.git
git commit -m "Your commit message"
git add . (add changes from the working directory to the staging area (also known as the "index"))
git push origin main
clear
dir



git pull origin main


cd folder_name
php file_name.php (to display in VS terminal)

cd .. (to back out/ exit from the file)


-----------------------
cd C:\Webprojects\Rappel-PHP-POO
git status
git init (To initialize a new, empty repository / To convert an existing, unversioned project into a Git repository)
git remote add origin https://github.com/your-username/your-repo.git
git add .
git commit -m "New addition"
git push -u origin main

------------------------------------
cd Chapitre3
php exceptions.php storage/seeds/articles.input.json


-----------------------------------------


git branch <branch-name>        # Create a new branch

git checkout new-feature        # Switch to the new branch

git checkout -b <new-branch-name>      # Create a new branch and immediately switch to it


git branch                      # List all local branches

git branch -r                   # List remote branches

git branch -a                   # List all (local + remote)


git add .

git commit -m "Work on new feature"

git push origin new-feature          # Push your branch to GitHub


   // Merge a Branch into Main

git checkout main

git pull origin main             # Make sure main is up to date

git merge new-feature            # Merge branch into main	

git push origin main             # Push merged changes




 // Pushes your local branch feature/realisation-portfolio to the remote repo (origin), creating it there if it doesn’t already exist.

git push --set-upstream origin feature/realisation-portfolio




-----------------------


git tag v1.0                           // creates a tag named v1.0 on your current commit 
git push origin v1.0                   // uploads the tag v1.0 from your local machine to the remote repository







