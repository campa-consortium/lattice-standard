**To be done only once for each user:**
1) Go to https://github.com/campa-consortium/lattice-standard.
2) Click the pulldown arrow next to Fork, click Create a New Fork, and specify your GitHub username in the Owner field (e.g., "cemitch99").
3) Go back to https://github.com/campa-consortium/lattice-standard and copy the url 
(e.g., https://github.com/campa-consortium/lattice-standard.git)
4) On your local machine, from the terminal, clone the main repository:
`git clone https://github.com/campa-consortium/lattice-standard.git`
5) Rename what we just cloned:  call it "mainline:
`git remote rename origin mainline`
6) Add your remote fork in order to track it locally:
`git remote add cemitch99 https://github.com/cemitch99/lattice-standard.git`

**To be done each time to submit a PR, in your local repo:**
1) Change into the working directory for your local repo:
`cd lattice-standard`
2) Make sure your local repository is up-to-date:
`git checkout main`
`git pull`
2) Create a new branch with a descriptive name for the desired changes (e.g., "update_instructions"):
`git checkout -b update_instructions`
3) Make the desired changes to the local files.
For example, modify the file "PR_Instructions.md"
4) View the proposed changes (optional).
`git status`
5) Add the changes to your local staging area:
`git add PR_Instructions.md`
6) Commit the changes to your local repo, including an informative message.
`git commit -m "Update PR instructions."`
7) Push the changes to your fork remote repo.
`git push -u cemitch99 update_instructions`
8) Follow the link that is generated to open a new Pull Request on GitHub that includes these changes.
e.g., go to:  https://github.com/cemitch99/lattice-standard/pull/new/update_instructions
9) Add a title and additional information relevant to the Pull Request (as needed).

