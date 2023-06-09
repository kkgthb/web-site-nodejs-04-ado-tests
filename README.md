# A tiny _well-tested_ Azure DevOps build pipeline

Accompanies 2 associated blog post on Katie Kodes:
1. "[Failing Azure DevOps Pipeline builds if unit tests fail](https://katiekodes.com/ado-fail-unit-test/)"
2. "[Deploying a built webapp onto Azure App Service with ADO Release Pipelines](https://katiekodes.com/deploy-to-azure-app-service-with-ado/)"

---

## Prerequisites

1. Please work your way through prerequisite exercises [1](https://katiekodes.com/node-hello-world/), [2](https://katiekodes.com/node-unit-test-jest/), and [3](https://katiekodes.com/ado-repo-branch-protection/).
    * At the very least, read articles [1](https://katiekodes.com/node-hello-world/) and [2](https://katiekodes.com/node-unit-test-jest/), and do exercise [3](https://katiekodes.com/ado-repo-branch-protection/).
1. Have an account in Azure DevOps.

<div class="attention-note attention-note-gentle" markdown="block">

No need to install anything onto your computer -- we'll do everything in this article through a web browser.

</div>

---

## How the pipeline will behave

The YAML-formatted file "[`/cicd/my-azure-build-pipeline.yml`](https://github.com/kkgthb/web-site-nodejs-04-ado-tests/blob/main/cicd/my-azure-build-pipeline.yml)" _(from the [sample codebase you just copied](https://github.com/kkgthb/web-site-nodejs-04-ado-tests/))_ forces any ADO Pipeline running it to walk through all of the [same sequential steps as mentioned in the previous article](https://katiekodes.com/ado-build-hello-world-from-git/#how-the-pipeline-will-behave), plus a few additional ones:

1. _(Execute only on edits to "`main`" branch, same as before.)_
1. _(Borrow a computer from Microsoft, same as before.)_
1. _(Install Node.js and NPM, same as before.)_
1. _(Install specific Node modules, same as before.)_
1. **New:**  Run unit tests.  It does this by executing a "`npm run test`" command on the agent's operating system.
1. **New:**  Publish results of unit test execution to the ADO project's "**Test Plans**" -> "**Runs**" dashboard.
1. **Conditional only if tests _PASS_:**
    * _(Build `/dist/`, same as before.)_
    * _(Copy `/dist/` to an "artifact staging directory," same as before.)_
    * _(Copy the "artifact staging directory" contents to a longer-lived "**Artifact**" named "`MyBuiltWebsite`," same as before.)_

Again, after these steps finish executing, it's safe for Azure DevOps to let the agent _(the Linux computer we borrowed from Microsoft)_ self-destruct.

---

## Running a successful build

Follow these steps from [the previous article](https://katiekodes.com/ado-build-hello-world-from-git/), with a few changes:

1. "[Importing my sample codebase into ADO Repos](https://katiekodes.com/ado-build-hello-world-from-git/#importing-my-sample-codebase-into-ado-repos), but..."
    * Only instead of importing a copy of `...03-ado-build.git`...
    * Import a copy of:
        ```
        https://github.com/kkgthb/web-site-nodejs-04-ado-tests.git
        ```
2. "Telling Azure Pipelines to build when code changes" -> "[Forcing ADO to use our YAML pipeline code](https://katiekodes.com/ado-build-hello-world-from-git/#forcing-ado-to-use-our-yaml-pipeline-code)."
3. "[Running the pipeline by editing code](https://katiekodes.com/ado-build-hello-world-from-git/#running-the-pipeline-by-editing-code)."
    * Please only edit `README.md` for now.
4. "[Validating the pipeline ran correctly](https://katiekodes.com/ado-build-hello-world-from-git/#validating-the-pipeline-ran-correctly)."
    * If you look at the job logs, you'll see that there were more steps involved to execute the pipeline.
    * Logs from the pipeline step that runs unit tests will have output that looks [somewhat like this "passing test" output from earlier on your own computer](https://katiekodes.com/node-unit-test-jest/#running-tests-that-pass).
    * In the job summary's 4th column, under "**Tests and coverage**," there should also be a clickable link that says "**100% passed**."  If you click it, you'll see a green donut chart showing that 2 of 2 tests passed.

Also follow these steps:

1. In the left-hand navigation of Azure DevOps, click on "**Test Plans**."
1. Beneath that, click "**Runs**."
    _(Note:  this particular pages's dedicated URL will be something like `https://dev.azure.com/YOUR-ADO-ORG-NAME/YOUR-ADO-PROJECT-NAME/_testManagement/runs`.)_
1. Under "**Recent test runs**" to the right, you should see a test in "completed" state.
1. Double-click it to see a lovely dashboard with green donut and bar charts indicating that 2 of 2 tests passed.
1. Click the "**Test results** tab to see each of the 2 tests listed separately _(they're also double-clickable, though in this case there's not really anything in there that adds useful detail)_.

---

## Running a failed build

Repeat the steps from the previous article's "[Running the pipeline by editing code](https://katiekodes.com/ado-build-hello-world-from-git/#running-the-pipeline-by-editing-code)" section.  Only instead of editing a `README.md` file...

Edit the contents of line 12 of the file at `/src/__tests__/my-first-test.js` and replace the word "**World**" with the word "**Goodbye**" so that instead of looking for `"Hello World!"` your test is looking for `"Hello Goodbye!"`.

Then repeat the steps from the previous article's "[Validating the pipeline ran correctly](https://katiekodes.com/ado-build-hello-world-from-git/#validating-the-pipeline-ran-correctly)" section.  Only this time:

1. Under "**Related**," the summary will say "**0 published**" and it won't be hyperlinked.
    * This is good -- we _**wanted**_ to fail the build if our unit tests failed!  Who wants to risk building bad code and leaving it lying it around where it might accidentally get deployed into production?  Not me.
2. Logs from the pipeline step that runs unit tests will have output that looks [somewhat like this "failing test" output from earlier on your own computer](https://katiekodes.com/node-unit-test-jest/#running-tests-that-fail).
3. In the job summary's 4th column, under "**Tests and coverage**," there should also be a clickable link that says "**0% passed**."  If you click it, you'll see a red donut chart showing that 0 tests passed and 2 failed.

Also follow these steps:

1. In the left-hand navigation of Azure DevOps, click on "**Test Plans**."
1. Beneath that, click "**Runs**."
    _(Note:  this particular pages's dedicated URL will be something like `https://dev.azure.com/YOUR-ADO-ORG-NAME/YOUR-ADO-PROJECT-NAME/_testManagement/runs`.)_
1. Under "**Recent test runs**" to the right, you should see a test in "completed" state.
1. Double-click it to see a lovely dashboard with red donut and bar charts indicating that 2 of 2 tests failed.
1. Click the "**Test results** tab to see each of the 2 tests listed separately _(they're also double-clickable, though in this case there's not really anything in there that adds useful detail)_.

---

## Extra credit -- test-driven development

Repeat the steps from the previous article's "[Running the pipeline by editing code](https://katiekodes.com/ado-build-hello-world-from-git/#running-the-pipeline-by-editing-code)" section.  Only instead of editing a `README.md` file...

Edit the contents of line 6 of the file at `/src/web/server.js` and replace the word "**World**" with the word "**Goodbye**" to match the unit test you updated earlier.

Then repeat the steps from the previous article's "[Validating the pipeline ran correctly](https://katiekodes.com/ado-build-hello-world-from-git/#validating-the-pipeline-ran-correctly)" section.  Once the pipeline finishesrunning, congratulate yourself that once again, your source code successfully builds into a webserver runtime "artifact" and that all your ADO Test Runs result dashboards are colored green.

---

## Deploying this codebase to live websites

See "[Deploying a built webapp onto Azure App Service with ADO Release Pipelines](https://katiekodes.com/deploy-to-azure-app-service-with-ado/)" for instructions on how to add an Azure DevOps "classic" "release" Pipeline to your copy of this codebase, so that you can see "**Hello World**" on a real web site hosted in Azure's cloud.

---

## Credits

* Rupesh Tiwari's "[Publishing Test Results Using JEST in Angular](https://www.rupeshtiwari.com/publishing-test-result-using-jest-in-angular/)" for teaching me how to reformat JEST output into JUnit format, one of the [formats that ADO's `PublishTestResults` command can read](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/publish-test-results-v2?view=azure-pipelines&tabs=trx%2Ctrxattachments%2Cyaml#inputs).
