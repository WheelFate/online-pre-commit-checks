# A Beginner's Guide to Pre-Merge Checks with GitHub Actions

This repository is a hands-on, step-by-step tutorial for beginners who want to learn how to automatically check their code *before* it gets merged into the main branch. We will do everything completely online using the GitHub interface and its powerful **GitHub Actions** feature.

This project avoids complex DevOps topics and focuses on one core concept: creating an automated quality gate for your code.

## What Are We Building?

We will build an automated check that scans every new contribution (a Pull Request) for a specific forbidden word ("FIXME").
*   If the word is found, the check will **fail**, blocking the Pull Request from being merged.
*   If the word is not found, the check will **pass**, allowing the Pull Request to be merged.

This is a fundamental building block for ensuring code quality in any project.

---

## Tutorial Steps

### Step 1: Create Your Repository

First, you need a place to store your project on GitHub.

1.  Log in to GitHub.
2.  Click the `+` icon in the top-right corner and select **"New repository"**.
3.  Give it a **Repository name** (e.g., `my-quality-checks`).
4.  Add a **Description** (e.g., "Learning to use GitHub Actions for pre-merge checks.").
5.  Make sure the repository is set to **Public**.
6.  Crucially, check the box to **"Add a README file"**.
7.  Click **"Create repository"**.

### Step 2: Create Your First Automated Check

We will now create the workflow that will run our check. This is done by adding a special YAML file in a specific folder within your repository.

1.  In your new repository, click the **"Actions"** tab at the top.
2.  GitHub might suggest some templates. Ignore them and click the link that says **"set up a workflow yourself"**.
3.  GitHub will create a new file for you named `main.yml` inside a `.github/workflows/` directory. This is the correct location.
4.  **Delete all the default text** in the editor.
5.  Copy the code below and paste it into the empty editor:

    ```yaml
    # A name for our set of checks
    name: Pre-Merge Quality Checks

    # This tells GitHub *when* to run our checks.
    # 'on: [pull_request]' means it will run on every new pull request.
    on: [pull_request]

    # This section defines the actual tasks to perform.
    jobs:
      # We'll name our first job 'check-for-forbidden-words'
      check-for-forbidden-words:
        # A more descriptive name that will appear on GitHub
        name: Check for Forbidden Words

        # The workflow will run on a free, temporary virtual computer running Ubuntu Linux.
        runs-on: ubuntu-latest

        # These are the specific steps the computer will follow.
        steps:
          # Step 1: This is a standard action that downloads your repository's code
          # onto the virtual computer so it can be checked.
          - name: Checkout repository code
            uses: actions/checkout@v4

          # Step 2: This is our custom check.
          - name: Search for the forbidden word "FIXME"
            run: |
              # We use the 'grep' command to search for text in files.
              # The 'if' statement checks if grep finds the word "FIXME" anywhere.
              # If `grep -r "FIXME" .` finds the word, it exits with 0 (success).
              # We want our check to FAIL in that case.
              if grep -r "FIXME" .
              then
                # If the word IS found, print an error and exit with an error code (1).
                # Exiting with 1 is what makes the GitHub check fail.
                echo "ERROR: The forbidden word 'FIXME' was found. Please remove it."
                exit 1
              else
                # If the word is NOT found, print a success message and exit successfully (0).
                echo "Check passed! The word 'FIXME' was not found."
                exit 0
              fi
    ```
6.  Click the green **"Commit changes..."** button on the right, and then commit again. This saves your workflow file to your main branch.

### Step 3: Test the Check (Making it Fail)

Let's see our new check in action by creating a Pull Request that *should* fail.

1.  Go to the **"Code"** tab of your repository.
2.  Create a new branch to work on. Click the dropdown that currently says **`main`**, type a new branch name like `test-readme-update`, and click **"Create branch: test-readme-update"**.
3.  You are now on your new branch. Find the `README.md` file and click the **pencil icon** to edit it.
4.  Add this line to the bottom of the file: `FIXME: This needs to be fixed.`
5.  Scroll down and **"Commit changes"** to your `test-readme-update` branch.
6.  You will now see a yellow banner. Click the green **"Compare & pull request"** button.
7.  Give your Pull Request a title (e.g., "Test README Update") and click **"Create pull request"**.

Wait a few moments on the Pull Request page. You will see your check run, and it will **fail with a red X**. You have successfully blocked a bad change! Click "Details" to see the error message you wrote.

### Step 4: Fix the Error and Watch it Pass

Now, let's complete the cycle by fixing our mistake.

1.  In the open Pull Request, click on the **"Files changed"** tab.
2.  Find the `README.md` file, click the three dots (`...`), and select **"Edit file"**.
3.  **Delete the entire line** containing the word `FIXME`.
4.  Scroll down and **"Commit changes"** directly to your branch.
5.  Go back to the **"Conversation"** tab of the Pull Request.

You will see that your new commit has automatically triggered the check again. This time, it will **pass with a green checkmark ✔**.

Because the check passed, the **"Merge pull request"** button is now available. You can click it to merge your clean code into the `main` branch.

Congratulations! You have successfully created and tested a fully automated quality check for your repository.
