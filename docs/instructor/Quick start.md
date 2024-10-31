# Quick start
## Introduction
After you login to the system, you will be directed to the dashboard. The dashboard is the first page you will see after you login. In the navigation bar, you will see the following options:

- Dashboard
- Courses
- Jobs
- Admin

## Courses
For now, we will focus on the Courses option. Click on the Courses option to view the list of courses you are enrolled in. You can also see other courses that are available for you to enroll in.

Let's create a course. Click on the Create Course button. You will be directed to a page where you can fill in the details of the course:

| Field                 | Description                                                          |
| --------------------- | -------------------------------------------------------------------- |
| Course Name           | The name of the course                                               |
| Course Description    | An optional, brief description of the course                         |
| Course Icon           | An optional icon for the course                                      |
| Owner                 | The owner of the course. By default, you are the owner of the course |
| Term                  | The term in which the course is being offered                        |
| Allow Self Enrollment | If enabled, students can enroll themselves in the course             |

Fill in the details and click on the Create button. You will be directed to the course page where you can add assignments and manage course members.

## Assignments
Now that you have created a course, let's add an assignment. Click on the Add Assignment button. You will be directed to a page where you can fill in the details of the assignment:

| Field                  | Description                                                                                                     |
| ---------------------- | --------------------------------------------------------------------------------------------------------------- |
| Assignment Name        | The name of the assignment                                                                                      |
| Assignment Description | An optional, brief description of the assignment                                                                |
| Due Date               | The due date of the assignment. In the future, scheduled assignments will be supported and graded automatically |

Next, you have to choose a Grading Workflow:

| Field                           | Description                                                                                                                                                                                                                               |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Workflow Name                   | The name of the workflow                                                                                                                                                                                                                  |
| Workflow Description            | An optional, brief description of the workflow                                                                                                                                                                                            |
| Docker Image                    | The Docker image that will be used to grade the assignment. Currently, this image has to be setup by the administrator, and should only be set to the default value. If you want to use a custom image, please contact the administrator. |
| Test Files                      | The test files that will be used to grade the assignment. The format is described below.                                                                                                                                                  |
| Schedule Workflow               | If enabled, the workflow will be scheduled to run automatically (Currently not supported)                                                                                                                                                 |
| Container Start Timeout         | The maximum time the container can take to start. If the container does not start within this time, the workflow will be marked as failed.                                                                                                |
| Container Stop Timeout          | The maximum time the container can take to stop.                                                                                                                                                                                          |
| Container Exec Timeout          | The maximum time the container can take to execute the grading script.                                                                                                                                                                    |
| Container Cleanup Timeout       | The maximum time the container can take to clean up.                                                                                                                                                                                      |
| Container Result Upload Timeout | The maximum time the container can take to upload the results.                                                                                                                                                                            |
| Allocated Memory                | The amount of memory allocated to the container. Note that tests might fail if the container does not have enough memory.                                                                                                                 |
| Allocated CPU Cores             | The number of CPU cores allocated to the container.                                                                                                                                                                                       |
| Priority                        | The priority of the workflow. Lower values have higher priority.                                                                                                                                                                          |

### Test Files
The test files can be any format depending on the grading container. For the default `Jagr`-based container, the test files be a zip archive containing the following files:

```plaintext title="test-files.zip"
.
├── 📂 graders
│   ├── 📦 HXX-Private-0.1.0-SNAPSHOT.jar
├── 📂 libs
│   ├── 📦 HXX-Private-0.1.0-SNAPSHOT-libs.jar
├── 📄 jagr.conf
```

## Submissions
After you have created an assignment, you can now import the submissions from Moodle. Therefore, on moodle, navigate to Course administration > Reports > Task overview. Click on the task you want to import the submissions from. Only select `submissions` as we do not need any existing feedback. Finally, click on the `Download files` button. You will get a zip archive containing the submissions.

You can also create your own archive, but it has to be in the following format (the filename of the archive does not matter, but the files inside have to be named as follows):

```plaintext title="submissions.zip"
.
├── 📂 Abgabe zu Übungsblatt XX _ Firstname Lastname - ab12cdef
│   ├── 📂 submissions
│       ├── 📦 <anyName>.jar
├── 📂 Abgabe zu Übungsblatt XX _ Firstname2 Lastname2 - bc23defg
│   ├── 📂 submissions
│       ├── 📦 <anyName2>.jar
..
```
???+ tip "Hint"
    The `submissions` folder is mandatory and has to be named exactly like that. The `<anyName>.jar` files can be named anything, but they have to be in the `submissions` folder. To be precise, only files matching
    ```ts
    /Abgabe[^0-9]*(?<assignmentId>[0-9]{1,2}) _ (?<name>[^-]*) - (?<studentId>[a-z]{2}[0-9]{2}[a-z]{4})\/submissions\/.*\.jar/
    ```
    will be imported. The `assignmentId` is the number of the assignment, the `name` is the name of the student, and the `studentId` is the student's id.

Now in Yougrade, click on the `Import Submissions` button. You will be prompted to upload the zip archive. After you have uploaded the archive, the submissions will be imported and you can start grading.

???+ tip "Hint"
    Manually refresh the page if the submissions are not displayed immediately.

## Grading
After you have imported the submissions, you can start grading. Either click the `Grade all submissions` button to grade all submissions at once, select the submissions you want to grade, and click the `Grade selected submissions` button, or click on the `Grade` button next to a submission to grade it individually. To track the status of the grading, you can click on the `Jobs` option in the navigation bar.

When the grading is finished, you should see the results of the grading in the table. For each submission, you can view a detailed report by clicking on the `View` button.  

## Exporting Grades
You can export either the grades of a single assignment or all assignments. To export the grades of a single assignment, click on the `View` button of the assignment you want to export and then click on the `Export CSV` button. To export the grades of all assignments, click on the `Export CSV` button in the Assignment's page.

## Uploading Grades to Moodle
After you have exported the grades, you can upload them to Moodle. Therefore, navigate to the assignment page, click on `Show grades` > `Actions` > `Upload grade table`. Select the exported CSV file and click on the `Upload this file` button. The grades will be uploaded to Moodle.

???+ tip "Hint"
    This process has not been verified to work for larger courses yet. It might be necessary to split the CSV file into smaller parts.
