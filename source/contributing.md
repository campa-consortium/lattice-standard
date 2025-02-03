# How to Contribute

<<<<<<< HEAD
Follow these steps **only once**:
1. Go to https://github.com/campa-consortium/particle-accelerator-lattice-standard.
=======
## Follow these steps **only once**

1. Go to https://github.com/campa-consortium/particle-accerator-lattice-standard.
>>>>>>> c86a619 (Apply suggestions from code review)

2. Click the pulldown arrow next to `Fork`, click `Create a new fork`, and specify your GitHub username in the `Owner` field (e.g., "username").

3. Go back to https://github.com/campa-consortium/particle-accelerator-lattice-standard, click `Code` > `SSH`, and copy the url of the repo:
(e.g., git@github.com:campa-consortium/particle-accelerator-lattice-standard.git)

The simpler option `Code` > `HTTPS` can be used if the user just want to look at the repo.  (This does not require a password setup.)

4. On your local machine, from the terminal, clone the main repository:
```
git clone git@github.com:campa-consortium/particle-accelerator-lattice-standard.git
cd particle-accelerator-lattice-standard
```

5. Rename what we just cloned:  call it "mainline":
```
git remote rename origin mainline
```

6.  Add your remote repository in order to track it locally:
```
git remote add username git@github.com:campa-consortium/particle-accelerator-lattice-standard.git
```

## Follow these steps **each time you submit a pull request**

1. Change into the working directory for your local repo:
```
cd particle-accelerator-lattice-standard
```

2. Make sure your local repository is up-to-date:
```
git checkout main
git pull
```

2. Create a new branch with a descriptive name for the desired changes (e.g., "add_template"):
```
git checkout -b add_template
```

3. Make the desired changes to the local files.
For example, add a new file "pull_request_element_template.md"

4. View the proposed changes via `git status` (optional).

5. Add the changes to your local staging area:
```
git add pull_request_element_template.md
```

6. Commit the changes to your local repo, including an informative message:
```
git commit -m "Add element template."
```

7. Push the changes to your fork:
```
git push -u username add_template
```

8. Follow the link that is generated to open a new pull request on GitHub that includes these changes, e.g., go to [https://github.com/campa-consortium/particle-accelerator-lattice-standard/compare](https://github.com/campa-consortium/particle-accelerator-lattice-standard/compare).

9. Add a title and additional information relevant to the pull request (as needed).
