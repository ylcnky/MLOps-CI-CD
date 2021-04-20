## Add the GitActions workflow file
1. Click the *Add file* --> *Create new file* button
2. Type `.github/workflows/mlops-ci.yml`
3. Type the following yml file
    ```yml
    name: mlops-ci-demo
    on: [push] # You can trigger the pipeline in any branch, or a specific branch you define
    jobs:
    run:
        runs-on: [ubuntu-latest]
        container: docker://dvcorg/cml-py3:latest # This is the docker file with many built in functionalities for MLOps. Link: https://dvc.org/
        steps:
        - uses: actions/checkout@v2
        - name: 'Train my model'
            env:
            repo_token: ${{ secrets.GITHUB_TOKEN }}
            run: |
            # Define the steps of the pipeline.
            
            # STEP-1: Install required packages and run the python file
            pip install -r requirements.txt
            python model.py
    ```
## Create a new branch in bottom and name as `experiment_1`.
Click the `Propose new file` button. This will open a new page.

Write some comments to describe what you do. For example `First step of MLOps demo starts`.

Click the `Create pull request` button. This will start the GithubActions steps.

Click the `Actions` section in Github page. And show the steps of the pipeline.

*Explain* that 
1. Github creates a virtual machine in their own servers,
2. Pulls the Docker image that we define in the `mlops-ci.yml` file,
3. Uploads our ML model,
4. `pip install requirements.txt` to get the relevant packages
5. run the python ML model in their virtual servers.
6. Instead of running this model in Github servers, we can run it in our own Cloud systems.
7. In line 527 (or different), show the `Mean Absolute Error` and `Mean Squared Error` as results.

## We can see the results of our model in the logs of Github Actions, but it is not user friendly to share the results with other team members.

### So instead of logging the results of our model in the output, we can store in a temporary text file --> reflect this text file as the part of the pull request.
1. Go back to the code page,
2. Go the `experiment_1` branch,
3. enable the commented last line for text file output
4. Commit the changes to `experiment_1` branch and click `Commit changes` button

## Update the `Github Actions` to output the results into MD and show in the results.
1. Open `mlops-ci.yml`, edit the file to update the pipeline
2. Add the new sections to the yml file.
    ```yml
    name: mlops-ci-demo
    on: [push] # You can trigger the pipeline in any branch, or a specific branch you define
    jobs:
    run:
        runs-on: [ubuntu-latest]
        container: docker://dvcorg/cml-py3:latest # This is the docker file with many built in functionalities for MLOps. Link: https://dvc.org/
        steps:
        - uses: actions/checkout@v2
        - name: 'Train my model'
            env:
            repo_token: ${{ secrets.GITHUB_TOKEN }}
            run: |
            # Define the steps of the pipeline.

            # STEP-1: Install required packages and run the python file
            pip install -r requirements.txt
            python model.py

            # STEP-2: Create a Markdown file and write the results into that file.
            echo "## Model Metrics" > mlops_demo_report.md
            cat metrics.txt >> mlops_demo_report.md
                # CML library has functinality to send comments as reports
            cml-send-comment mlops_demo_report.md
    ```
3. Click `Start commit` button
4. Commit to `experiment_1` branch
5. Check the progress in the pull requests
6. Show the output of the result as the comment result

## Now the results are visible, but only as text. So how about visualizing the results?. We can generate charts to show the performance metrics of our model.
1. Go back to the model.py
2. Uncomment the line for `plot_predictions` functions
3. Commit the change in `experiment_1` branch
4. Go the `Github Actions` file and update the yml file as following
    ```yml
    name: mlops-ci-demo
    on: [push] # You can trigger the pipeline in any branch, or a specific branch you define
    jobs:
    run:
        runs-on: [ubuntu-latest]
        container: docker://dvcorg/cml-py3:latest # This is the docker file with many built in functionalities for MLOps. Link: https://dvc.org/
        steps:
        - uses: actions/checkout@v2
        - name: 'Train my model'
            env:
            repo_token: ${{ secrets.GITHUB_TOKEN }}
            run: |
            # Define the steps of the pipeline.

            # STEP-1: Install required packages and run the python file
            pip install -r requirements.txt
            python model.py

            # STEP-2: Create a Markdown file and write the results into that file.
                # STEP-2.1
            echo "## Model Metrics" > mlops_demo_report.md
            cat metrics.txt >> mlops_demo_report.md
                # STEP 2.2
             echo "\n## Model Performance" >> mlops_demo_report.md
             echo "Model performance metrics are on the plot below." >> mlops_demo_report.md
             cml-publish model_results.png --md >> mlops_demo_report.md
            
            # STEP 3 CML library has functinality to send comments as reports
            cml-send-comment mlops_demo_report.md
    ```
5. Click `Start commit` button and add a relevant comment.
6. Go to Actions section, and show the progress
7. At the end, in the Pull Request, there is the image

## Now what about we want to experiment our results and see how the model performs?
1. Go to the code base in `experiment_1` branch
2. Change the line with
    ```python
    model.fit(X_train, y_train, epochs=100)
    ```
    Change the epochs=50
3. Commit the changes in `experiment_1`

## Explain about comparing the results betweeen different commits and show the difference between multiple experiments, to pick the best one.