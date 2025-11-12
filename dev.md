Ass-1
Set A:
1. Create a Jenkins freestyle job to pull source code from a Git repository, build the project using Maven, and archive the generated artifact. use git Repository: https://github.com/hkhcoder/vprofile-project.git branch: main Steps to include: 
● Create a new freestyle job. 
● Configure the Git repository in the "Source Code Management" section. 
● In the "Build" step, invoke the top-level Maven target (e.g., clean install). 
● Add a "Post-build Action" to archive the artifacts (e.g., target/*.war).

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Create a New Freestyle Job
1.	From the Jenkins dashboard, click on "New Item" in the top-left corner.
2.	Enter an item name (e.g., vprofile-project-build).
3.	Select "Freestyle project" from the list.
4.	Click "OK" at the bottom of the page.

2. Configure Source Code Management (SCM)
1.	On the job configuration page, scroll down to the "Source Code Management" section.
2.	Select "Git".
3.	In the "Repository URL" field, enter:
4.	[https://github.com/hkhcoder/vprofile-project.git](https://github.com/hkhcoder/vprofile-project.git)
5.	In the "Branch Specifier (blank for 'any')" field, change */master to:
6.	*/main
Note: The default might be master, so ensure it is set to main to match the repository's primary branch.

3. Configure the Build Step (Maven)
1.	Scroll down to the "Build" section.
2.	Click the "Add build step" dropdown menu.
3.	Select "Invoke top-level Maven targets".
4.	If you have multiple Maven versions configured in Jenkins, select the one you wish to use from the "Maven Version" dropdown. If not, you can leave it as (Default).
5.	In the "Goals" field, enter:
6.	clean install
o	clean: Deletes any previously compiled code and artifacts from the target directory.
o	install: Compiles the code, runs tests, and packages the project, installing it into your local Maven repository. This process generates the .war file in the target directory.

4. Configure Post-build Action (Archive Artifacts)
1.	Scroll down to the "Post-build Actions" section.
2.	Click the "Add post-build action" dropdown menu.
3.	Select "Archive the artifacts".
4.	In the "Files to archive" field, enter:
5.	target/*.war
This tells Jenkins to find all files ending with .war inside the target directory (which is created by the Maven build) and save them with the build record.

5. Save and Run
1.	Click the "Save" button at the bottom of the page.
2.	You will be redirected to the job's main page.
3.	Click "Build Now" in the left-hand menu to run the job.
4.	After the build is complete (indicated by a blue or green ball in the "Build History"), you can click on the build number. The archived artifact (e.g., vprofile-v1.war) will be available on the build's page.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

2. Build a Jenkins freestyle project that automates the following tasks for a Maven project: 
use git Repository: https://github.com/hkhcoder/vprofile-project.git 
branch: main 
● Pull the source code from the Git repository. 
● Build the project using Maven to generate a .war file. 
● Implement a strategy to version the .war file with the format projectname-build_id- 
time_stamp.war and store it in a separate versions directory.

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Create a New Freestyle Job

From the Jenkins dashboard, click on "New Item".

Enter an item name (e.g., vprofile-build-and-version).

Select "Freestyle project" and click "OK".


2. Configure Source Code Management (SCM)

On the configuration page, scroll to "Source Code Management".

Select "Git".

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: */main


3. Build Step 1: Invoke Maven

This step builds the project and creates the original .war file in the target directory.

Scroll down to the "Build" section.

Click "Add build step" and select "Invoke top-level Maven targets".

Maven Version: Select the Maven installation you configured in Global Tool Configuration (e.g., Default_Maven or Maven-3.9.6). This is the step that fixes the mvn: not found error.

Goals: clean install


4. Build Step 2: Version and Move Artifact

This step runs after the Maven build is complete. It finds the .war file, renames it using Jenkins variables, and moves it to your versions directory.

In the same "Build" section, click "Add build step" (so you now have two build steps).

Select "Execute shell".

Paste the following script into the "Command" box:

echo "Starting artifact versioning..."

# Find the generated .war file in the target directory.
# This is safer than hardcoding the version number.
SOURCE_WAR_FILE=$(find target -maxdepth 1 -name "*.war" | head -n 1)

# Check if a .war file was actually found
if [ -z "$SOURCE_WAR_FILE" ]; then
    echo "ERROR: No .war file found in target directory!"
    exit 1
fi

echo "Found artifact: $SOURCE_WAR_FILE"

# --- Define new file name components ---

# 1. Project Name (as requested)
PROJECT_NAME="vprofile-project"

# 2. Build ID (this is a built-in Jenkins variable)
# e.g., "15"
BUILD_ID="$BUILD_ID"

# 3. Timestamp (formatted for file names)
# e.g., "20251110-225800"
TIME_STAMP=$(date +%Y%m%d-%H%M%S)

# --- Define target directory and file name ---
TARGET_DIR="versions"
TARGET_FILE_NAME="${PROJECT_NAME}-${BUILD_ID}-${TIME_STAMP}.war"
TARGET_PATH="${TARGET_DIR}/${TARGET_FILE_NAME}"

# Create the target directory if it doesn't exist
# The '-p' flag prevents errors if the directory already exists
echo "Creating target directory: $TARGET_DIR"
mkdir -p $TARGET_DIR

# Move and rename the artifact
echo "Moving $SOURCE_WAR_FILE to $TARGET_PATH"
mv $SOURCE_WAR_FILE $TARGET_PATH

echo "Artifact versioning complete. New file is at $TARGET_PATH"


5. Post-build Action: Archive the Versioned Artifact

This step is optional but highly recommended. It saves your newly-named .war file with the Jenkins build record, so you can easily download it from the Jenkins build page.

Scroll down to the "Post-build Actions" section.

Click "Add post-build action" and select "Archive the artifacts".

In the "Files to archive" field, enter:

versions/*.war


This tells Jenkins to look inside the versions directory (which your script created) and archive any .war files it finds.


6. Save and Run

Click "Save" at the bottom of the page.

Click "Build Now" to run your new job.

When the build finishes, click on the build number in the "Build History". You should see your versioned artifact (e.g., vprofile-project-1-20251110-225901.war) listed as a build artifact, ready to be downloaded.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************



3. Schedule above job to run periodically every 5 minutes. (i.e. Q2) 

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

You can easily add a schedule to your existing job. Here’s how to set it up to run every 5 minutes.

Go to your Jenkins dashboard and click on your job (e.g., vprofile-build-and-version).

Click on "Configure" from the left-hand menu.

Scroll down to the "Build Triggers" section.

Check the box for "Build periodically".

In the "Schedule" text area that appears, paste the following cron expression:

H/5 * * * *
What this means:
H/5: The H is a Jenkins feature that means "hash" or "anywhere within." So, H/5 tells Jenkins to run the job once during every 5-minute period (e.g., at 11:01, 11:06, 11:11...). This is better than */5 as it helps spread out the load if you have many jobs running.

* * * *: These (four asterisks) represent "every hour," "every day of the month," "every month," and "every day of the week."

Finally, click "Save" at the bottom of the page. Jenkins will now automatically run this job approximately every 5 minutes.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

4. Edit above job to notify users via email whenever there is an unstable build. (i.e. Q2) 

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Part 1: Configure Jenkins to Send Email (System-Wide)
You only need to do this once, and it will work for all your jobs.

Go to System Configuration:

On the Jenkins dashboard, go to Manage Jenkins > System.

Set Jenkins Admin Email:

Scroll down to the "Jenkins Location" section.

Set the "System Admin e-mail" address. This is often used as the default "From" address for notifications (e.g., jenkins@your-company.com).

Configure SMTP Server (E-mail Notification):

Scroll to the very bottom to find the "E-mail Notification" section.

Fill in your mail server details. (This example uses Gmail, but you should use your organization's SMTP server if available).

SMTP server: smtp.gmail.com

Click the "Advanced..." button.

Check "Use SMTP Authentication".

User Name: Your full email address (e.g., your-email@gmail.com).

Password: Your email password.

Important (for Gmail/Google): You can't use your regular password. You must generate an "App Password" from your Google Account security settings and use that here.

Check "Use TLS" (if your server uses it, like Gmail).

SMTP Port: 587 (for TLS) or 465 (for SSL).

Test Your Configuration:

Check the box "Test configuration by sending test e-mail".

Enter your own email in "Test e-mail recipient".

Click the "Test configuration" button. You should receive a test email. If not, troubleshoot your SMTP settings before continuing.

Save your changes at the bottom of the System page.

Part 2: Configure Your Job to Send Notification
Now that Jenkins can send emails, you'll edit your vprofile-build-and-version job.

Go to Your Job:

From the dashboard, click on your job (e.g., vprofile-build-and-version).

Click "Configure".

Add the Post-build Action:

Scroll down to the "Post-build Actions" section.

Click the "Add post-build action" dropdown menu.

Select "E-mail Notification".

Configure the Trigger:

A new block will appear.

Recipients: Enter the email address (or multiple addresses separated by commas) of who should be notified (e.g., tejas@example.com, dev-team@example.com).

Check the box for "Send e-mail for every unstable build".

Save the job configuration.

That's it! Now, if your build completes but is marked as "unstable" (which typically happens if your code compiles but fails its own tests), Jenkins will automatically send an email to the recipients you listed.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

5. Build a jenkins freestyle job that incorporates the following elements: 
●  Pulls the latest code (The job should trigger automatically on code changes). 
●  Builds the Docker image using a Dockerfile located in the repository. 
●  Runs a container from the built image. 
●  Push Build image to Docker Hub with proper tags. 
Note: use your own GitHub account, use any backend technology (e.g. nodejs, Django, 
Flask, etc...)

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Part 1: Prerequisites (Do This First!)

Your Jenkins server (or agent) needs to be prepared.

1. Install Docker:
Ensure Docker is installed on the machine Jenkins is running on.

2. Give Jenkins Docker Permissions (CRITICAL):
By default, Jenkins cannot run Docker commands. You must add the jenkins user to the docker group.

# Run this on your Jenkins server
sudo usermod -aG docker jenkins



IMPORTANT: You must restart Jenkins after running this command for the new permissions to take effect.

(On Linux with systemd): sudo systemctl restart jenkins

(Or restart via the Jenkins UI): Go to http://<your-jenkins-url>/safeRestart

3. Store Docker Hub Credentials in Jenkins:
Never hardcode your password in a job!

Go to Manage Jenkins > Credentials.

Click on (global) under the "Stores scoped to Jenkins" section.

Click "Add Credentials" on the left.

Kind: Select "Username with password".

Scope: Global.

Username: Your Docker Hub username (e.g., my-docker-user).

Password: Your Docker Hub password or an Access Token (Access Token is recommended).

ID: Give it a memorable, simple ID, like dockerhub-creds.

Click "OK".

Part 2: Your Sample Node.js Project

Create a new GitHub repository and add the following three files. This will be a simple "Hello World" web app.

File 1: server.js

const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Hello from Jenkins Docker Build! This is version 2.');
});

app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});



File 2: package.json

{
  "name": "jenkins-docker-demo",
  "version": "1.0.0",
  "description": "Simple app for Jenkins",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}



File 3: Dockerfile

# Use an official Node.js runtime as a parent image
FROM node:18-alpine

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json (if available)
COPY package*.json ./

# Install app dependencies
RUN npm install

# Bundle app's source code
COPY . .

# Expose port 3000
EXPOSE 3000

# Define environment variable (optional)
ENV NAME="JenkinsDemo"

# Run the app
CMD [ "npm", "start" ]



Commit and push these three files to your new GitHub repository (e.g., https://github.com/your-username/my-docker-app.git).

Part 3: Jenkins Job Configuration

1. Create the Freestyle Job:

Go to New Item > Freestyle project.

Name it (e.g., docker-build-and-push).

Click "OK".

2. Configure Source Code Management (SCM):

Select "Git".

Repository URL: Enter the URL of the GitHub repo you just created (e.g., https://github.com/your-username/my-docker-app.git).

Branch Specifier: */main (or */master depending on your repo).

3. Configure the Build Trigger (Automatic):

Go to the "Build Triggers" section.

Check the box for "GitHub hook trigger for GITScm polling".

Set up the Webhook in GitHub:

Go to your GitHub repository.

Click Settings > Webhooks > Add webhook.

Payload URL: http://<YOUR_JENKINS_URL>/github-webhook/ (Make sure the trailing slash / is there).

Content type: application/json.

Secret: Leave blank for now.

Which events...: Select "Just the push event."

Click "Add webhook". You should see a green checkmark if Jenkins is reachable from GitHub.

4. Configure the Build Environment (Credentials):
This securely injects your Docker Hub credentials into the build.

Go to the "Build Environment" section.

Check the box "Use secret text(s) or file(s)".

Click "Add" > "Username and password (separated)".

Credentials: Select the credential ID you created in Part 1 (e.g., dockerhub-creds).

Username Variable: DOCKER_USERNAME

Password Variable: DOCKER_PASSWORD

(Jenkins will now create two environment variables, $DOCKER_USERNAME and $DOCKER_PASSWORD, that your script can use).

5. Add the "Execute Shell" Build Step:
This is where all the logic happens.

Go to the "Build" section.

Click "Add build step" > "Execute shell".

Paste the following script into the "Command" box.

IMPORTANT: Change the DOCKERHUB_USERNAME variable to your actual Docker Hub username.

echo "--- Starting Docker Build and Push ---"

# --- 1. Define Variables ---
# !! CHANGE THIS to your Docker Hub username !!
DOCKERHUB_USERNAME="your-dockerhub-username"

# This will be the name of your image
IMAGE_NAME="my-node-app"

# We will tag the image with the Jenkins Build Number
# e.g., "my-node-app:1", "my-node-app:2"
IMAGE_TAG="v${BUILD_NUMBER}"

# This is the full name for Docker Hub
# e.g., "your-dockerhub-username/my-node-app:v1"
FULL_IMAGE_NAME="${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"

# This is the name for the running container
CONTAINER_NAME="my-node-app-container"

echo "Building image: ${FULL_IMAGE_NAME}"

# --- 2. Build the Docker Image ---
# The "." means "use the Dockerfile in the current directory"
docker build -t ${FULL_IMAGE_NAME} .

echo "--- Running Container (Test) ---"

# --- 3. Run the Container ---
# Stop and remove any old container with the same name
# "|| true" prevents the script from failing if the container doesn't exist
docker stop ${CONTAINER_NAME} || true
docker rm ${CONTAINER_NAME} || true

# Run the new container in detached mode (-d)
# Map port 3000 on the host to 3000 in the container
docker run -d -p 3000:3000 --name ${CONTAINER_NAME} ${FULL_IMAGE_NAME}

echo "Container is running. Pausing for 5 seconds to ensure it's stable..."
sleep 5

# Check if the container is actually running
docker ps -f "name=${CONTAINER_NAME}"
echo "Container test complete."

echo "--- Pushing Image to Docker Hub ---"

# --- 4. Log in and Push to Docker Hub ---
# Use the credentials we injected from the "Build Environment" step
# --password-stdin is the most secure way to log in
echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

# Push the image
docker push ${FULL_IMAGE_NAME}

# Optional: Also push a "latest" tag
docker tag ${FULL_IMAGE_NAME} "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"
docker push "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"

echo "--- Build and Push Complete ---"

# Optional: Clean up local image to save space
docker rmi ${FULL_IMAGE_NAME}



6. Save and Run:

Click "Save".

Click "Build Now" to run it manually the first time.

Check the "Console Output" to watch the script run. You should see it build, run, and push.

Test the trigger: Go to your GitHub repo, edit server.js (e.g., change the "Hello World" message), and commit the change. A new Jenkins build should start automatically within a minute or two.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set B=

1. Create a Jenkins freestyle job that automates the following for a Maven project: 
use git Repository: https://github.com/hkhcoder/vprofile-project.git 
branch: atom 
● Pull the source code from a Git repository. 
● Build the project using Maven. The build process should execute a series of goals, 
including clean, package, and jacoco:report. 
● Implement a conditional build step that runs unit tests with a code coverage 
threshold. If code coverage falls below 70%, the build should be marked as unstable, 
but not failed. 
● Archive the generated .war file and the JaCoCo test reports. 
● Implement a versioning strategy for the .war file using a combination of the project 
name, the Jenkins build ID, and a custom build parameter (e.g., 
RELEASE_VERSION). The final artifact should be named 
projectname-RELEASE_VERSION-build_id.war and stored in a dedicated releases 
directory.
Answer=
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Create Job & Add Build Parameter

First, create the job and add the RELEASE_VERSION parameter.

From the Jenkins dashboard, click on "New Item".

Enter an item name (e.g., vprofile-jacoco-build).

Select "Freestyle project" and click "OK".

On the configuration page, go to the "General" tab.

Check the box "This project is parameterized".

Click "Add Parameter" and select "String Parameter".

Fill in the fields:

Name: RELEASE_VERSION

Default Value: 1.0.0 (or 1.0-SNAPSHOT)

Description: "The release version for this build. This will be part of the artifact name."

2. Configure Source Code Management (SCM)

This step pulls the code from the specified atom branch.

Scroll down to the "Source Code Management" section.

Select "Git".

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: */atom (Note: The default is */main or */master, you must change it to */atom).

3. Configure Build Steps

You will add two build steps in sequence: one to build the project and one to rename the artifact.

Build Step 1: Invoke Maven Goals

Scroll down to the "Build" section.

Click "Add build step" and select "Invoke top-level Maven targets".

Maven Version: Select your configured Maven installation (e.g., Default_Maven).

Goals: clean package jacoco:report

clean: Deletes previous builds.

package: Compiles and creates the .war file.

jacoco:report: Generates the JaCoCo code coverage reports (usually in target/site/jacoco).

Build Step 2: Execute Shell (for Versioning)

Click "Add build step" (in the same "Build" section).

Select "Execute shell".

Paste the following script into the "Command" box. This script will find the .war file, create the releases directory, and rename the file using the Jenkins variables.

echo "--- Starting Artifact Versioning ---"

# --- 1. Define Variables ---

# This is the project name you want in the file
PROJECT_NAME="vprofile-project"

# These variables are automatically provided by Jenkins:
# $RELEASE_VERSION (from the build parameter you created)
# $BUILD_ID (the Jenkins build number, e.g., "1", "2")

echo "Release Version: $RELEASE_VERSION"
echo "Build ID: $BUILD_ID"

# --- 2. Find Source Artifact ---
# Find the .war file Maven created in the 'target' directory
SOURCE_WAR_FILE=$(find target -maxdepth 1 -name "*.war" | head -n 1)

if [ -z "$SOURCE_WAR_FILE" ]; then
    echo "ERROR: No .war file found in target directory. Aborting."
    exit 1
fi

echo "Found source artifact: $SOURCE_WAR_FILE"

# --- 3. Create Target Directory and New Name ---
TARGET_DIR="releases"
TARGET_FILE_NAME="${PROJECT_NAME}-${RELEASE_VERSION}-${BUILD_ID}.war"
TARGET_PATH="${TARGET_DIR}/${TARGET_FILE_NAME}"

echo "Creating directory: $TARGET_DIR"
mkdir -p $TARGET_DIR

# --- 4. Move and Rename Artifact ---
echo "Moving $SOURCE_WAR_FILE to $TARGET_PATH"
mv $SOURCE_WAR_FILE $TARGET_PATH

echo "--- Artifact Versioning Complete ---"


4. Configure Post-build Actions

This is where you will archive the files and, most importantly, set the code coverage threshold.

Post-build Action 1: Record JaCoCo Coverage Report (Conditional Step)

Scroll down to the "Post-build Actions" section.

Click "Add post-build action" and select "Record JaCoCo coverage report". (This option only appears if the JaCoCo plugin is installed).

In the "Path to JaCoCo reports" field, enter the path to the XML report. The default for jacoco:report is:
target/site/jacoco/jacoco.xml

Click the "Advanced..." button to show the threshold settings.

Find the "Set minimum coverage thresholds and build health" section.

You can set thresholds for different metrics (Instructions, Branch, Line, etc.). Let's use Line coverage.

In the row for Line, enter 70 in the "Unstable" column.

Leave the "Failed" column at 0.

This configuration means: If Line coverage is less than 70%, mark the build as "Unstable" (yellow), but don't fail it (red). This matches your requirement perfectly.

Post-build Action 2: Archive the Artifacts

Click "Add post-build action" again.

Select "Archive the artifacts".

In the "Files to archive" field, enter the paths to both the .war file and the reports, separated by a comma:

releases/*.war, target/site/jacoco/**


releases/*.war: Archives your newly named .war file.

target/site/jacoco/**: Archives the full HTML JaCoCo report so you can browse it from the Jenkins build page.

5. Save and Run

Click "Save".

You will be taken to the job's main page. Since it's parameterized, you must click "Build with Parameters".

Confirm the RELEASE_VERSION (or change it) and click "Build".

Summary of Job Flow

You click "Build with Parameters" and provide a RELEASE_VERSION.

Jenkins checks out the atom branch.

Maven runs clean package jacoco:report, creating target/vprofile-v1.war and target/site/jacoco/....

The shell script runs, moving target/vprofile-v1.war to releases/vprofile-project-1.0.0-1.war (assuming version 1.0.0 and build ID 1).

The JaCoCo plugin runs, reads target/site/jacoco/jacoco.xml, and checks the line coverage. If it's 65%, it marks the build as UNSTABLE.

The "Archive Artifacts" step saves both releases/vprofile-project-1.0.0-1.war and the target/site/jacoco directory for you to review.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
2. Create a Jenkins freestyle job that extends the basic Docker workflow with more advanced 
features: 
● Configure the job to automatically trigger whenever new code is pushed to the main 
branch of your Git repository using a Git SCM. 
● Build a Docker image from a Dockerfile located in the repository. The build should 
include passing environment variables (e.g., BUILD_ID, GIT_COMMIT) to the 
Dockerfile as build arguments. 
● Implement a multi-stage Docker build within the Dockerfile to produce a smaller, 
optimized final image. 
● Tag the built image with a dynamic tag that includes the Git commit hash and the 
Jenkins build number (e.g., 
your-dockerhub-username/your-app:git-commit-hash-build-id). 
● Push the tagged image to Docker Hub. 
● Configure email notifications to be sent to a specific user group for both unstable 
and failed builds. The email body should include the build status, a link to the build 
console output, and a list of changes from the last successful build.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Part 1: Prerequisites (Plugins & System Config)
This job requires a more advanced email plugin.

Install the "Email Extension" Plugin:

Go to Manage Jenkins > Plugins > Available plugins.

Search for and install "Email Extension Plugin" (also called email-ext). This plugin is required to customize the email body with the changelog.

Configure System Email (SMTP):

You must have already configured Jenkins to send emails.

Go to Manage Jenkins > System.

Fill out the "E-mail Notification" section with your SMTP server details (as we did in a previous task).

Also fill out the "Extended E-mail Notification" section at the bottom (it's added by the plugin). You can often just copy your SMTP settings here. This is what the plugin will use.

Ensure Docker Credentials are Stored:

Make sure your Docker Hub credentials are stored in Manage Jenkins > Credentials with an ID like dockerhub-creds.

Part 2: The Multi-Stage Dockerfile
For this project, you need a new Dockerfile in your repository that uses multi-stage builds and accepts build arguments.

Here is a sample Node.js project you can use. Put these 3 files in your repository (the one from the previous task will work, just replace the Dockerfile).

File 1: Dockerfile (Multi-stage & with Build Args)

Dockerfile

# --- Stage 1: The Builder ---
# Use a full Node.js image to install dependencies
FROM node:18 AS builder

WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the source code
COPY . .

# --- Stage 2: The Final Image ---
# Use a slim, alpine-based image for the smallest size
FROM node:18-alpine

WORKDIR /app

# --- Build Arguments ---
# These ARGs are passed in from the 'docker build' command
ARG BUILD_ID=unknown
ARG GIT_COMMIT=unknown

# --- Environment Variables ---
# Set ARGs as ENV so the running app can access them (optional)
ENV BUILD_ID=$BUILD_ID
ENV GIT_COMMIT=$GIT_COMMIT

# Copy the installed modules from the 'builder' stage
COPY --from=builder /app/node_modules ./node_modules

# Copy the application code
COPY server.js .

# Expose the port the app runs on
EXPOSE 3000

# Run the app
CMD [ "node", "server.js" ]
File 2: server.js (Updated to show build info)

JavaScript

const express = require('express');
const app = express();
const PORT = 3000;

// Read build info from environment variables
const buildId = process.env.BUILD_ID || 'Not Set';
const gitCommit = process.env.GIT_COMMIT || 'Not Set';

app.get('/', (req, res) => {
  res.send(`
    <h1>Hello from the Advanced Docker Build!</h1>
    <p>This app was built by Jenkins.</p>
    <ul>
      <li><b>Build ID:</b> ${buildId}</li>
      <li><b>Git Commit:</b> ${gitCommit}</li>
    </ul>
  `);
});

app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});
File 3: package.json

JSON

{
  "name": "jenkins-advanced-docker",
  "version": "1.0.0",
  "description": "App for advanced CI/CD",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}
Commit and push these files to the main branch of your GitHub repository.

Part 3: Jenkins Job Configuration
1. Create Job and Configure SCM:

Create a new Freestyle project (e.g., advanced-docker-pipeline).

Source Code Management: Select "Git".

Repository URL: Your GitHub repo URL.

Branch Specifier: */main

2. Configure Build Trigger:

Go to the "Build Triggers" section.

Check "GitHub hook trigger for GITScm polling".

Set up the Webhook in your GitHub repo's Settings > Webhooks page. The Payload URL is http://<YOUR_JENKINS_URL>/github-webhook/. (This will trigger the job on every git push).

3. Configure Build Environment (Credentials):

Go to the "Build Environment" section.

Check "Use secret text(s) or file(s)".

Click "Add" > "Username and password (separated)".

Credentials: Select dockerhub-creds.

Username Variable: DOCKER_USERNAME

Password Variable: DOCKER_PASSWORD

4. Configure the Build Step (Execute Shell): This script is the core of the job. It gets the Git variables, defines the tag, and passes the build arguments.

Go to the "Build" section, click "Add build step" > "Execute shell".

Paste in the following script (remember to change the DOCKERHUB_USERNAME):

Bash

echo "--- Starting Advanced Docker Build ---"

# --- 1. Define Variables ---
# !! CHANGE THIS to your Docker Hub username !!
DOCKERHUB_USERNAME="your-dockerhub-username"
IMAGE_NAME="my-advanced-app"

# --- 2. Get Dynamic Variables ---
# These variables are injected by the Jenkins Git plugin
echo "Jenkins Build ID: $BUILD_ID"
echo "Git Commit Hash: $GIT_COMMIT"

# Create a short 7-character hash for the tag
GIT_HASH_SHORT=$(echo $GIT_COMMIT | cut -c1-7)
echo "Short Git Hash: $GIT_HASH_SHORT"

# --- 3. Define Dynamic Tag ---
# Format: git-commit-hash-build-id
DYNAMIC_TAG="git-${GIT_HASH_SHORT}-build-${BUILD_ID}"
FULL_IMAGE_NAME="${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${DYNAMIC_TAG}"
LATEST_IMAGE_NAME="${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"

echo "Building image: $FULL_IMAGE_NAME"

# --- 4. Build the Docker Image ---
# This is the key step:
# --build-arg passes variables from Jenkins *into* the Dockerfile
docker build \
  --build-arg BUILD_ID=$BUILD_ID \
  --build-arg GIT_COMMIT=$GIT_COMMIT \
  -t $FULL_IMAGE_NAME \
  .

echo "Build complete."

# --- 5. Log in and Push to Docker Hub ---
echo "Logging into Docker Hub..."
echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

echo "Pushing image: $FULL_IMAGE_NAME"
docker push $FULL_IMAGE_NAME

# Also tag this build as 'latest' and push it
echo "Tagging and pushing 'latest'..."
docker tag $FULL_IMAGE_NAME $LATEST_IMAGE_NAME
docker push $LATEST_IMAGE_NAME

echo "--- Build and Push Complete ---"
5. Configure Post-build Actions (Advanced Email): This is the final requirement.

Go to the "Post-build Actions" section.

Click "Add post-build action" and select "Editable Email Notification" (the one from the new plugin).

Project Recipient List: Enter the email address or group (e.g., tejas@example.com, dev-team@example.com).

Content Type: Select HTML (text/html).

Default Subject: Paste this: Build ${BUILD_STATUS}: ${PROJECT_NAME} - Build #${BUILD_NUMBER}

Default Content: This is the most important part. Paste this HTML/token script:

HTML

<h2>Build ${BUILD_STATUS} for ${PROJECT_NAME} (Build #${BUILD_NUMBER})</h2>
<hr>

<p>
  Check the console output for full details:
  <a href="${BUILD_URL}console">${BUILD_URL}console</a>
</p>

<h3>Changes Since Last Successful Build:</h3>

<ul>
  ${CHANGES_SINCE_LAST_SUCCESS, 
    format="<li><b>%c</b> (%a) - %m</li>",
    pathFormat="<a href='${FILE_LINK}'>%f</a><br>"
  }
</ul>

<hr>
<p><i>This is an automated notification from Jenkins.</i></p>
$BUILD_STATUS, $PROJECT_NAME, $BUILD_NUMBER, and $BUILD_URL are all tokens provided by the plugin.

${CHANGES_SINCE_LAST_SUCCESS} is the special token that lists the Git commits.

Add Triggers for Sending the Email:

At the bottom of the email block, click "Add Trigger".

Select "Unstable".

Click "Add Trigger" again.

Select "Failure - Any".

6. Save and Run:

Click "Save".

Push a new commit to your main branch.

The job will automatically start, build the image with the build args, tag it with the commit hash, and push it to Docker Hub.

If it fails, it will send the detailed email you just configured.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
3. Create two linked Jenkins jobs to demonstrate a basic CI/CD pipeline: 

use git Repository: https://github.com/hkhcoder/vprofile-project.git 

branch: main 

I. Job A (Build Job): 

● Create a parameterized build job that takes a string parameter for 

GIT_BRANCH and a choice parameter for BUILD_ENV (e.g., development, 

staging). 

● Pull code from the specified Git branch. 

● Build the Maven project with a goal to generate a .jar or .war file. 

● Archive the build artifact. 

II. Job B (Deploy Job): 

● Create a second parameterized job that is triggered after a successful build of 

Job A. 

● The deploy job should receive the BUILD_ID and BUILD_ENV parameters 

from Job A. 

● Copy the archived artifact from Job A's workspace to a designated 

deployment directory. 
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Part 1: Create Job A (Build Job)

This job will be parameterized, build the Maven project, and archive the resulting .war file.

Create the Job:

Click "New Item" on the Jenkins dashboard.

Enter the name job-a-build.

Select "Freestyle project" and click "OK".

Add Parameters (General tab):

In the "General" section, check the box "This project is parameterized".

Parameter 1:

Click "Add Parameter" > "String Parameter".

Name: GIT_BRANCH

Default Value: main

Description: "The Git branch to build (e.g., main, develop)."

Parameter 2:

Click "Add Parameter" > "Choice Parameter".

Name: BUILD_ENV

Choices: (Enter one per line)

development
staging


Description: "The target environment for this build."

Configure Source Code Management (SCM):

Scroll to "Source Code Management".

Select "Git".

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: $GIT_BRANCH

(This is the key: it uses the parameter you just created.)

Configure Build Step (Maven):

Scroll to the "Build" section.

Click "Add build step" > "Invoke top-level Maven targets".

Maven Version: Select your configured Maven (e.g., Default_Maven).

Goals: clean package

(This project builds a .war file, so package is the correct goal.)

Configure Post-Build Actions:
This job needs to do two things after building: archive its own artifact, and then trigger Job B.

Action 1: Archive the artifact

Click "Add post-build action" > "Archive the artifacts".

Files to archive: target/*.war

Action 2: Trigger the deploy job

Click "Add post-build action" > "Trigger/call builds on other projects".

Projects to build: job-b-deploy (You will create this job next, but type the name in now).

Trigger when build is: Stable.

Click the "Add Parameters" button and select "Predefined parameters".

In the "Parameters" box, type:

BUILD_ENV=$BUILD_ENV


(This will take the BUILD_ENV value from Job A and pass it to Job B.)

Save the job.

Part 2: Create Job B (Deploy Job)

This job will be triggered by Job A. It will copy the artifact from Job A's workspace and "deploy" it.

Create the Job:

Click "New Item" on the Jenkins dashboard.

Enter the name job-b-deploy.

Select "Freestyle project" and click "OK".

Add Parameters (General tab):

This job also needs parameters so it can receive them from Job A.

Check "This project is parameterized".

Parameter 1:

Click "Add Parameter" > "String Parameter".

Name: BUILD_ENV

Default Value: development

Description: "Passed automatically from job-a-build."

Configure Build Steps:
This job has two build steps: copy the artifact, then deploy it.

Step 1: Copy Artifact from Job A

Click "Add build step" > "Copy artifacts from another project". (This is from the Copy Artifact plugin).

Project name: job-a-build

Which build: Upstream build that triggered this project

(This is the magic link. It automatically finds the build from Job A that started this one.)

Artifacts to copy: target/*.war

Target directory: (Leave blank to copy to the current workspace.)

Step 2: Simulate Deployment (Execute Shell)

Click "Add build step" > "Execute shell".

Paste the following script into the "Command" box:

echo "--- Starting Deployment Job ---"

# This is the Build ID from Job A, provided by the Copy Artifact plugin
echo "Deploying artifact from Job A, Build ID: $COPY_ARTIFACT_UPSTREAM_BUILD_NUMBER"

# This is the environment passed as a parameter from Job A
echo "Target Environment: $BUILD_ENV"

# Find the artifact we just copied
WAR_FILE=$(find . -maxdepth 2 -name "*.war")

if [ -z "$WAR_FILE" ]; then
  echo "ERROR: No .war file was copied from Job A!"
  exit 1
fi

echo "Found artifact: $WAR_FILE"

# Create a simulated deployment directory
DEPLOY_DIR="/tmp/deployment/$BUILD_ENV"
mkdir -p $DEPLOY_DIR

echo "Copying artifact to $DEPLOY_DIR"
cp $WAR_FILE $DEPLOY_DIR/

echo "--- Deployment Complete ---"
ls -l $DEPLOY_DIR


Save the job.

How to Run Your Pipeline

Go to the dashboard and find job-a-build.

Click the "Build with Parameters" button on the left.

You can leave the GIT_BRANCH as main.

Choose either development or staging from the BUILD_ENV dropdown.

Click "Build".

What will happen:

Job A will start. It will build the main branch.

Once Job A finishes and is "Stable", it will archive its .war file.

Job A will then automatically trigger Job B, passing the BUILD_ENV parameter (e.g., "staging") to it.

Job B will start. Its "Copy Artifact" step will look for the upstream build (Job A) and copy the .war file.

Job B's shell script will then run, find the .war file, and move it to /tmp/deployment/staging.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set C: 
1. Design, implement, and document a fully automated CI/CD free style job for a sample web 
application using Jenkins. The free style job must leverage on-demand EC2 instances as 
dynamic build agents to demonstrate elastic scaling.

Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Design Doc: Jenkins CI/CD with Dynamic EC2 Build Agents

This document outlines the design, implementation, and documentation for a Jenkins CI/CD pipeline that leverages on-demand Amazon EC2 instances as dynamic build agents. This provides "elastic scaling," where build agents are provisioned only when needed and terminated when idle, reducing cost.

We will use a sample Maven web application (vprofile-project) as the payload for our CI/CD job.

Part 1: Solution Design & Architecture

Jenkins Controller: A central Jenkins server (master). This can be running anywhere (on-prem, EC2, EKS, etc.), but it must be reachable by the build agents.

AWS (Amazon Web Services):

IAM: To grant Jenkins permission to create/delete EC2 instances.

EC2: To provide the on-demand compute instances.

AMI (Amazon Machine Image): A "template" for our build agents. This is pre-baked with Java, Git, and Maven.

Networking (VPC & Security Groups): To allow the Jenkins controller and EC2 agents to communicate securely.

Jenkins EC2 Plugin: The key component. This plugin, installed on the Jenkins controller, manages the entire lifecycle of the EC2 agents.

Jenkins Job (Freestyle): The CI/CD job. It is configured to only run on an agent with a specific "label" (e.g., ec2-maven-agent).

Workflow:

A build is triggered for the "Freestyle Job".

Jenkins sees the job requires the label ec2-maven-agent.

It checks its "Clouds" configuration and finds the EC2 Plugin can provision an agent with this label.

The EC2 Plugin uses the AWS IAM credentials to make an API call to ec2:RunInstances.

A new EC2 instance is launched from the pre-configured AMI.

Jenkins waits for the instance to boot, then connects via SSH (using a stored private key) to start the Jenkins agent process (agent.jar).

The EC2 agent connects to the controller, takes the job from the queue, and begins the build (e.g., git clone, mvn package).

After the build, the agent is idle.

After a configured "idle timeout" (e.g., 10 minutes), the EC2 Plugin terminates the instance (ec2:TerminateInstances) to save money.

Part 2: AWS Prerequisites (The "Infrastructure")

1. Create an IAM User for Jenkins:
This user will have programmatic access only.

Go to IAM > Users > Create user.

Name: jenkins-ec2-manager

Attach a new policy. Click "Create policy", go to the JSON editor, and paste this:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances",
                "ec2:DescribeInstances",
                "ec2:DescribeRegions",
                "ec2:DescribeSubnets",
                "ec2:DescribeImages",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeInstanceStatus",
                "ec2:TerminateInstances",
                "ec2:CreateTags",
                "ec2:StopInstances"
            ],
            "Resource": "*"
        }
    ]
}


Save the policy (e.g., JenkinsEC2ManagementPolicy) and attach it to the jenkins-ec2-manager user.

On the final screen, save the Access key ID and Secret access key.

2. Create an EC2 SSH Key Pair:
This is how Jenkins will SSH into the new agents.

Go to EC2 > Network & Security > Key Pairs.

Click "Create key pair".

Name: jenkins-agent-key

Format: .pem

Save the jenkins-agent-key.pem file.

3. Create Security Groups:

Agent Security Group (jenkins-agent-sg):

This group will be for the dynamic agents.

Inbound Rules:

Type: SSH (port 22)

Source: The public IP of your Jenkins controller (or its security group ID if both are in the same AWS account). This is critical for security.

Outbound Rules:

Allow all (so it can download Maven dependencies, etc.).

Controller Security Group (Modify existing):

Ensure your Jenkins controller's security group allows inbound traffic on port 8080 (or your Jenkins port) from the jenkins-agent-sg so the agents can connect back.

4. Create the Custom AMI (The "Golden Image"):
This is the most important step. We create a "template" for all our build agents.

Launch a new, basic EC2 instance (e.g., Amazon Linux, Ubuntu). Use the jenkins-agent-sg and jenkins-agent-key.

SSH into the instance: ssh -i jenkins-agent-key.pem ec2-user@<instance-ip>

Install all required build tools:

# Example for Amazon Linux
sudo yum update -y

# Install Java (required for agent.jar)
sudo yum install -y java-11-amazon-corretto-devel

# Install Git
sudo yum install -y git

# Install Maven
sudo yum install -y maven


Create a home directory for the Jenkins agent to work in.

sudo mkdir -p /opt/jenkins
sudo chown -R ec2-user:ec2-user /opt/jenkins


(Note: We are using the default ec2-user for simplicity. A better practice is to create a dedicated jenkins user.)

Go to the EC2 console, find this running instance, and select "Actions" > "Image & Templates" > "Create image".

Image name: jenkins-maven-agent-ami

Click "Create image". Once it's "Available", copy its AMI ID (e.g., ami-0abcdef123456789).

You can now terminate the instance you used to create the AMI.

Part 3: Jenkins Configuration (The "Controller")

1. Install the EC2 Plugin:

Go to Manage Jenkins > Plugins > Available plugins.

Search for and install "Amazon EC2" plugin.

Restart Jenkins.

2. Add AWS Credentials to Jenkins:

Go to Manage Jenkins > Credentials > System > (global).

Credential 1: AWS Credentials:

Click "Add Credentials".

Kind: AWS Credentials

ID: aws-ec2-creds

Access Key ID: The key you saved from Part 2, Step 1.

Secret Access Key: The secret you saved.

Credential 2: EC2 SSH Key:

Click "Add Credentials".

Kind: SSH Username with private key

ID: ec2-ssh-key

Username: ec2-user (or the user you set up on the AMI).

Private Key: Select "Enter directly" and paste the entire contents of your jenkins-agent-key.pem file.

3. Configure the EC2 Cloud:

Go to Manage Jenkins > System > Cloud (at the very bottom).

Click "Add a new cloud" > "Amazon EC2".

Name: AWS-EC2

AWS Credentials: Select aws-ec2-creds.

EC2 Region: Select the region where you created your AMI.

Click "Test Connection". You should see "Success".

AMI: Click "Add AMI".

AMI ID: ami-0abcdef123456789 (The one you copied).

Instance Type: t2.micro (or any you want).

Security group(s): jenkins-agent-sg

Remote root directory: /opt/jenkins (The folder you created on the AMI).

Credentials: Select ec2-ssh-key.

Labels: ec2-maven-agent (This is the key that links the job).

Usage: "Only build jobs with label expressions matching this node".

Idle termination time: 10 (minutes). This is the "elastic" part.

Click "Save".

Part 4: The Jenkins Freestyle Job (The "Implementation")

Now, create the job that will use this setup.

Go to New Item > Freestyle project.

Name: vprofile-ec2-build

General (Critical Step):

Check the box "Restrict where this project can be run".

Label Expression: ec2-maven-agent

(This tells Jenkins it must find an agent with this label. It will trigger the EC2 plugin.)

Source Code Management:

Git

Repository URL: https://github.com/hkhcoder/vprofile-project.git

Branch Specifier: */main

Build:

Add build step > "Invoke top-level Maven targets".

Goals: clean package

Post-build Actions:

Add post-build action > "Archive the artifacts".

Files to archive: target/*.war

Save the job.

Part 5: Run and Observe

Click "Build Now" on the vprofile-ec2-build job.

Go to the "Build Executor Status" on the left menu of the main dashboard.

You will see a new agent appear, "Provisioning (AWS-EC2)..." and then "Connecting...".

Go to your EC2 console. You will see a new t2.micro instance launching and running.

The build will run on that EC2 instance.

After the build finishes, the agent will be "Idle".

Wait 10 minutes (or whatever you set for the idle time). The EC2 plugin will terminate the instance, and it will disappear from your EC2 console.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************


Ass-2
Set A:
1. Create a new Jenkins Pipeline job and configure it to use a Jenkinsfile with the following 
specifications: 
use git Repository: https://github.com/hkhcoder/vprofile-project.git 
branch: main 
A. Pipeline Configuration: 
● The pipeline must run on an agent with the label builtinubuntu. 
● It must specify the use of maven "maven 3.9.10" and jdk "openjdk 21.0.7" 
from the global tool configurations. 
B. Stage Implementation (in this specific order): 
● Stage 1: fetch code 
This stage should clone the specified Git repository and branch. 
● Stage 2: unit test 
This stage should compile the code and run all unit tests using the Maven 
command: sh 'mvn test'.
● Stage 3: build the code 
This stage should package the application into a .war file without running the 
tests again. Use the command: sh 'mvn package -DskipTests'. 
● Stage 4: archive the artifact 
This stage should find and archive the generated .war file. Use the 
archiveArtifacts step to save **/*.war. 
● Stage 5: deploy to staging 
This stage should only run if all previous stages have succeeded. 
Simulate a deployment by printing a message to the console: echo 
'Deploying artifact to staging server.'. 
C. Failure Handling: 
● Implement a pipeline-level post block that triggers only on failure. 
● If any stage fails, the pipeline should execute a shell command to print the 
message: echo 'Build failed. Notifying the development team.'. 
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // A. Pipeline Configuration:
    // Run on an agent with the specified label
    agent { label 'builtinubuntu' }

    // Specify the tools from Global Tool Configuration
    tools {
        maven 'maven 3.9.10'
        jdk 'openjdk 21.0.7'
    }

    stages {
        // B. Stage Implementation:
        
        // Stage 1: fetch code
        stage('fetch code') {
            steps {
                // Clone the specified git repository and branch
                git branch: 'main', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }

        // Stage 2: unit test
        stage('unit test') {
            steps {
                // Compile code and run unit tests
                sh 'mvn test'
            }
        }

        // Stage 3: build the code
        stage('build the code') {
            steps {
                // Package the application, skipping tests
                sh 'mvn package -DskipTests'
            }
        }

        // Stage 4: archive the artifact
        stage('archive the artifact') {
            steps {
                // Find and archive the .war file
                archiveArtifacts artifacts: '**/*.war', followSymlinks: false
            }
        }

        // Stage 5: deploy to staging
        stage('deploy to staging') {
            steps {
                // This stage only runs if all previous stages succeeded
                echo 'Deploying artifact to staging server.'
            }
        }
    }

    post {
        // C. Failure Handling:
        // This block runs after all stages are complete
        failure {
            // This only runs if the build status is 'FAILURE'
            echo 'Build failed. Notifying the development team.'
        }
    }
}

1. Prerequisite: Check Global Tool Configuration

This pipeline will only work if your Jenkins administrator has set up the tools with the exact names specified.

Go to Manage Jenkins > Tools:

Under "JDK installations", you must have an entry named openjdk 21.0.7.

Under "Maven installations", you must have an entry named maven 3.9.10.

You must have a build agent (or the controller) with the label builtinubuntu.

If these names are different (e.g., maven-3.9), you must update the tools section of the Jenkinsfile to match.

2. Create the Pipeline Job

From the Jenkins dashboard, click "New Item".

Enter a name for your job (e.g., vprofile-pipeline).

Select "Pipeline" as the job type and click "OK".

3. Configure the Pipeline

On the job configuration page, scroll down to the "Pipeline" section at the bottom.

Make sure the "Definition" dropdown is set to "Pipeline script".

In the "Script" text area, copy and paste the entire contents of the Jenkinsfile I provided.

4. Save and Run

Click "Save".

Click "Build Now" from the job's main page.

Jenkins will now execute your pipeline, pulling the tools you specified and running each stage in order.


**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

2. Automate the process of building and testing a simple Node.js application using Jenkins 
Pipeline and Docker.Write a Jenkins Declarative Pipeline that performs the following tasks: 
use GitHub repository:  https://github.com/ajay-raut/spnodeserver.git 
branch: main 
● Uses the agent builtin. 
● Fetches the application code from the GitHub repository: 
● Builds a Docker image named my-node-app. 
● Runs a container from the built image with the name nodeserver on port 5000. 
● Waits for 10 seconds and then stops and removes the container. 
● Displays the running containers list before stopping. 
● On pipeline failure, automatically sends an email notification to the developer at 
username@gmail.com with build details.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // 1. Use the agent with the label 'builtin'
    agent { label 'builtin' }

    stages {
        // 2. Fetch code from the repository
        stage('Fetch Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ajay-raut/spnodeserver.git'
            }
        }

        // 3. Build the Docker image
        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image my-node-app...'
                // The '.' means build from the Dockerfile in the current directory
                sh 'docker build -t my-node-app .'
            }
        }

        // 4. Run, Test, and Stop the container
        stage('Run & Test Container') {
            steps {
                // We use a script block to allow for try/finally,
                // which is the best practice for container cleanup.
                script {
                    try {
                        // 5. Clean up any old container with the same name
                        echo 'Cleaning up any old containers...'
                        sh 'docker stop nodeserver || true'
                        sh 'docker rm nodeserver || true'

                        // 6. Run the new container
                        echo 'Running container nodeserver on port 5000...'
                        // This maps host port 5000 to the container's port 8080 (which the app uses)
                        sh 'docker run -d -p 5000:8080 --name nodeserver my-node-app'

                        // 7. Wait for 10 seconds
                        echo 'Container started. Waiting 10 seconds for it to stabilize...'
                        sleep 10

                        // 8. Display the running container
                        echo '--- Displaying running containers (test pass) ---'
                        sh 'docker ps -f "name=nodeserver"'

                    } finally {
                        // 9. Stop and remove the container
                        // This 'finally' block runs whether the 'try' block
                        // succeeded or failed, ensuring we always clean up.
                        echo '--- Test complete. Stopping and removing container. ---'
                        sh 'docker stop nodeserver || true'
                        sh 'docker rm nodeserver || true'
                    }
                }
            }
        }
    }

    // 10. Failure handling
    post {
        failure {
            echo 'Build failed. Sending email notification...'
            // Send an email to the specified address
            mail to: 'username@gmail.com',
                 subject: "Build FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                 body: """
                    <p>The build for ${env.JOB_NAME} (Build #${env.BUILD_NUMBER}) failed.</p>
                    <p>Check the console output here: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>
                 """
        }
    }
}

Here's how to set up and run this pipeline in Jenkins.

Part 1: Prerequisites (CRITICAL!)

This pipeline will fail if you don't complete these steps on your Jenkins server.

1. Docker Permissions:
The Jenkins user (jenkins) must be able to run Docker commands.

On your agent (the node with the builtin label): Make sure Docker is installed.

Add jenkins to the docker group:

sudo usermod -aG docker jenkins


Restart Jenkins: You MUST restart the Jenkins service for this permission to take effect.

sudo systemctl restart jenkins


2. Email Configuration:
For the failure notification to work, Jenkins must be configured to send emails.

Go to Manage Jenkins > System.

Scroll down to the "E-mail Notification" section.

Enter your SMTP server details (e.g., smtp.gmail.com).

Click "Advanced..." and enter your SMTP username and password.

Use the "Test configuration by sending test e-mail" checkbox to ensure it works.

Part 2: Create the Pipeline Job

From the Jenkins dashboard, click "New Item".

Enter a name for your job (e.g., node-docker-pipeline).

Select "Pipeline" and click "OK".

On the configuration page, scroll to the bottom to find the "Pipeline" section.

In the "Definition" dropdown, select "Pipeline script".

In the "Script" text area, copy and paste the entire Jenkinsfile_node_docker script.

Click "Save".

Now you can click "Build Now" to run your pipeline.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

3. securely run a PostgreSQL container inside a Jenkins Declarative Pipeline. 
Write a Jenkins Pipeline with the following requirements: 
● Use the agent builtin. 
● In the pipeline, run a Docker container named some-postgres using the official 
postgres image.
● The container should be started with an environment variable 
POSTGRES_PASSWORD, but the password must not be hardcoded in the pipeline. 
Instead: 
○ Store the password in Jenkins credentials with ID postgres-password. 
○ Use withCredentials to securely inject the password into the pipeline. 
● Ensure the container runs in detached mode (-d).
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // 1. Use the agent with the label 'builtin'
    agent { label 'builtin' }

    stages {
        stage('Run Secure PostgreSQL Container') {
            steps {
                // We use a script block to allow for try/finally.
                // This ensures the container is always cleaned up.
                script {
                    try {
                        // 2. Clean up any old container with the same name
                        echo 'Cleaning up any old containers...'
                        sh 'docker stop some-postgres || true'
                        sh 'docker rm some-postgres || true'

                        // 3. Securely wrap the docker run command
                        // This step injects the credential 'postgres-password'
                        // into a variable named 'DB_PASSWORD'.
                        withCredentials([string(credentialsId: 'postgres-password', variable: 'DB_PASSWORD')]) {
                            
                            echo 'Starting PostgreSQL container...'
                            
                            // 4. Run the container
                            // The $DB_PASSWORD variable is securely used.
                            // Jenkins masks this variable in all logs.
                            sh """
                                docker run -d \
                                --name some-postgres \
                                -e POSTGRES_PASSWORD=$DB_PASSWORD \
                                postgres
                            """
                        }

                        // 5. Verify the container is running
                        echo 'Container started. Waiting 5 seconds...'
                        sleep 5
                        echo '--- Displaying running containers ---'
                        sh 'docker ps -f "name=some-postgres"'

                    } finally {
                        // 6. Always clean up the container
                        echo '--- Test complete. Stopping and removing container. ---'
                        sh 'docker stop some-postgres || true'
                        sh 'docker rm some-postgres || true'
                    }
                }
            }
        }
    }

    post {
        // Optional: Notify on success or failure
        success {
            echo 'PostgreSQL container test ran successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

Here's how to set up and run this pipeline in Jenkins.

Part 1: Prerequisites (CRITICAL!)

1. Docker Permissions:
The Jenkins user (jenkins) must be able to run Docker commands.

On your agent (the node with the builtin label): Make sure Docker is installed.

Add jenkins to the docker group:

sudo usermod -aG docker jenkins


Restart Jenkins: You MUST restart the Jenkins service for this permission to take effect.

sudo systemctl restart jenkins


2. Create the postgres-password Credential:
The pipeline requires a Jenkins credential with the ID postgres-password.

Go to Manage Jenkins > Credentials.

Click on (global) under "Stores scoped to Jenkins".

Click "Add Credentials" on the left.

Fill out the form:

Kind: Select Secret text.

Secret: Enter the password you want to use for PostgreSQL (e.g., my-super-secret-password).

ID: postgres-password (This must match the ID in the Jenkinsfile).

Description: PostgreSQL root password

Click "OK".

Part 2: Create the Pipeline Job

From the Jenkins dashboard, click "New Item".

Enter a name for your job (e.g., secure-postgres-pipeline).

Select "Pipeline" and click "OK".

On the configuration page, scroll to the bottom to find the "Pipeline" section.

In the "Definition" dropdown, select "Pipeline script".

In the "Script" text area, copy and paste the entire Jenkinsfile_postgres script.

Click "Save".

Now you can click "Build Now" to run your pipeline. Jenkins will securely handle the password and clean up the container after the run.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

4. set up a Jenkins Declarative Pipeline to build and test a Maven-based Java web 
application.Create a Jenkins pipeline that performs the following tasks: 
use GitHub repository: https://github.com/hkhcoder/vprofile-project.git 
branch: main 
Use the agent whis is aws ec2 cloud agent. 
● Configure tools: 
○ Maven version 3.9.10. 
○ JDK version openjdk 21.0.7. 
● Fetch the source code from the GitHub repository: 
https://github.com/hkhcoder/vprofile-project.git (branch: main). 
● Build the project using Maven with the command: 
● Create a directory named versions (if it does not exist). 
● Copy the generated WAR file (target/vprofile-v2.war) into the versions directory 
with a filename format: 
● Run unit tests using Maven.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // 1. Use the agent from your AWS EC2 cloud
    // This label MUST match the label in your Jenkins Cloud configuration
    agent { label 'ec2-maven-agent' }

    // 2. Configure tools from Global Tool Configuration
    tools {
        maven '3.9.10'
        jdk 'openjdk 21.0.7'
    }

    stages {
        // 3. Fetch the source code
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }

        // 4. Run unit tests
        // This runs 'mvn clean test' to compile and run all tests.
        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn clean test'
            }
        }

        // 5. Build the project
        // This packages the app, skipping tests since they just passed.
        stage('Build (Package)') {
            steps {
                echo 'Building the .war file...'
                sh 'mvn package -DskipTests'
            }
        }

        // 6. Version and copy the artifact
        stage('Version Artifact') {
            steps {
                echo "Creating 'versions' directory..."
                // 7. Create the versions directory
                sh 'mkdir -p versions'

                echo "Copying artifact with version..."
                // 8. Copy the .war file with the build number
                // This creates: vprofile-v2-1.war, vprofile-v2-2.war, etc.
                sh 'cp target/vprofile-v2.war versions/vprofile-v2-${BUILD_NUMBER}.war'
            }
        }
    }

    // 9. Post-build actions
    post {
        // This block runs after all stages, regardless of status
        always {
            echo 'Archiving the versioned .war file...'
            
            // Archive the artifact so it's downloadable from the build page
            archiveArtifacts artifacts: 'versions/*.war', fingerprint: true
            
            // Clean up the workspace for the next build
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check logs.'
        }
    }
}

Here's how to set up and run this pipeline. This pipeline has critical prerequisites to function.

Part 1: Prerequisites (CRITICAL!)

This pipeline will fail unless you have configured Jenkins with the tools and agent it requires.

1. AWS EC2 Plugin Configuration:
This pipeline is designed to run on a dynamic EC2 agent.

You must have the "Amazon EC2" plugin installed in Jenkins.

You must configure it in Manage Jenkins > System > Cloud.

You must have an AMI configured in the plugin with the label ec2-maven-agent.

(If your label is different, e.g., my-aws-agent, you must change agent { label 'ec2-maven-agent' } in the Jenkinsfile to match).

2. Global Tool Configuration:
The pipeline will download tools from your global settings.

Go to Manage Jenkins > Tools.

Under "JDK installations":

Click "Add JDK".

Give it the Name: openjdk 21.0.7

Select "Install from AdoptOpenJDK" or "Install from https://www.google.com/search?q=download.oracle.com" and choose the correct version.

Under "Maven installations":

Click "Add Maven".

Give it the Name: 3.9.10

Select "Install from Apache" and choose version 3.9.10.

(If your tool names are different, you must update the tools block in the Jenkinsfile to match.)

Part 2: Create the Pipeline Job

From the Jenkins dashboard, click "New Item".

Enter a name for your job (e.g., vprofile-maven-pipeline).

Select "Pipeline" and click "OK".

On the configuration page, scroll to the bottom to find the "Pipeline" section.

In the "Definition" dropdown, select "Pipeline script".

In the "Script" text area, copy and paste the entire Jenkinsfile_maven_ec2 script.

Click "Save".

How it Works

When you click "Build Now":

Jenkins will see the agent { label 'ec2-maven-agent' } requirement.

It will ask the EC2 plugin to provision a new agent with that label.

A new EC2 instance will launch, and Jenkins will connect to it.

The pipeline will run all stages on that new instance.

After the build, the instance will be terminated (if you configured an idle timeout).

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************


Set B: 
1. implement a Jenkins Declarative Pipeline that integrates Maven build, unit testing, and 
Slack notifications. 
use GitHub repository: https://github.com/hkhcoder/vprofile-project.git 
branch: main 
Create a Jenkins Pipeline with the following requirements: 
● Use the agent labeled builtinubuntu. 
● Configure tools: 
○ Maven version 3.9.10 
○ JDK version openjdk 21.0.7 
● Stages: 
○ Fetch code: Clone the repository 
https://github.com/hkhcoder/vprofile-project.git (branch: main). 
○ Build the code: 
■ Run mvn clean package -DskipTests. 
■ Create a folder named versions (if it doesn’t exist). 
■ Copy the generated vprofile-v2.war file into the versions directory 
with the format: 
■ Unit test: Execute unit tests with mvn test. 
Configure a Slack notification in the post section that: 
● Sends a message to the channel #jenkinsserver. 
● Uses a color code based on build result (SUCCESS = green, FAILURE = red, 
UNSTABLE = yellow). 
● Sends a message in the format:
Answer=
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // 1. Use the agent with the label 'builtinubuntu'
    agent { label 'builtinubuntu' }

    // 2. Configure tools from Global Tool Configuration
    tools {
        maven '3.9.10'
        jdk 'openjdk 21.0.7'
    }

    stages {
        // 3. Stage 1: Fetch code
        stage('Fetch code') {
            steps {
                git branch: 'main', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }

        // 4. Stage 2: Build the code
        stage('Build the code') {
            steps {
                echo 'Building the application package...'
                // Run mvn clean package, skipping tests (they run in the next stage)
                sh 'mvn clean package -DskipTests'
                
                echo 'Creating versions directory...'
                // Create the 'versions' directory, -p ensures it doesn't fail if it exists
                sh 'mkdir -p versions'
                
                echo 'Copying artifact to versions directory...'
                // Copy the .war file with the build number
                sh 'cp target/vprofile-v2.war versions/vprofile-v2-${BUILD_NUMBER}.war'

                echo 'Archiving the versioned artifact...'
                // Archive the artifact for download from the build page
                archiveArtifacts artifacts: 'versions/*.war', fingerprint: true
            }
        }

        // 5. Stage 3: Unit test
        stage('Unit test') {
            steps {
                echo 'Running unit tests...'
                // Run the tests
                sh 'mvn test'
            }
        }
    }

    // 6. Post-build actions (Slack notification)
    post {
        // The 'always' block runs regardless of the build status
        always {
            script {
                def slackColor
                def buildStatus = currentBuild.currentResult
                
                // 7. Set color code based on build result
                switch(buildStatus) {
                    case 'SUCCESS':
                        slackColor = 'good' // 'good' is green
                        break
                    case 'UNSTABLE':
                        slackColor = 'warning' // 'warning' is yellow
                        break
                    case 'FAILURE':
                        slackColor = 'danger' // 'danger' is red
                        break
                    default:
                        slackColor = '#808080' // A neutral gray
                }

                // 8. The message format from the user request
                def message = "[${env.JOB_NAME}] - Build #${env.BUILD_NUMBER} - ${buildStatus}"

                // 9. Send the notification
                slackSend(
                    channel: '#jenkinsserver',
                    color: slackColor,
                    message: message,
                    // This credential ID must be set up in Jenkins
                    tokenCredentialId: 'slack-token' 
                )
            }
        }
    }
}

Here's how to set up and run this pipeline. This pipeline has critical prerequisites to function.

Part 1: Prerequisites (CRITICAL!)

1. Jenkins Plugins:
You must install the "Slack Notification" plugin.

Go to Manage Jenkins > Plugins.

Click on the "Available plugins" tab.

Search for and install Slack Notification.

2. Slack App and Token:
The plugin needs a token from Slack to send messages.

Go to your Slack workspace at api.slack.com/apps.

Click "Create New App" > "From scratch".

Name it (e.g., "Jenkins CI Bot") and choose your workspace.

Go to "OAuth & Permissions" in the app's sidebar.

In the "Bot Token Scopes" section, click "Add an OAuth Scope" and add:

chat:write (to send messages)

files:write (if you plan to upload files)

chat:write.public (to post in public channels)

Scroll up and click "Install to Workspace".

Copy the "Bot User OAuth Token" (it starts with xoxb-).

In your Slack workspace, go to the #jenkinsserver channel, click the channel name > "Integrations" > "Add an app", and add your new "Jenkins CI Bot".

3. Jenkins Credentials:
Now, securely store the Slack token in Jenkins.

Go to Manage Jenkins > Credentials.

Click on "(global)" under "Stores scoped to Jenkins".

Click "Add Credentials" on the left.

Fill out the form:

Kind: Select Secret text.

Secret: Paste the xoxb-... token you copied from Slack.

ID: slack-token (This must match the tokenCredentialId in the Jenkinsfile).

Description: Slack Bot Token for Jenkins

Click "OK".

4. Global Tool Configuration:
The pipeline will download tools from your global settings.

Go to Manage Jenkins > Tools.

Under "JDK installations":

Click "Add JDK".

Name: openjdk 21.0.7 (must match the Jenkinsfile).

Select "Install from AdoptOpenJDK" or another source and choose the version.

Under "Maven installations":

Click "Add Maven".

Name: 3.9.10 (must match the Jenkinsfile).

Select "Install from Apache" and choose version 3.9.10.

5. Agent Setup:

Ensure you have an agent (or the controller itself) configured with the label builtinubuntu.

Part 2: Create the Pipeline Job

From the Jenkins dashboard, click "New Item".

Enter a name for your job (e.g., vprofile-maven-slack).

Select "Pipeline" and click "OK".

On the configuration page, scroll to the bottom to find the "Pipeline" section.

In the "Definition" dropdown, select "Pipeline script".

In the "Script" text area, copy and paste the entire Jenkinsfile_maven_slack script.

Click "Save".

Now, when you "Build Now", the pipeline will run, and you will see a notification in your #jenkinsserver Slack channel.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

2. Create a Jenkins Declarative Pipeline to automate building and pushing a Docker image to 
Docker Hub. 
use GitHub repository: https://github.com/ajay-raut/spnodeserver.git 
branch: main 
A. Write a Jenkins Pipeline that performs the following tasks: 
● Use the agent labeled builtinubuntu. 
● Define a credentials ID named docker-hub-credentials in Jenkins that stores 
your Docker Hub username and password/token. 
● Fetch code from the repository: 
https://github.com/ajay-raut/spnodeserver.git (branch: main). 
● Build the Docker image: 
○ Use credentials binding to securely inject Docker Hub username and 
password. 
○ Build the image with a tag in the format: 
● Push the image to Docker Hub: 
Authenticate to Docker Hub using the stored credentials. 
Tag the image with both: 
:latest 
:<BUILD_NUMBER> (Jenkins build number) 
● Push both tags to Docker Hub.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // 1. Use the agent labeled 'builtinubuntu'
    agent { label 'builtinubuntu' }

    // Define environment variables
    environment {
        // The ID of the credential we will use
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        // The name of the app on Docker Hub
        IMAGE_NAME = 'my-node-app' 
    }

    stages {
        // 2. Stage 1: Fetch code
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ajay-raut/spnodeserver.git'
            }
        }

        // 3. Stage 2: Build the Docker image
        stage('Build Docker Image') {
            // This environment block securely loads the credential
            // and makes it available as variables:
            // DOCKERHUB_USR (for username) and DOCKERHUB_PSW (for password)
            environment {
                DOCKERHUB = credentials('docker-hub-credentials')
                
                // Define the full tag names using the injected username
                BASE_IMAGE_NAME = "${DOCKERHUB_USR}/${IMAGE_NAME}"
                BUILD_TAG = "${BASE_IMAGE_NAME}:${env.BUILD_NUMBER}"
                LATEST_TAG = "${BASE_IMAGE_NAME}:latest"
            }
            steps {
                echo "Building image: ${BUILD_TAG}"
                // Build the image using the Dockerfile in the repo
                sh "docker build -t ${BUILD_TAG} ."

                // Also tag this build as 'latest'
                echo "Tagging image as: ${LATEST_TAG}"
                sh "docker tag ${BUILD_TAG} ${LATEST_TAG}"
            }
        }

        // 4. Stage 3: Push the image to Docker Hub
        stage('Push Image to Docker Hub') {
            // We load the credentials again for this stage
            environment {
                DOCKERHUB = credentials('docker-hub-credentials')
                
                // Re-define the tags for this stage's scope
                BASE_IMAGE_NAME = "${DOCKERHUB_USR}/${IMAGE_NAME}"
                BUILD_TAG = "${BASE_IMAGE_NAME}:${env.BUILD_NUMBER}"
                LATEST_TAG = "${BASE_IMAGE_NAME}:latest"
            }
            steps {
                // 5. Authenticate to Docker Hub
                echo "Logging into Docker Hub..."
                // DOCKERHUB_PSW is masked in the logs
                sh "echo ${DOCKERHUB_PSW} | docker login -u ${DOCKERHUB_USR} --password-stdin"
                
                // 6. Push the build number tag
                echo "Pushing build tag: ${BUILD_TAG}"
                sh "docker push ${BUILD_TAG}"

                // 7. Push the 'latest' tag
                echo "Pushing latest tag: ${LATEST_TAG}"
                sh "docker push ${LATEST_TAG}"
            }
        }
    }

    // 8. Post-build actions
    post {
        always {
            // Always log out and clean up the workspace
            echo "Logging out from Docker Hub..."
            sh "docker logout"
            cleanWs()
        }
        success {
            echo 'Pipeline finished successfully. Image pushed to Docker Hub.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
Here's how to set up and run this pipeline. This pipeline has critical prerequisites to function.

Part 1: Prerequisites (CRITICAL!)

1. Jenkins Plugins:
You must install the "Slack Notification" plugin.

Go to Manage Jenkins > Plugins.

Click on the "Available plugins" tab.

Search for and install Slack Notification.

2. Slack App and Token:
The plugin needs a token from Slack to send messages.

Go to your Slack workspace at api.slack.com/apps.

Click "Create New App" > "From scratch".

Name it (e.g., "Jenkins CI Bot") and choose your workspace.

Go to "OAuth & Permissions" in the app's sidebar.

In the "Bot Token Scopes" section, click "Add an OAuth Scope" and add:

chat:write (to send messages)

files:write (if you plan to upload files)

chat:write.public (to post in public channels)

Scroll up and click "Install to Workspace".

Copy the "Bot User OAuth Token" (it starts with xoxb-).

In your Slack workspace, go to the #jenkinsserver channel, click the channel name > "Integrations" > "Add an app", and add your new "Jenkins CI Bot".

3. Jenkins Credentials:
Now, securely store the Slack token in Jenkins.

Go to Manage Jenkins > Credentials.

Click on "(global)" under "Stores scoped to Jenkins".

Click "Add Credentials" on the left.

Fill out the form:

Kind: Select Secret text.

Secret: Paste the xoxb-... token you copied from Slack.

ID: slack-token (This must match the tokenCredentialId in the Jenkinsfile).

Description: Slack Bot Token for Jenkins

Click "OK".

4. Global Tool Configuration:
The pipeline will download tools from your global settings.

Go to Manage Jenkins > Tools.

Under "JDK installations":

Click "Add JDK".

Name: openjdk 21.0.7 (must match the Jenkinsfile).

Select "Install from AdoptOpenJDK" or another source and choose the version.

Under "Maven installations":

Click "Add Maven".

Name: 3.9.10 (must match the Jenkinsfile).

Select "Install from Apache" and choose version 3.9.10.

5. Agent Setup:

Ensure you have an agent (or the controller itself) configured with the label builtinubuntu.

Part 2: Create the Pipeline Job

From the Jenkins dashboard, click "New Item".

Enter a name for your job (e.g., vprofile-maven-slack).

Select "Pipeline" and click "OK".

On the configuration page, scroll to the bottom to find the "Pipeline" section.

In the "Definition" dropdown, select "Pipeline script".

In the "Script" text area, copy and paste the entire Jenkinsfile_maven_slack script.

Click "Save".

Now, when you "Build Now", the pipeline will run, and you will see a notification in your #jenkinsserver Slack channel.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

3. implement a Jenkins Declarative Pipeline that builds, tests, and analyzes a Java project with 
code quality checks. 
use GitHub repository: https://github.com/hkhcoder/vprofile-project.git 
branch: atom 
A. Write a Jenkins Pipeline that performs the following steps: 
● Use the agent labeled builtinubuntu. 
● Configure tools: 
○ Maven version 3.9.10 
○ JDK version openjdk 21.0.7
B. Pipeline stages must include: 
● Fetch code: Clone the GitHub repository: 
https://github.com/hkhcoder/vprofile-project.git (branch: atom). 
● Build: 
Run mvn install -DskipTests. 
On success, archive the generated WAR file (target/*.war). 
● Unit Test: Run mvn test to execute tests. 
● Checkstyle Analysis: Run mvn checkstyle:checkstyle and generate a report. 
● SonarQube Analysis: 
○ Use SonarQube scanner tool (sonarscaner720). 
○ Configure withSonarQubeEnv('sonarserver'). 
○ Run code analysis with parameters: 
○ Project key: jenkins 
○ Sources: src/ 
○ Binaries: target/test-classes/com/visualpathit/account/controllerTest/ 
○ Reports: JUnit (target/surefire-reports/), Jacoco (target/jacoco.exec), 
Checkstyle (target/checkstyle-result.xml) 
● Quality Gate: Wait for SonarQube Quality Gate result and abort the pipeline 
if it fails.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // 1. Use the agent labeled 'builtinubuntu'
    agent { label 'builtinubuntu' }

    // 2. Configure tools from Global Tool Configuration
    tools {
        maven '3.9.10'
        jdk 'openjdk 21.0.7'
        // This tool name must match your SonarQube Scanner setup in Global Tools
        tool 'sonarscaner720' 
    }

    stages {
        // 3. Stage 1: Fetch code
        stage('Fetch code') {
            steps {
                git branch: 'atom', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }

        // 4. Stage 2: Build
        stage('Build') {
            steps {
                echo 'Building the application (skipping tests)...'
                // Run 'install' to build the .war and populate local .m2 repo
                // This also generates the 'target/classes' directory
                sh 'mvn install -DskipTests'
            }
            // Post-stage action: Archive the .war only if this stage succeeds
            post {
                success {
                    echo 'Build successful. Archiving .war file...'
                    archiveArtifacts artifacts: 'target/*.war', fingerprint: true
                }
            }
        }

        // 5. Stage 3: Unit Test
        stage('Unit Test') {
            steps {
                echo 'Running unit tests...'
                // The pom.xml in this repo is configured to run JaCoCo
                // This command will generate:
                // 1. JUnit reports in 'target/surefire-reports'
                // 2. JaCoCo coverage data in 'target/jacoco.exec'
                sh 'mvn test'
            }
        }

        // 6. Stage 4: Checkstyle Analysis
        stage('Checkstyle Analysis') {
            steps {
                echo 'Running Checkstyle analysis...'
                // The pom.xml is configured to generate 'target/checkstyle-result.xml'
                // We use catchError so that if Checkstyle finds violations,
                // it marks the build as UNSTABLE but doesn't stop the pipeline.
                // This allows SonarQube to process the report.
                catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                    sh 'mvn checkstyle:checkstyle'
                }
            }
        }

        // 7. Stage 5: SonarQube Analysis
        stage('SonarQube Analysis') {
            steps {
                // This 'sonarserver' name must match the one configured
                // in Manage Jenkins > System > SonarQube servers
                withSonarQubeEnv('sonarserver') {
                    script {
                        // Get the path to the scanner tool defined in the 'tools' block
                        def scannerHome = tool 'sonarscaner720'
                        
                        // Run the sonar-scanner command.
                        // We pass all the report paths as parameters.
                        // NOTE: sonar.java.binaries is corrected to 'target/classes'
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=jenkins \
                            -Dsonar.sources=src/ \
                            -Dsonar.java.binaries=target/classes \
                            -Dsonar.junit.reportsPath=target/surefire-reports \
                            -Dsonar.coverage.jacoco.reportsPath=target/jacoco.exec \
                            -Dsonar.checkstyle.reportsPath=target/checkstyle-result.xml
                        """
                    }
                }
            }
        }

        // 8. Stage 6: Quality Gate
        stage('Quality Gate') {
            steps {
                echo 'Waiting for SonarQube Quality Gate result...'
                // This step polls the SonarQube server for the analysis result.
                // 'abortPipeline: true' will fail the pipeline if the QG is not 'PASSED'.
                // The timeout is set to 5 minutes.
                waitForQualityGate abortPipeline: true, timeout: 5, unit: 'MINUTES'
            }
        }
    }

    // 9. Post-build actions
    post {
        always {
            echo 'Pipeline finished. Cleaning up workspace...'
            // Clean up the workspace to save disk space
            cleanWs()
        }
        success {
            echo 'Pipeline Succeeded and Passed Quality Gate.'
        }
        failure {
             echo 'Pipeline Failed. Check logs or SonarQube Quality Gate.'
        }
    }
}

Here's how to set up and run this pipeline. This pipeline has critical prerequisites and will fail if they are not met.

Part 1: Prerequisites (CRITICAL!)

1. Jenkins Plugins:
You must install the "SonarQube Scanner" plugin.

Go to Manage Jenkins > Plugins.

Click on the "Available plugins" tab.

Search for and install SonarQube Scanner.

2. SonarQube Server Configuration (Admin Task):
This pipeline requires a running SonarQube server (e.g., in a Docker container).

On your SonarQube Server:

Log in as an admin.

Go to Administration > Security > Users.

Click the "Token" icon for your user (or a dedicated 'jenkins' bot user).

Generate a new token. Copy this token now, as you will not see it again.

In Jenkins: Store the SonarQube Token:

Go to Manage Jenkins > Credentials.

Click "(global)" > "Add Credentials".

Kind: Secret text.

Secret: Paste your SonarQube token.

ID: sonarqube-token (or a name you will remember).

In Jenkins: Link to SonarQube Server:

Go to Manage Jenkins > System.

Scroll down to the "SonarQube servers" section.

Click "Add SonarQube".

Name: sonarserver (This must match the withSonarQubeEnv name in the Jenkinsfile).

Server URL: The URL of your SonarQube instance (e.g., http://localhost:9000).

Server authentication token: Select the sonarqube-token credential you just created.

Click "Save".

3. Global Tool Configuration:
The pipeline will download tools from your global settings.

Go to Manage Jenkins > Tools.

Under "JDK installations":

Click "Add JDK".

Name: openjdk 21.0.7 (must match the Jenkinsfile).

Select "Install from OpenJDK" or another installer and choose the version.

Under "Maven installations":

Click "Add Maven".

Name: 3.9.10 (must match the Jenkinsfile).

Select "Install from Apache" and choose version 3.9.10.

Under "SonarQube Scanner installations":

Click "Add SonarQube Scanner".

Name: sonarscaner720 (This must match the tool name in the Jenkinsfile).

Select "Install from SonarQube" or "Install from https://www.google.com/search?q=download.sonarsource.com".

4. Agent Setup:

Ensure you have an agent (or the controller itself) configured with the label builtinubuntu.

Part 2: Create the Pipeline Job

From the Jenkins dashboard, click "New Item".

Enter a name for your job (e.g., vprofile-quality-pipeline).

Select "Pipeline" and click "OK".

On the configuration page, scroll to the bottom to find the "Pipeline" section.

In the "Definition" dropdown, select "Pipeline script".

In the "Script" text area, copy and paste the entire Jenkinsfile_sonar_quality_v2 script.

Click "Save".

Now, when you "Build Now", the pipeline will run all stages. SonarQube will auto-create the jenkins project on its server if it's the first run, and the final "Quality Gate" stage will determine if the build passes or fails based on your SonarQube rules.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
Set C: 
1. Demonstrate real world usecase of Jenkins Shared libraries, also usecase of vars, resource, src directory in real lifescenario.Demonstrate the real-world application of Jenkins Shared 
Libraries, including the use cases for the vars, resources, and src directories in a practical scenario.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
The Scenario: "My DevOps Corp"

The Problem:
You are a DevOps engineer at a company with 50+ microservices. Every team writes their own Jenkinsfile.

The Jenkinsfile for the "User-Service" (Java) is 200 lines long.

The Jenkinsfile for the "Payment-Service" (also Java) is 250 lines long and looks similar, but builds and notifies in a slightly different way.

The "Auth-Service" (Node.js) has its own 150-line Jenkinsfile.

A security vulnerability is found, and you need to add a "Security Scan" stage to all 50+ jobs. This is a nightmare.

The Solution:
You create a single Jenkins Shared Library (a Git repository) to hold all the common logic. You define standard ways to build, test, and deploy.

Your goal is to make the Jenkinsfile in each project's repository incredibly simple, like this:

// The new, simple Jenkinsfile
@Library('my-corp-library') _
standardJavaPipeline()


This single file will contain the code for the library itself, showing a clear example for each directory.

1. The vars Directory: The "Easy Buttons"

What it is: The vars directory is for simple, globally-available functions. This is your most-used directory.

How it works: A file named vars/myFunction.groovy creates a global variable/function called myFunction that any pipeline can call.

Real-World Use: You create simple "wrapper" functions that define entire pipelines or common steps.

vars/standardJavaPipeline.groovy:

// Defines the "standardJavaPipeline" function
def call() {
    pipeline {
        agent { label 'java-builder' }
        tools {
            maven 'maven-3.9'
            jdk 'jdk-17'
        }
        stages {
            stage('Checkout') {
                steps {
                    git '...'
                }
            }
            stage('Build & Test') {
                steps {
                    // Calls ANOTHER shared var!
                    buildAndTest()
                }
            }
            stage('Security Scan') {
                steps {
                    // Calls a complex class from /src
                    script {
                        def scanner = new com.mycorp.security.Scanner(this)
                        scanner.runScan()
                    }
                }
            }
        }
        post {
            always {
                // Calls ANOTHER shared var!
                notifySlack(currentBuild.currentResult)
            }
        }
    }
}


vars/buildAndTest.groovy:

// Defines the "buildAndTest" function
def call() {
    sh 'mvn clean package'
    sh 'mvn test'
    archiveArtifacts 'target/*.war'
}


vars/notifySlack.groovy:

// Defines the "notifySlack" function
def call(String buildStatus) {
    def color = (buildStatus == 'SUCCESS') ? 'good' : 'danger'
    def msg = "Build ${env.JOB_NAME} #${env.BUILD_NUMBER}: ${buildStatus}"
    
    slackSend(channel: '#ci-alerts', color: color, message: msg)
}


2. The src Directory: The "Heavy Lifting"

What it is: Standard Groovy/Java source code. This is where you write proper Classes for complex logic (e.g., state, helpers, API integrations).

How it works: You must use standard package names (like com.mycorp...) and import the classes in your pipeline.

Real-World Use: Your SecurityScanner needs to call an external API, send a file, wait for a result, and parse complex JSON. This logic is too complex for a simple vars script.

src/com/mycorp/security/Scanner.groovy:

// This is a full Groovy Class, not a simple script
package com.mycorp.security

class Scanner implements Serializable {
    // We need the 'script' object to run pipeline steps like 'sh' or 'echo'
    def script

    // Constructor
    Scanner(def script) {
        this.script = script
    }

    // Public method
    def runScan() {
        script.echo "Starting advanced security scan..."
        
        // Use a resource file!
        def config = loadConfig()
        
        script.sh "echo 'Running scan with timeout ${config.timeout}'"
        
        // ...
        // Complex logic to build a request, send to an API,
        // poll for the result, and parse the response.
        // ...
        
        script.echo "Scan complete. 0 vulnerabilities found."
    }

    // Private helper method
    private def loadConfig() {
        // Reads a file from the 'resources' directory
        def configText = script.libraryResource 'com/mycorp/security/scanner-config.json'
        return script.readJSON text: configText
    }
}


3. The resources Directory: The "Data Files"

What it is: A directory for non-Groovy files that your code needs. Think of it as a place to store data, templates, or helper scripts.

How it works: You use the libraryResource step to read a file from this directory into a string.

Real-World Use: The SecurityScanner class needs a JSON configuration file to know its settings. You don't want to hardcode this in the Groovy class.

resources/com/mycorp/security/scanner-config.json:

{
  "api_endpoint": "[https://security.mycorp.api/v2/scan](https://security.mycorp.api/v2/scan)",
  "timeout": 300,
  "fail_on_severity": "high"
}

This file shows the complete file structure of the Git repository named my-corp-library.

vars/standardJavaPipeline.groovy

/**
 * Defines the entire, standardized CI pipeline for a Java Maven project.
 * This is the main "easy button" for developers.
 */
def call() {
    pipeline {
        agent { label 'java-builder' }
        tools {
            maven 'maven-3.9'
            jdk 'jdk-17'
        }
        stages {
            stage('Checkout') {
                steps {
                    // 'scm' is a built-in variable referring to the
                    // project's own Git repository
                    checkout scm
                }
            }
            stage('Build & Test') {
                steps {
                    // Call another function from the 'vars' directory
                    buildAndTest()
                }
            }
            stage('Security Scan') {
                steps {
                    script {
                        // Import and use a complex class from the 'src' directory
                        def scanner = new com.mycorp.security.Scanner(this)
                        scanner.runScan()
                    }
                }
            }
        }
        post {
            always {
                // Call another function from the 'vars' directory
                notifySlack(currentBuild.currentResult)
            }
        }
    }
}


vars/buildAndTest.groovy

/**
 * A simple helper function to run the standard Maven build and test commands.
 */
def call() {
    sh 'mvn clean package'
    sh 'mvn test'
    archiveArtifacts artifacts: 'target/*.war', fingerprint: true
}


vars/notifySlack.groovy

/**
 * A reusable function to send a formatted Slack message.
 * @param buildStatus The result from currentBuild.currentResult (e.g., 'SUCCESS')
 */
def call(String buildStatus) {
    // Set color for Slack message
    def color = 'danger' // Red (fail)
    if (buildStatus == 'SUCCESS') {
        color = 'good' // Green
    } else if (buildStatus == 'UNSTABLE') {
        color = 'warning' // Yellow
    }

    def msg = "Build ${env.JOB_NAME} #${env.BUILD_NUMBER}: ${buildStatus}"
    
    // Send the notification
    slackSend(
        channel: '#ci-alerts', 
        color: color, 
        message: msg,
        tokenCredentialId: 'slack-ci-token' // Assumes a credential ID
    )
}


src/com/mycorp/security/Scanner.groovy

package com.mycorp.security

// 'Serializable' is required for classes used in pipelines
class Scanner implements Serializable {
    
    // We must store the 'script' context to run pipeline steps
    def script

    // Constructor to receive the script context
    Scanner(def script) {
        this.script = script
    }

    /**
     * The main public method to execute the security scan.
     */
    def runScan() {
        script.echo "Starting advanced security scan..."
        
        // Load configuration from a file in the 'resources' directory
        def config = loadConfig()
        
        // Use credentials stored in Jenkins
        script.withCredentials([string(credentialsId: 'scanner-api-key', variable: 'API_KEY')]) {
            
            script.echo "Running scan against: ${config.api_endpoint}"
            
            // Simulate a complex scan by running a shell command
            script.sh """
                echo "Running scan with timeout ${config.timeout}..."
                echo "Failing on severity: ${config.fail_on_severity}"
                echo "Simulating API call..."
                sleep 5
                echo "Scan complete. 0 vulnerabilities found."
            """
        }
    }

    /**
     * A private helper method to load the config file.
     * This is not visible outside the class.
     */
    private def loadConfig() {
        // 'libraryResource' loads a text file from the 'resources' dir
        def configText = script.libraryResource 'com/mycorp/security/scanner-config.json'
        
        // 'readJSON' is a pipeline step to parse JSON
        return script.readJSON text: configText
    }
}


resources/com/mycorp/security/scanner-config.json

{
  "api_endpoint": "[https://security.mycorp.api/v2/scan](https://security.mycorp.api/v2/scan)",
  "timeout": 300,
  "fail_on_severity": "high",
  "ignore_cves": [
    "CVE-2023-1234",
    "CVE-2023-5678"
  ]
}

This file shows how a developer on the "User-Service" team would update their Jenkinsfile to use the new shared library.

Before: The "Old" Messy Jenkinsfile

This is what was in the user-service Git repository before. It's long, hard to read, and brittle.

// Jenkinsfile (OLD)
pipeline {
    agent { label 'java-builder' }
    tools {
        maven 'maven-3.9'
        jdk 'jdk-17'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: '[https://github.com/mycorp/user-service.git](https://github.com/mycorp/user-service.git)'
            }
        }
        stage('Build & Test') {
            steps {
                sh 'mvn clean package'
                sh 'mvn test'
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }
        stage('Security Scan') {
            steps {
                echo "TODO: Add security scan"
                // This will be different from the 'payment-service' scan...
            }
        }
    }
    post {
        always {
            // Slack notification is hardcoded and different from other jobs
            script {
                def color = (currentBuild.currentResult == 'SUCCESS') ? 'good' : 'danger'
                slackSend(
                    channel: '#user-service-alerts', // Wrong channel!
                    color: color, 
                    message: "Build for User-Service: ${currentBuild.currentResult}"
                )
            }
        }
    }
}


After: The "New" Clean Jenkinsfile

The developer can now delete all that code and replace it with just two lines.

// Jenkinsfile (NEW)

// 1. Import the library. The '_' is Groovy syntax.
@Library('my-corp-library') _

// 2. Call the global function from the 'vars' directory
standardJavaPipeline()


How to Use src Classes (Advanced)

What if a developer needs to run just the security scan? They can import the class directly.

@Library('my-corp-library') _

// 1. Explicitly import the class from the 'src' directory
import com.mycorp.security.Scanner

pipeline {
    agent any
    stages {
        stage('Run Custom Scan') {
            steps {
                script {
                    // 2. Create a new instance of the class
                    //    We must pass 'this' to give it the script context.
                    def scanner = new Scanner(this)

                    // 3. Call the public method
                    scanner.runScan()
                }
            }
        }
    }
}

This is the one-time configuration a Jenkins Administrator must perform to make the shared library available to all pipelines.

How to Configure the Shared Library in Jenkins

Go to Manage Jenkins:

From the Jenkins dashboard, go to Manage Jenkins > System.

Find "Global Pipeline Libraries":

Scroll down until you find the "Global Pipeline Libraries" section.

Add the Library:

Click "Add".

Name: my-corp-library

(This is the name developers will use in @Library('my-corp-library'))

Default version: main (or your default Git branch)

Retrieval method: Select "Modern SCM".

Source Code Management: Select "Git".

Project Repository: Enter the Git URL for your shared library (e.g., https://github.com/my-devops-corp/jenkins-shared-library.git).

Credentials: Add credentials if the repository is private.

Save:

Click "Save" at the bottom of the page.

That's it! Jenkins will now automatically pull from this repository, and all pipelines can reference the library by its name (my-corp-library).
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
 
2. Practical demonstration of utilizing a Docker agent within a pipeline, involving the use of multiple containers.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    // 1. Outer Agent:
    // This pipeline runs on an agent that MUST have the Docker CLI installed.
    // We'll assume the 'builtinubuntu' agent has Docker, as per your
    // previous examples.
    agent { label 'builtinubuntu' }

    stages {
        // 2. This stage will run its steps inside its OWN agent
        stage('Run Test with DB Sidecar') {
            
            // 3. Stage-level Agent:
            // This 'agent' directive tells Jenkins to do the following:
            //  - Create a temporary Docker network.
            //  - Start the 'postgres:14' container in the background.
            //  - Start the 'node:18' container and run the 'steps' inside it.
            agent {
                docker {
                    // Container 1: The 'main' container where 'steps' run
                    image 'node:18'
                    
                    // Container 2: The 'sidecar' container
                    // 'args' are the flags you would pass to 'docker run'
                    // -d = detached (run in background)
                    // --name postgres-db = GIVES IT A HOSTNAME
                    // -e ... = Sets environment variables to init the DB
                    args '-d --name postgres-db -e POSTGRES_PASSWORD=mysecretpassword postgres:14-alpine'
                }
            }
            
            // 4. These steps run INSIDE the 'node:18' container
            steps {
                echo "We are now running inside the 'node:18' container."
                echo "The 'postgres-db' sidecar container is starting up in the background."
                
                // Postgres takes a few seconds to initialize
                echo "Waiting 15 seconds for PostgreSQL to start..."
                sh "sleep 15"
                
                echo "To prove we can talk to the DB, we will install 'netcat'..."
                // The 'node:18' image is based on Debian, so it has 'apt'
                sh "apt-get update && apt-get install -y netcat-openbsd"
                
                echo "Testing network connection from this Node container to 'postgres-db' on port 5432..."
                
                // This command tests the network connection.
                // It works because 'postgres-db' is a valid hostname on the
                // shared Docker network that Jenkins created.
                sh "nc -z postgres-db 5432"
                
                echo "Connection Test Succeeded!"
                echo "The Node container can successfully talk to the Postgres container."
            }
        } // End of stage
    } // End of stages
    
    // 5. Post-build Cleanup
    // This 'post' block runs on the OUTER agent ('builtinubuntu'),
    // NOT inside the 'node' container.
    post {
        always {
            // This is CRITICAL. Jenkins starts the sidecar ('postgres-db')
            // but does not stop it. We MUST clean it up ourselves.
            echo "Cleaning up the 'postgres-db' sidecar container..."
            
            // These 'sh' commands run on the 'builtinubuntu' agent's shell
            // '|| true' ensures the build doesn't fail if the container
            // is already stopped for some reason.
            sh "docker stop postgres-db || true"
            sh "docker rm postgres-db || true"
        }
    }
}

This pipeline demonstrates a powerful integration testing pattern, but it has one critical prerequisite.

Prerequisite: Docker on Your Agent

This pipeline must run on a Jenkins agent that:

Has Docker installed.

Has the jenkins user added to the docker group.

If your builtinubuntu agent doesn't have this, you will see a "permission denied" error.

To fix this (on your builtinubuntu agent):

# 1. Install Docker (if not already present)
sudo apt-get update
sudo apt-get install -y docker.io

# 2. Add 'jenkins' user to the 'docker' group
sudo usermod -aG docker jenkins

# 3. RESTART Jenkins for the new group membership to apply
sudo systemctl restart jenkins


How to Run

Create a new "Pipeline" job in Jenkins.

In the "Pipeline" section, select "Pipeline script" from the "Definition" dropdown.

Copy and paste the entire Jenkinsfile_docker_multi script into the text area.

Click "Save" and then "Build Now".

What This Demonstrates

This pipeline shows you how to use agent { docker { ... } } at the stage level to:

Define a main container (node:18) to run your build steps.

Use the args property to launch a second "sidecar" container (postgres:14) on the same network.

How the main container can access the sidecar container using its name (postgres-db) as a hostname.

The critical post { always { ... } } block to clean up the sidecar container, which runs on the outer agent.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Ass-3

Set A: 
1. Launch two new virtual machine instances. The first should run an operating system from 
the Debian family, and the second should run one from the Red Hat family. 
2. Create a project folder containing an ansible.cfg file and an inventory file. The inventory 
file should contain the details for two managed nodes. Then, establish and test the 
connection between the Ansible control node and the two managed nodes. 
3. Run following ad-hoc command: 
I. Install apache2 on a Debian system using an Ansible ad-hoc command. 
II. Install httpd on RedHat system using an Ansible ad-hoc command.
III.Demonstrate at least one use case of ansible.builtin.copy module using adhoc 
command. 
IV.Demonstrate at least one use case of ansible.builtin.file module using adhoc 
command. 
V.Demonstrate at least one use case of copy module using adhoc command. 
VI.Demonstrate at least one use case of ansible.builtin.service module using adhoc 
command. 
VII.Demonstrate at least one use case of ansible.builtin.apt module using adhoc 
command. 
VIII.Demonstrate at least one use case of ansible.builtin.yum module using adhoc 
command. 
IX.Demonstrate at least one use case of ansible.builtin.user module using adhoc 
command. 
X.Demonstrate at least one use case of ansible.builtin.setup module using adhoc 
command.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Launch Your AWS EC2 Instances
First, you need to create your two virtual machines (managed nodes) and identify your control node.

Control Node: This will be your own computer (e.g., your new Asus TUF laptop) where you will install Ansible and run the commands.

Managed Nodes: These are the two EC2 instances you will launch.

Steps to Launch Instances:

Log in to the AWS Management Console and navigate to the EC2 service.

Create a Key Pair:

Go to Network & Security > Key Pairs.

Click Create key pair.

Name it ansible-key and choose the .pem format.

Your browser will download ansible-key.pem. Keep this file safe.

Create a Security Group:

Go to Network & Security > Security Groups.

Click Create security group.

Name it ansible-sg.

Add an Inbound rule:

Type: SSH

Port: 22

Source: My IP (This is more secure)

Click Create security group.

Launch Instance 1 (Debian Family):

Go to Instances and click Launch instances.

Name: debian-node

Application and OS Images (AMI): Select Ubuntu Server (e.g., "Ubuntu Server 22.04 LTS").

Instance type: Select t2.micro (This is eligible for the AWS Free Tier).

Key pair: Select the ansible-key you created.

Network settings: Click Edit.

Security group: Select Select existing security group and choose ansible-sg.

Click Launch instance.

Launch Instance 2 (Red Hat Family):

Click Launch instances again.

Name: redhat-node

Application and OS Images (AMI): Select Amazon Linux (e.g., "Amazon Linux 2023 AMI"). This is RHEL-family.

Instance type: Select t2.micro.

Key pair: Select the same ansible-key.

Network settings: Select the same ansible-sg security group.

Click Launch instance.

Get Your IP Addresses:

Go back to your Instances list. Wait for both instances to show "Running" and "2/2 checks passed."

Note down the Public IPv4 address for both debian-node and redhat-node.

2. Set Up Your Ansible Project
On your local machine (the control node), you'll set up your project folder.

Install Ansible (if not already):

Bash

pip install ansible
Create Your Project Folder:

Bash

mkdir ansible-aws-project
cd ansible-aws-project
Move Your Key Pair:

Move the ansible-key.pem file you downloaded into this new ansible-aws-project folder.

Crucial: Set the correct permissions for the key file.

Bash

chmod 400 ansible-key.pem
Create ansible.cfg file:

Create a file named ansible.cfg in your project folder.

This file tells Ansible what defaults to use for this project.

Ini, TOML

[defaults]
inventory = inventory
private_key_file = ansible-key.pem
host_key_checking = False
Create inventory file:

Create a file named inventory.

This file lists your managed nodes and groups them. Replace the placeholders with the Public IPs you noted earlier.

Note: Ubuntu AMIs use the user ubuntu. Amazon Linux AMIs use the user ec2-user. We must specify this.

Ini, TOML

[debian_nodes]
<your-ubuntu-public-ip> ansible_user=ubuntu

[redhat_nodes]
<your-amazon-linux-public-ip> ansible_user=ec2-user

[all_servers:children]
debian_nodes
redhat_nodes
Test the Connection:

Run the Ansible ping module to confirm your control node can connect to both managed nodes.

Bash

ansible all -m ping
Expected Output: You should see two "SUCCESS" messages in green, each returning a "pong".

JSON

<your-ubuntu-public-ip> | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
<your-amazon-linux-public-ip> | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
3. Run Ad-hoc Commands
Now you can run your list of commands. The --become flag is used to run the command with sudo (root) privileges, which is required for installing software or managing services.

I. Install apache2 on Debian system:

Bash

ansible debian_nodes -m apt -a "name=apache2 state=present" --become
II. Install httpd on RedHat system:

Bash

ansible redhat_nodes -m yum -a "name=httpd state=present" --become
III. ansible.builtin.copy (copy a file from local to remote):

First, create a simple index.html file on your local machine:

Bash

echo "Welcome to my Ansible-managed website!" > index.html
Now, copy it to the default web directory on all servers:

Bash

ansible all_servers -m ansible.builtin.copy -a "src=index.html dest=/var/www/html/index.html" --become
IV. ansible.builtin.file (create a new directory):

This command creates a new directory /opt/app-data on all servers.

Bash

ansible all_servers -m ansible.builtin.file -a "path=/opt/app-data state=directory mode=0755" --become
V. copy module (different use case: set file content directly):

This demonstrates using the content attribute instead of src to create a file with specific text.

Bash

ansible all_servers -m copy -a "content='This is a config file\nVERSION=1.0' dest=/etc/myapp.conf" --become
VI. ansible.builtin.service (start and enable a service):

This command will start the httpd service on your Red Hat node and ensure it starts on boot.

Bash

ansible redhat_nodes -m ansible.builtin.service -a "name=httpd state=started enabled=yes" --become
VII. ansible.builtin.apt (update package cache):

This is the equivalent of running sudo apt-get update on your Debian node.

Bash

ansible debian_nodes -m ansible.builtin.apt -a "update_cache=yes" --become
VIII. ansible.builtin.yum (install a different package):

This command installs the git tool on your Red Hat node.

Bash

ansible redhat_nodes -m ansible.builtin.yum -a "name=git state=present" --become
IX. ansible.builtin.user (create a new user):

This command creates a new user named jdoe on all your servers.

Bash

ansible all_servers -m ansible.builtin.user -a "name=jdoe comment='John Doe' state=present" --become
X. ansible.builtin.setup (gather system facts):

This module gathers a huge amount of data about the host systems. Using a filter makes it much more readable.

This command will show you just the OS distribution for your Debian node:

Bash

ansible debian_nodes -m ansible.builtin.setup -a "filter=ansible_distribution*"
Output:

JSON

<your-ubuntu-public-ip> | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "Ubuntu",
        "ansible_distribution_major_version": "22",
        "ansible_distribution_release": "jammy",
        "ansible_distribution_version": "22.04"
    },
    "changed": false
}
You have now successfully completed the entire assignment!

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
Set B:
1. Launch two new virtual machine instances. The first & second should run an operating 
system from the Debian family, and the third should run one from the Red Hat family. 
2. Create a project folder containing an ansible.cfg, inventory file and playbooks folder. The 
inventory file should contain the details for three managed nodes. Then, establish and test 
the connection between the Ansible control node and the two managed nodes. 
3. Create an Ansible playbook to update the apt package manager, install Java, and display the 
installed Java version on all managed nodes. 
4. Create an Ansible playbook to install the Nginx web server and start the Nginx service on 
all managed nodes. 
5. Create an Ansible playbook to add multiple users on all managed nodes, ensuring that 
each user has a home directory.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. 🚀 Launch Your AWS EC2 Instances
For this set, you need three instances. We must also update your security group to allow web traffic for the Nginx playbook.

Use Your Existing Key Pair: You can re-use the ansible-key.pem file from Set A.

Update Your Security Group (ansible-sg):

Go to the EC2 > Security Groups section in your AWS Console.

Select the ansible-sg you created.

Click the Inbound rules tab, then Edit inbound rules.

Click Add rule:

Type: HTTP

Port: 80

Source: Anywhere (or 0.0.0.0/0)

Click Save rules. This is necessary to test your Nginx web server in Task 4.

Launch Instance 1 (Debian):

Name: debian-node-1

Application and OS Images (AMI): Ubuntu Server 22.04 LTS

Instance type: t2.micro (Free Tier eligible)

Key pair: ansible-key

Security group: Select your existing ansible-sg

Click Launch instance.

Launch Instance 2 (Debian):

Name: debian-node-2

AMI: Ubuntu Server 22.04 LTS

Instance type: t2.micro

Key pair: ansible-key

Security group: ansible-sg

Click Launch instance.

Launch Instance 3 (Red Hat):

Name: redhat-node-1

AMI: Amazon Linux 2023 (This is RHEL-family)

Instance type: t2.micro

Key pair: ansible-key

Security group: ansible-sg

Click Launch instance.

Get Your IP Addresses:

Go to your Instances list.

Note down the Public IPv4 address for all three new instances.

2. 📁 Set Up Your Ansible Project
On your WSL terminal, create a new project folder as specified.

Create Your Project Structure:

Bash

# Create a new folder for this set
mkdir ansible-setB
cd ansible-setB

# Create the 'playbooks' folder as requested
mkdir playbooks

# Create your config and inventory files
touch ansible.cfg
touch inventory
Copy and Secure Your Key:

Copy your ansible-key.pem file (from your Set A project or your downloads) into this new ansible-setB folder.

Set the correct permissions (since you are on WSL, chmod is the correct command):

Bash

chmod 400 ansible-key.pem
Edit ansible.cfg:

Open the file (nano ansible.cfg) and add the following content. This tells Ansible where to find your inventory and key.

Ini, TOML

[defaults]
inventory = inventory
private_key_file = ansible-key.pem
host_key_checking = False
Edit inventory:

Open the file (nano inventory) and add your 3 new servers. Replace the IPs with your new public IPs. This inventory will define your groups of managed nodes.


Ini, TOML

[debian_nodes]
<debian-node-1-public-ip> ansible_user=ubuntu
<debian-node-2-public-ip> ansible_user=ubuntu

[redhat_nodes]
<redhat-node-1-public-ip> ansible_user=ec2-user

[all_servers:children]
debian_nodes
redhat_nodes
Test the Connection:

Run the ping module to confirm you can connect to all 3 nodes.


Bash

ansible all -m ping
Expected Output: You should see three "SUCCESS" messages in green, one for each of your new IPs.

3. ☕ Playbook: Install Java
This playbook will update your package manager, install Java, and then display the version. It uses when conditions to run different tasks on different operating systems.

Create the playbook file:

Bash

nano playbooks/java_install.yml
Add this content:

YAML

---
- name: Playbook to Install Java
  hosts: all_servers
  become: yes  # This means "run as root" (using sudo)

  tasks:
    - name: Update apt cache on Debian nodes
      ansible.builtin.apt:
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Install OpenJDK 11 on Debian nodes
      ansible.builtin.apt:
        name: openjdk-11-jdk
        state: present
      when: ansible_os_family == "Debian"

    - name: Install Java 11 (Corretto) on Red Hat nodes
      ansible.builtin.yum:
        name: java-11-amazon-corretto
        state: present
      when: ansible_os_family == "RedHat"

    - name: Check Java version
      ansible.builtin.command: java -version
      register: java_version_output
      changed_when: false # This command doesn't change anything

    - name: Display Java version
      ansible.builtin.debug:
        # Java prints its version to stderr, not stdout
        var: java_version_output.stderr_lines
Run the playbook:

Bash

ansible-playbook playbooks/java_install.yml
You will see it "skip" the apt tasks for the Red Hat node and "skip" the yum task for the Debian nodes. This is correct!

4. 🌐 Playbook: Install Nginx
This playbook will install and start the Nginx web server. We will use the generic package module, which is smart enough to use apt or yum automatically.

Create the playbook file:

Bash

nano playbooks/nginx_install.yml
Add this content:

YAML

---
- name: Playbook to Install and Start Nginx
  hosts: all_servers
  become: yes

  tasks:
    - name: Install Nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: Start and enable the Nginx service
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: yes
Run the playbook:

Bash

ansible-playbook playbooks/nginx_install.yml
Verify the result:

Open your web browser.

Paste the Public IPv4 address of any of your three EC2 instances into the address bar.

You should see the "Welcome to Nginx!" page. (This works because you opened port 80).

5. 👥 Playbook: Add Multiple Users
This playbook will add multiple users and ensure their home directories are created, as requested. This demonstrates how to use a loop.

Create the playbook file:

Bash

nano playbooks/add_users.yml
Add this content:

YAML

---
- name: Playbook to Add Multiple Users
  hosts: all_servers
  become: yes

  tasks:
    - name: Add several new users
      ansible.builtin.user:
        name: "{{ item }}"  # 'item' is the variable for the loop
        state: present
        create_home: yes   # Ensures /home/alice, etc. is made
      loop:
        - alice
        - bob
        - charlie
Run the playbook:

Bash

ansible-playbook playbooks/add_users.yml
Verify the result (with an ad-hoc command):

Pick one of the users and check if they exist on all servers.

Bash

ansible all -m shell -a "id alice"
You should see a successful output from all three servers showing the user alice's uid and gid.

You have now successfully completed all the tasks for Assignment 3, Set B!

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set C: 
1.Create an Ansible playbook to: 
I.Install the Nginx web server. 
II.Download sample project files (use: https://www.tooplate.com/) .
III.Transfer all files to the web server's document root. 
IV.Ensure that the project is accessible via the managed node's public IP address in a 
web browser.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
We will continue using the same 3-node AWS setup and the ansible-setB project folder from the previous assignment. The security group (ansible-sg) should already allow HTTP traffic on port 80 from Set B.

Deploying a Static Website (Set C, Task 1)
This playbook will install Nginx, download a sample website zip file from https://www.tooplate.com/, unzip it, and move the contents to the correct web server directory for both Debian and Red Hat systems.

Based on my search, I've found a good sample template called "Little Fashion".

Download URL: https://www.tooplate.com/zip-templates/2127-little-fashion.zip

Folder inside zip: 2127-little-fashion

1. Create the Playbook
In your ansible-setB project folder, create a new file named deploy_website.yml inside your playbooks directory.

Bash

nano playbooks/deploy_website.yml
Now, add the following content to this file.

YAML

---
- name: Deploy Static Website to Nginx
  hosts: all_servers
  become: yes

  vars:
    # Define the download URL and the folder name inside the zip
    download_url: "https://www.tooplate.com/zip-templates/2127-little-fashion.zip"
    unzip_folder: "2127-little-fashion"
    unzip_dest: "/tmp"

  tasks:
    - name: Set web root directory for Debian
      ansible.builtin.set_fact:
        web_root: /var/www/html
      when: ansible_os_family == "Debian"

    - name: Set web root directory for Red Hat
      ansible.builtin.set_fact:
        web_root: /usr/share/nginx/html
      when: ansible_os_family == "RedHat"

    - name: I. Install Nginx
      ansible.builtin.package:
        name: nginx
        state: present

    - name: II. Download and Unarchive Website
      ansible.builtin.unarchive:
        src: "{{ download_url }}"
        dest: "{{ unzip_dest }}"
        remote_src: yes
        # Idempotency: Don't re-download if the folder already exists
        creates: "{{ unzip_dest }}/{{ unzip_folder }}/"

    - name: III. Transfer files to Nginx root
      ansible.builtin.shell:
        # The /* copies the *contents* of the folder, not the folder itself
        cmd: "cp -R {{ unzip_dest }}/{{ unzip_folder }}/* {{ web_root }}/"
        # Idempotency: Don't copy if the index.html is already there
        creates: "{{ web_root }}/index.html"

    - name: Ensure Nginx is started and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: yes
2. How This Playbook Works
set_fact: We first set a variable named web_root based on the host's OS (ansible_os_family). This is the key to making the playbook work for both Debian and Red Hat.

ansible.builtin.package: This installs Nginx. If it's already installed from Set B, this task will just report "ok" and change nothing.

ansible.builtin.unarchive: This powerful module does two things:

Downloads the file because we set remote_src: yes.

Unzips it to the dest directory (/tmp).

The creates parameter makes this task idempotent. If it sees the {{ unzip_dest }}/{{ unzip_folder }}/ directory already exists, it will skip this task.

ansible.builtin.shell: Since we are copying files from one remote directory (/tmp/2127-little-fashion) to another ({{ web_root }}), the shell module is the most direct tool.

The /* at the end of the source path is critical. It tells cp to copy the contents of the folder, not the folder itself.

The creates parameter checks if index.html already exists in the web root. If it does, this task is skipped, making it idempotent.

3. Run the Playbook
Save the file and run it from your WSL terminal:

Bash

ansible-playbook playbooks/deploy_website.yml
4. Verify the Result
To verify that the task was successful:

Go to your AWS EC2 Console and get the Public IPv4 address of any of your three instances.

Paste this IP address into your web browser.

You should now see the "Little Fashion" website live on the internet.

This completes Set C, Task 1.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
2. Design Ansible playbook Deploy and configure Time service idempotently on all managed 
nodes. (Note: use concept of conditionals and handlers) 
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
1. Create the Template Files
First, we need to create the configuration file templates. You must create a templates directory inside your ansible-setB project folder if it doesn't exist.

Bash

# Still inside your ansible-setB project folder
mkdir -p templates
A. Create the template for systemd-timesyncd (for Debian/Ubuntu):

Bash

nano templates/timesyncd.conf.j2
Paste this content into the file. This tells timesyncd which servers to use.

Ini, TOML

[Time]
NTP=0.pool.ntp.org 1.pool.ntp.org 2.pool.ntp.org
FallbackNTP=ntp.ubuntu.com
B. Create the template for chrony (for Red Hat/Amazon Linux):

Bash

nano templates/chrony.conf.j2
Paste this content into the file. This is a standard chrony config file.

Ini, TOML

# Use public servers from the pool.
pool 0.pool.ntp.org iburst
pool 1.pool.ntp.org iburst
pool 2.pool.ntp.org iburst

# Record the rate at which the clock drifts.
driftfile /var/lib/chrony/drift

# Allow the system clock to be stepped in the first three updates
# if its offset is larger than 1 second.
makestep 1.0 3

# Enable kernel synchronization of the real-time clock (RTC).
rtcsync

# Specify directory for log files.
logdir /var/log/chrony
2. Create the Ansible Playbook
Now, create the new playbook file named deploy_time_service.yml in your playbooks folder.

Bash

nano playbooks/deploy_time_service.yml
Paste the entire playbook content below into this file.

YAML

---
- name: Configure Time Service (NTP)
  hosts: all_servers
  become: yes

  tasks:
    # --- Debian (Ubuntu) Tasks ---
    - name: Configure systemd-timesyncd on Debian
      ansible.builtin.template:
        src: templates/timesyncd.conf.j2
        dest: /etc/systemd/timesyncd.conf
        owner: root
        group: root
        mode: '0644'
      when: ansible_os_family == "Debian"
      notify: Restart timesyncd # This triggers the handler

    - name: Ensure systemd-timesyncd is started and enabled on Debian
      ansible.builtin.service:
        name: systemd-timesyncd
        state: started
        enabled: yes
      when: ansible_os_family == "Debian"

    # --- Red Hat (Amazon Linux) Tasks ---
    - name: Install chrony on Red Hat
      ansible.builtin.package:
        name: chrony
        state: present
      when: ansible_os_family == "RedHat"

    - name: Configure chrony on Red Hat
      ansible.builtin.template:
        src: templates/chrony.conf.j2
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: '0644'
      when: ansible_os_family == "RedHat"
      notify: Restart chronyd # This triggers the handler

    - name: Ensure chronyd is started and enabled on Red Hat
      ansible.builtin.service:
        # Note the 'd' at the end for the service name
        name: chronyd
        state: started
        enabled: yes
      when: ansible_os_family == "RedHat"

  # --- Handlers ---
  # These tasks only run if "notified" by a change in a previous task.
  handlers:
    - name: Restart timesyncd
      ansible.builtin.service:
        name: systemd-timesyncd
        state: restarted
      listen: Restart timesyncd # This name matches the 'notify'

    - name: Restart chronyd
      ansible.builtin.service:
        name: chronyd
        state: restarted
      listen: Restart chronyd # This name matches the 'notify'
3. How It Works (Conditionals & Handlers)
Conditionals (when):

The playbook is split into two sections of tasks.

The first set of tasks only runs when: ansible_os_family == "Debian".

The second set only runs when: ansible_os_family == "RedHat".

This allows you to manage two different services and configuration files all within one playbook.

Handlers (notify & listen):

The ansible.builtin.template tasks  both have a notify statement.


Ansible's template module is idempotent. It checks if the file on the server already matches the template.

If there is no change: The task reports "ok" (green), and the notify is not sent.

If the file is changed: The task reports "changed" (yellow), and the notify message is sent to the handlers block.

The handler task with the matching listen statement (or matching name) will then run at the very end of the play. This ensures the service is only restarted when a configuration change actually happens.

4. Run the Playbook
Execute your new playbook from the ansible-setB directory:

Bash

ansible-playbook playbooks/deploy_time_service.yml
Expected Output: You will see the tasks for Red Hat "skipping" on your Debian nodes, and the Debian tasks "skipping" on your Red Hat node. You will also see the template tasks report "changed" (yellow) on the first run, which will trigger the "RUNNING HANDLER" at the end.

If you run the playbook a second time, all tasks will report "ok" (green), and the handlers will not run, proving the playbook is idempotent.

5. Verify the Result
You can check the status of the time service on all nodes with an ad-hoc command.

On your Debian nodes, timedatectl status is a good command.

On your Red Hat nodes, chronyc sources is the command.

Let's run chronyc sources on the Red Hat node specifically:

Bash

ansible redhat_nodes -m shell -a "chronyc sources"
You should see an output listing the pool.ntp.org servers you defined.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Ass-4

Assignment 4: Ansible Variable Management, Templating, and Roles
Set A: 
1. Launch three new virtual machine instances. All should run an operating system from the 
Debian family.
2. Create a project folder containing an ansible.cfg file and an inventory file. The inventory 
file should contain the details for two managed nodes. Then, establish and test the 
connection between the Ansible control node and the two managed nodes. 
3. Utilize variables to demonstrate user creation and add comments. Additionally, use the 
debug module to output the name of the created user and their associated comment. 
4. Generate host_vars and define the variables username and comment for a selected host. 
5. Create group_vars and define the variables username and comment for the 'all' group and at 
least one other group specified in the inventory.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
1. Launch Your AWS EC2 Instances (Task 1)
For this assignment, you need three identical Debian-family instances.

Log in to the AWS EC2 Console.

Launch 3 Instances:

Name: debian-node-1 (and debian-node-2, debian-node-3 for the others)

Application and OS Images (AMI): Ubuntu Server 22.04 LTS (This is in the Debian family)

Instance type: t2.micro (Free Tier)

Key pair: Select your existing ansible-key.

Network settings: Select your existing ansible-sg security group. (Port 22 for SSH is all that's needed for this assignment).

Get Your IPs: Once all three instances are running, note down their Public IPv4 addresses.

2. Create Your Project (Task 2)
This task asks for an inventory with two nodes. We'll start with that and then expand it in a later step to use all three instances.

On your WSL terminal, create a new project folder:

Bash

mkdir ansible-setA4
cd ansible-setA4

# Create a directory for playbooks
mkdir playbooks
Copy and Secure Your Key:

Copy your ansible-key.pem file into the ansible-setA4 folder.

Set the correct permissions:

Bash

chmod 400 ansible-key.pem
Create ansible.cfg:

nano ansible.cfg

Add the following content:

Ini, TOML

[defaults]
inventory = inventory
private_key_file = ansible-key.pem
host_key_checking = False
Create inventory:

nano inventory

Add your first two instances. Using names like this will be very important for Task 4.

Ini, TOML

# We use hostnames (debian-node-1) and link them to their IP
# This makes host_vars easier to manage
debian-node-1 ansible_host=<your-debian-1-public-ip>
debian-node-2 ansible_host=<your-debian-2-public-ip>

[all:vars]
ansible_user=ubuntu
Test Connection:

Run a ping. It should only contact the two nodes you've added.

Bash

ansible all -m ping
You should see two "SUCCESS" messages.

3. Playbook with vars: Section (Task 3)
This task demonstrates defining variables directly inside a playbook.


Create the playbook file:

Bash

nano playbooks/create_user_vars.yml
Add this content:

YAML

---
- name: Create user from playbook variables
  hosts: all  # This will run on debian-node-1 & debian-node-2
  become: yes

  # The 'vars' section defines variables for this play
  vars:
    username: "playbook_user"
    comment: "User from playbook vars section"

  tasks:
    - name: Create the user
      ansible.builtin.user:
        name: "{{ username }}"
        comment: "{{ comment }}"
        state: present

    - name: Debug the variables
      ansible.builtin.debug:
        msg: "Created user {{ username }} with comment: {{ comment }}"
Run the playbook:

Bash

ansible-playbook playbooks/create_user_vars.yml
Result: This will run on debian-node-1 and debian-node-2. Both will create a user named playbook_user and print the debug message.

4 & 5. host_vars and group_vars (Tasks 4 & 5)
This is the most important part of the assignment. We will now modify the project to use all 3 servers and demonstrate Ansible's variable precedence.





A. Update Your Project Structure Create the group_vars and host_vars directories. Ansible loads variables from these directories automatically.

Bash

# Still in your ansible-setA4 directory
mkdir group_vars
mkdir host_vars
B. Update Your inventory File Let's add our third node and organize all nodes into groups.

nano inventory

Replace the old content with this. Make sure to use your 3 public IPs.

Ini, TOML

[debian_nodes]
debian-node-1 ansible_host=<your-debian-1-public-ip>
debian-node-2 ansible_host=<your-debian-2-public-ip>

[special_nodes]
debian-node-3 ansible_host=<your-debian-3-public-ip>

[all:vars]
ansible_user=ubuntu
C. Create Variable Files (Task 5)


group_vars/all.yml (Lowest Precedence): These variables apply to every host.



nano group_vars/all.yml

Add this content:

YAML

username: "all_user"
comment: "From group_vars/all"

group_vars/debian_nodes.yml (Medium Precedence): These variables apply only to hosts in the [debian_nodes] group and will override all.yml.


nano group_vars/debian_nodes.yml

Add this content:

YAML

username: "debian_user"
comment: "From group_vars/debian_nodes"
D. Create Host Variable File (Task 4)


host_vars/debian-node-1.yml (Highest Precedence): These variables apply only to debian-node-1 and will override all group variables. The filename must match the host's name in the inventory.


nano host_vars/debian-node-1.yml

Add this content:

YAML

username: "host_user"
comment: "From host_vars/debian-node-1"
E. Create the Test Playbook Now we'll create a simple playbook that just prints the variables so we can see which one Ansible chooses for each host.

Create the file:

Bash

nano playbooks/test_precedence.yml
Add this content:

YAML

---
- name: Test variable precedence
  hosts: all # This will run on all 3 nodes
  gather_facts: no

  tasks:
    - name: Debug the username and comment
      ansible.builtin.debug:
        msg: "Host {{ inventory_hostname }} will use USER: {{ username }} and COMMENT: {{ comment }}"
F. Run and Analyze the Result Run the playbook:

Bash

ansible-playbook playbooks/test_precedence.yml
You will see three different outputs, perfectly demonstrating Ansible's precedence:


Expected Output:

debian-node-1 | SUCCESS => { "msg": "Host debian-node-1 will use USER: host_user and COMMENT: From host_vars/debian-node-1" }


Why: host_vars always wins.


debian-node-2 | SUCCESS => { "msg": "Host debian-node-2 will use USER: debian_user and COMMENT: From group_vars/debian_nodes" }


Why: It's in the debian_nodes group, so group_vars/debian_nodes.yml overrides group_vars/all.yml.

debian-node-3 | SUCCESS => { "msg": "Host debian-node-3 will use USER: all_user and COMMENT: From group_vars/all" }


Why: It's not in the debian_nodes group and has no host_vars file, so it falls back to the default group_vars/all.yml.

This completes Set A. You have successfully demonstrated how variables are defined and how precedence works.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set B: 
1. Launch two new virtual machine instances. All should run an operating system from the 
Debian family. 
2. Create a project folder containing an ansible.cfg file and an inventory file. The inventory 
file should contain the details for two managed nodes. Then, establish and test the 
connection between the Ansible control node and the two managed nodes. 
3. Generate a templates directory containing the necessary files: 
● Create index.html.j2 file and dipay ansible_hostname and 
ansible_default_ipv4.address use jinja2 formatting to accomplish the task. 
● Create and edit the nginx.conf.j2 file using Jinja2 templating to define the necessary 
NGINX configuration. 
4. Write playbook to install Nginx server on all hosts present in inventory file. 
5. Create a separate Ansible playbook. This playbook should copy the nginx.conf.j2 file to 
/etc/nginx/sites-available/[example.com].conf on all target servers. 
6. Develop an independent Ansible playbook. This playbook's purpose is to replace the content 
within index.nginx-debian.html with the index.html.j2 file, and this change should be 
applied to all designated target servers. 
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. & 2. Launch Instances and Set Up Project
First, let's get your AWS instances and your local Ansible project folder set up.

Launch AWS Instances:

Go to your AWS EC2 Console.

Launch two new instances.

AMI: Ubuntu Server 22.04 LTS (Debian family).

Instance Type: t2.micro.

Key Pair: Your existing ansible-key.

Security Group: Select your ansible-sg.

CRITICAL: Click Edit on network settings, go to your ansible-sg security group, and add an Inbound Rule to allow HTTP from Anywhere (Port 80). You will need this to see your website.

Note down the Public IPv4 addresses for both instances.

Create Project Folder (on your WSL terminal):

Bash

# Create a new, clean project for this assignment
mkdir ansible-setB4
cd ansible-setB4

# Create directories for playbooks and templates
mkdir playbooks
mkdir templates
Copy and Secure Your Key:

Copy your ansible-key.pem file into the ansible-setB4 folder.

Set the correct permissions:

Bash

chmod 400 ansible-key.pem
Create ansible.cfg:

nano ansible.cfg

Add this content:

Ini, TOML

[defaults]
inventory = inventory
private_key_file = ansible-key.pem
host_key_checking = False
Create inventory:

nano inventory

Add your two new servers. We'll group them as [webservers].

Ini, TOML

[webservers]
<your-web-1-public-ip>
<your-web-2-public-ip>

[all:vars]
ansible_user=ubuntu
Test Connection:

Bash

ansible all -m ping
You should see two green "SUCCESS" messages.

3. Generate Templates Directory
Now, let's create the two Jinja2 templates as requested.

A. Create index.html.j2 This file will dynamically pull in Ansible "facts" (discovered variables) from each server.

Create the file:

Bash

nano templates/index.html.j2
Add this HTML content:

HTML

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Welcome to {{ ansible_hostname }}</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; background-color: #f0f8ff; }
        .container { margin-top: 50px; padding: 20px; background-color: #fff; border-radius: 8px; box-shadow: 0 4px 8px rgba(0,0,0,0.1); }
        h1 { color: #333; }
        p { color: #555; font-size: 1.2em; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Hello from {{ ansible_hostname }}!</h1>
        <p>This server is being managed by Ansible.</p>
        <p>You can reach me at IP address: <strong>{{ ansible_default_ipv4.address }}</strong></p>
    </div>
</body>
</html>
B. Create nginx.conf.j2 This will be our custom Nginx server configuration. We'll use a variable {{ domain_name }} to make it reusable.

Create the file:

Bash

nano templates/nginx.conf.j2
Add this Nginx configuration:

Nginx

server {
    listen 80;
    listen [::]:80;

    # This will be a variable we pass from our playbook
    server_name {{ domain_name }};

    root /var/www/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
4. Playbook: Install Nginx
Create the first playbook to install Nginx.

Create the file:

Bash

nano playbooks/install_nginx.yml
Add this content:

YAML

---
- name: Install and start Nginx
  hosts: webservers
  become: yes

  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: yes

    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Ensure Nginx is started and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: yes
Run the playbook:

Bash

ansible-playbook playbooks/install_nginx.yml
At this point, Nginx is installed and running with its default page.

5. Playbook: Deploy Nginx Configuration
Now, create a separate playbook to deploy your custom Nginx configuration. This playbook will disable the default config and enable yours.

Create the file:

Bash

nano playbooks/deploy_config.yml
Add this content:

YAML

---
- name: Deploy custom Nginx configuration
  hosts: webservers
  become: yes

  vars:
    domain_name: "example.com" # Define the variable for our template

  tasks:
    - name: Copy Nginx config template
      ansible.builtin.template:
        src: templates/nginx.conf.j2
        dest: "/etc/nginx/sites-available/{{ domain_name }}.conf"
        mode: '0644'
      notify: Restart Nginx

    - name: Create symlink to enable our site
      ansible.builtin.file:
        src: "/etc/nginx/sites-available/{{ domain_name }}.conf"
        dest: "/etc/nginx/sites-enabled/{{ domain_name }}.conf"
        state: link
      notify: Restart Nginx

    - name: Remove default Nginx site
      ansible.builtin.file:
        path: /etc/nginx/sites-enabled/default
        state: absent
      notify: Restart Nginx

  # Handlers only run if a task "notifies" them (i.e., if a change was made)
  handlers:
    - name: Restart Nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
Run the playbook:

Bash

ansible-playbook playbooks/deploy_config.yml
This playbook will copy the config, enable it, disable the default, and then restart Nginx only if any of those tasks made a change.

6. Playbook: Deploy Custom HTML Page
Finally, create the third playbook to deploy your custom index.html page. This will replace the default Nginx page content.

Create the file:

Bash

nano playbooks/deploy_html.yml
Add this content:

YAML

---
- name: Deploy custom HTML index page
  hosts: webservers
  become: yes

  tasks:
    - name: Deploy new index.html from template
      ansible.builtin.template:
        src: templates/index.html.j2
        dest: /var/www/html/index.html
        owner: www-data
        group: www-data
        mode: '0644'

    - name: Remove the default Nginx page
      ansible.builtin.file:
        path: /var/www/html/index.nginx-debian.html
        state: absent
Run the playbook:

Bash

ansible-playbook playbooks/deploy_html.yml
Verify Your Work
Open your web browser and navigate to the Public IPv4 address of both of your instances. You should see your new custom web page, and the <h1> tag will show the server's internal hostname (e.g., ip-172-31-xx-xx).

This completes Set B! You have successfully used three separate playbooks to install a web server, configure it with a Jinja2 template, and deploy a custom, dynamic web page.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set C: 
1. Launch two new virtual machine instances. The first should run an operating system from 
the Debian family, and the second should run one from the Red Hat family. 
2. Create a project folder containing an ansible.cfg file and an inventory file. The inventory 
file should contain the details for two managed nodes. Then, establish and test the 
connection between the Ansible control node and the two managed nodes. 
3. Create an Ansible role named "apache" designed to serve an HTML page displaying the 
hostname and IPv4 address on an Apache server. This role should be used in playbooks 
targeting servers within the Debian family. 
4. Create an Ansible role named "httpd" designed to serve an HTML page displaying the 
hostname and IPv4 address on an httpd server. This role should be used in playbooks 
targeting servers within the RedHat family.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
1. & 2. Setup and Inventory (Tasks 1 & 2)
First, let's launch your two different VMs and set up the Ansible project folder.

Launch AWS Instances:

Go to your AWS EC2 Console.

Security Group: Ensure your ansible-sg group allows HTTP (Port 80) from Anywhere (0.0.0.0/0). You need this to see the websites.

Instance 1 (Debian):

Name: debian-server

AMI: Ubuntu Server 22.04 LTS

Key/Security Group: ansible-key and ansible-sg.

Instance 2 (Red Hat):

Name: redhat-server

AMI: Amazon Linux 2023 (Red Hat family)

Key/Security Group: ansible-key and ansible-sg.

Note both Public IPv4 addresses.

Create Project Folder (on your WSL terminal):

Bash

# Create a new project folder
mkdir ansible-setC4
cd ansible-setC4

# Create the 'roles' directory as required
mkdir roles
Copy and Secure Your Key:

Copy your ansible-key.pem file into the ansible-setC4 folder.

chmod 400 ansible-key.pem

Create ansible.cfg:

nano ansible.cfg

Add this content:

Ini, TOML

[defaults]
inventory = inventory
roles_path = ./roles
private_key_file = ansible-key.pem
host_key_checking = False
Create inventory:

nano inventory

Add your two servers, organized into OS-specific groups. This is crucial for targeting your roles.

Ini, TOML

[debian]
<your-ubuntu-public-ip> ansible_user=ubuntu

[redhat]
<your-amazon-linux-public-ip> ansible_user=ec2-user
Test Connection:

Bash

ansible all -m ping
You should get a green "SUCCESS" from both your debian and redhat hosts.

3. Create 'apache' Role (for Debian) 

Now we will create the role scaffold for apache and add its tasks and templates.

Generate Role Structure:

In your ansible-setC4 directory, run:

Bash

ansible-galaxy init roles/apache
This creates the standard role directory structure for you. 

Create the Template:

This is the HTML file that will be served.

Create the file roles/apache/templates/index.html.j2:

Bash

nano roles/apache/templates/index.html.j2
Add this content. It's the same template content as in Set B, which is fine—roles are all about reusability.

HTML

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Welcome to {{ ansible_hostname }}</title>
</head>
<body style="font-family: Arial, sans-serif; text-align: center; margin-top: 50px;">
    <h1>Hello from {{ ansible_hostname }}</h1>
    <p>My IPv4 address is: <strong>{{ ansible_default_ipv4.address }}</strong></p>
    <p>This server is in the <strong>{{ ansible_os_family }}</strong> family.</p>
</body>
</html>
Add the Role Tasks:

This file tells Ansible what to do.

Edit the main tasks file roles/apache/tasks/main.yml: 

Bash

nano roles/apache/tasks/main.yml
Replace all the default content with this:

YAML

---
# tasks file for roles/apache
- name: Install apache2 on Debian
  ansible.builtin.apt:
    name: apache2
    state: present
    update_cache: yes

- name: Deploy custom index.html
  ansible.builtin.template:
    src: index.html.j2
    dest: /var/www/html/index.html
    mode: '0644'
  notify: Restart apache2 # Notifies the handler

- name: Ensure apache2 service is started and enabled
  ansible.builtin.service:
    name: apache2
    state: started
    enabled: yes
Add the Handler:

Handlers are triggered by notify events and are used to restart services. 

Edit the handler file roles/apache/handlers/main.yml:

Bash

nano roles/apache/handlers/main.yml
Replace all default content with this:

YAML

---
# handlers file for roles/apache
- name: Restart apache2
  ansible.builtin.service:
    name: apache2
    state: restarted
4. Create 'httpd' Role (for Red Hat) 

This is the exact same process, but for the Red Hat (httpd) server.

Generate Role Structure:

In your ansible-setC4 directory, run:

Bash

ansible-galaxy init roles/httpd
Create the Template:

We can use the exact same template file.

Create roles/httpd/templates/index.html.j2:

Bash

nano roles/httpd/templates/index.html.j2
Paste the same HTML content you used for the apache role.

Add the Role Tasks:

Edit the main tasks file roles/httpd/tasks/main.yml:

Bash

nano roles/httpd/tasks/main.yml
Replace all default content with this. Note the different package and service names.

YAML

---
# tasks file for roles/httpd
- name: Install httpd on Red Hat
  ansible.builtin.yum:
    name: httpd
    state: present

- name: Deploy custom index.html
  ansible.builtin.template:
    src: index.html.j2
    dest: /var/www/html/index.html
    mode: '0644'
  notify: Restart httpd # Notifies the handler

- name: Ensure httpd service is started and enabled
  ansible.builtin.service:
    name: httpd
    state: started
    enabled: yes
Add the Handler:

Edit the handler file roles/httpd/handlers/main.yml:

Bash

nano roles/httpd/handlers/main.yml
Replace all default content with this:

YAML

---
# handlers file for roles/httpd
- name: Restart httpd
  ansible.builtin.service:
    name: httpd
    state: restarted
5. Create and Run the Main Playbook
You have built your roles. Now you need a "main" playbook (like site.yml) to call them.

Check Your Structure: Your ansible-setC4 directory should now look like this:

ansible-setC4/
├── ansible.cfg
├── inventory
├── roles/
│   ├── apache/
│   │   ├── tasks/main.yml
│   │   ├── templates/index.html.j2
│   │   ├── handlers/main.yml
│   │   └── ... (other empty dirs)
│   └── httpd/
│       ├── tasks/main.yml
│       ├── templates/index.html.j2
│       ├── handlers/main.yml
│       └── ... (other empty dirs)
└── site.yml  <-- We will create this now
Create the Main Playbook:

nano site.yml

Add this content. This playbook has two plays: one for each host group.

YAML

---
- name: Configure Debian Web Servers
  hosts: debian
  become: yes
  roles:
    - apache  # This runs the 'apache' role

- name: Configure Red Hat Web Servers
  hosts: redhat
  become: yes
  roles:
    - httpd   # This runs the 'httpd' role
Run the Playbook!

Bash

ansible-playbook site.yml
Ansible will run the first play, see hosts: debian, and only run the apache role on your Ubuntu server. Then, it will run the second play, see hosts: redhat, and only run the httpd role on your Amazon Linux server.

6. Verify the Deployment
Get the Public IPv4 address for your debian-server and paste it into your browser. You should see your custom page, which will say "This server is in the Debian family."

Get the Public IPv4 address for your redhat-server and paste it into your browser. You should see your custom page, which will say "This server is in the RedHat family."

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Ass-5

Set A: 
1. Deploy postgres Database with adminer on k8s cluster having one master and one worker 
node.
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Phase 1: 🖥️ Provision Your AWS EC2 Nodes
You need two Ubuntu instances. The K8s control plane requires more resources than a t2.micro, so we'll use t2.medium.

Create a K8s Security Group (k8s-sg):

Go to EC2 > Security Groups > Create security group.

Name: k8s-sg

Inbound Rules:

Rule 1 (SSH): Type: SSH, Port: 22, Source: My IP.

Rule 2 (K8s API): Type: Custom TCP, Port: 6443, Source: My IP (and later, you'll add the worker's private IP).

Rule 3 (NodePorts): Type: Custom TCP, Port Range: 30000-32767, Source: Anywhere (0.0.0.0/0) (This allows you to access your web UIs).

Rule 4 (Internal): Type: All traffic, Source: k8s-sg (Select the ID of this same security group. This allows all nodes within the group to communicate freely).

Click Create security group.

Launch Instance 1 (Master):

AMI: Ubuntu Server 22.04 LTS

Instance Type: t2.medium

Name: k8s-master

Key Pair: Your existing ansible-key.

Security Group: Select the k8s-sg you just created.

Click Launch instance.

Launch Instance 2 (Worker):

AMI: Ubuntu Server 22.04 LTS

Instance Type: t2.medium

Name: k8s-worker

Key Pair: Your existing ansible-key.

Security Group: Select k8s-sg.

Click Launch instance.

Phase 2: 📦 Install K8s on BOTH Nodes
You must perform these steps on both the k8s-master and k8s-worker instances. SSH into both machines.

Disable Swap (Required by K8s):

Bash

sudo swapoff -a
# Make it permanent by commenting out the swap line in /etc/fstab
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
Enable Kernel Modules and sysctl Settings:

Bash

# Enable bridged networking
sudo modprobe br_netfilter

# Set K8s networking prerequisites
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply settings without reboot
sudo sysctl --system
Install containerd (Container Runtime):

Bash

sudo apt-get update
sudo apt-get install -y ca-certificates curl

# Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install containerd
sudo apt-get update
sudo apt-get install -y containerd.io

# Configure containerd to use 'systemd' cgroup driver (required by K8s)
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# Restart containerd to apply changes
sudo systemctl restart containerd
Install kubeadm, kubelet, and kubectl:

Bash

# Add Kubernetes GPG key
sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Add Kubernetes repository
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Install K8s tools
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl

# Pin the versions to prevent accidental upgrades
sudo apt-mark hold kubelet kubeadm kubectl
Phase 3: 🚀 Initialize the Master Node
These commands are run ONLY on the k8s-master instance.

Initialize the Cluster:

We'll use Calico for our network plugin, which requires the --pod-network-cidr flag.

Bash

sudo kubeadm init --pod-network-cidr=192.168.0.0/16
IMPORTANT: Save Your Output!

When the command finishes, it will print two crucial pieces of text:

A) The kubectl config commands. It looks like this:

Bash

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
B) The kubeadm join command. It looks like this:

Bash

kubeadm join <MASTER_IP>:6443 --token <TOKEN> \
    --discovery-token-ca-cert-hash sha256:<HASH>
Copy the join command to a safe place. You need it for your worker node.

Configure kubectl on Master:

Run the kubectl config commands (from step 2-A) on your master node:

Bash

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
Install a Network Plugin (Calico):

Your cluster needs a CNI (Container Network Interface) to allow pods to communicate.

Bash

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.3/manifests/calico.yaml
Check Master Status:

Bash

kubectl get nodes
You should see your k8s-master node, and its status will change to Ready after a minute.

Phase 4: 🤝 Join the Worker Node
Now, move to your k8s-worker instance.

Run the Join Command:

Paste the kubeadm join... command (from Phase 3, step 2-B) and run it with sudo:

Bash

sudo kubeadm join <MASTER_IP>:6443 --token <TOKEN> \
    --discovery-token-ca-cert-hash sha256:<HASH>
Verify on Master:

Go back to your k8s-master node.

Run kubectl get nodes again.

After a minute, you should see both k8s-master and k8s-worker in the Ready state.

Congratulations! Your 2-node K8s cluster is now operational.

Phase 5: 🚢 Deploy Postgres and Adminer (Set A, Task 1)
Now, let's complete your actual assignment. Run these commands on your k8s-master node.

Install Git (if not present):

Bash

sudo apt-get install -y git
Clone the Repository:

As specified in the assignment, we'll use the provided Git repository.

Bash

git clone https://github.com/ajay-raut/k8s-postgres.git
Deploy the Application:

The repository contains all the necessary YAML manifest files (Deployments, Services, ConfigMaps, Secrets). We can apply them all at once.

Bash

cd k8s-postgres
kubectl apply -f .
This will create:

postgres-configmap: For database settings.

postgres-secret: For the database password.

postgres-deployment: To run the PostgreSQL pod.

postgres-service: To give the database a stable internal network name.

adminer-deployment: To run the Adminer web UI pod.

adminer-service: To expose Adminer to the outside world using a NodePort.

Verify the Deployment:

Check if your pods are running. It may take a minute or two for the images to be pulled and the containers to start.

Bash

kubectl get pods
Expected Output:

NAME                        READY   STATUS    RESTARTS   AGE
adminer-deployment-xxxxx    1/1     Running   0          1m
postgres-deployment-xxxxx   1/1     Running   0          1m
Access Your Adminer Web UI:

Find the NodePort: Get the port that K8s assigned to your adminer-service.

Bash

kubectl get service adminer-service -o jsonpath='{.spec.ports[0].nodePort}'
This will output a port number (e.g., 31234).

Open Your Browser: Go to the following address: http://<YOUR_WORKER_NODE_PUBLIC_IP>:<NODE_PORT>

Login to Adminer:

    * **System:** `PostgreSQL`
Server: postgres-service (This is the K8s internal service name)

Username: postgres (This is from the postgres-secret.yaml in the repo)

Password: admin (This is from the postgres-secret.yaml in the repo)

Database: postgres (This is from the postgres-configmap.yaml in the repo)

Click Login. If it's successful, you are now managing your K8s-deployed PostgreSQL database from Adminer!

This completes Set A of your Kubernetes assignment.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set B: 
1. Deploy a two-tier application, including a PostgreSQL database and a Go API on k8s.  
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

This is a great next step, moving from a pre-packaged application (Adminer) to a custom two-tier application.

We will deploy this application onto the same 2-node Kubernetes cluster (one master, one worker) that you set up in Set A.

Let's begin. All commands should be run on your k8s-master node.

1. 📂 Clone the Application Repository
First, get the application's manifest files from the Git repository provided in your assignment.

Bash

# Go back to your home directory
cd ~

# Clone the new repository
git clone https://github.com/ajay-raut/k8s-2-tier-Go-Postgres-API.git

# Move into the new directory
cd k8s-2-tier-Go-Postgres-API
2. 🚀 Deploy the Two-Tier Application
This repository contains all the necessary YAML files for both the PostgreSQL database and the Go API. You can apply them all at once.

Bash

kubectl apply -f .
This single command creates all the required K8s objects:

Postgres Tier:

postgres-deployment.yaml: Runs the PostgreSQL database pod.

postgres-service.yaml: Creates an internal-only ClusterIP service so the Go API can find the database.

Go API Tier:

go-api-deployment.yaml: Runs the custom Go API pod. This deployment's manifest is configured to look for the database at postgres-service.

go-api-service.yaml: Exposes the Go API to the outside world using a NodePort service.

3. ✅ Verify the Deployment
Let's check that all the pods for both tiers are running. It may take a minute or two for the container images to be pulled and the containers to start.

Bash

kubectl get pods
You should see two new pods running (along with your pods from Set A, if you haven't deleted them):

NAME                                READY   STATUS    RESTARTS   AGE
go-api-deployment-xxxxx-xxxxx       1/1     Running   0          1m
postgres-deployment-xxxxx-xxxxx   1/1     Running   0          1m
4. 🧪 Test the Go API
Now for the final test. The Go API is exposed via a NodePort. We need to find that port to access the application.

Get the Service Details:

Bash

kubectl get service go-api-service
Expected Output:

NAME             TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
go-api-service   NodePort   10.101.50.60   <none>        80:30001/TCP   2m
Look at the PORT(S) column. In this example, the service is listening on port 30001 on the node. Your port will likely be in the 30000-32767 range.

Access the API:

You can now access your Go API using the Public IP of your k8s-worker node and the NodePort you just found.

Open your web browser and go to: http://<YOUR_WORKER_NODE_PUBLIC_IP>:<NODE_PORT>

Alternatively, you can use curl from your master node's terminal:

Bash

curl http://<YOUR_WORKER_NODE_PUBLIC_IP>:<YOUR_NODE_PORT>
If successful, you should see a JSON message from the Go API, indicating it has successfully connected to the PostgreSQL database.

You have now successfully deployed a custom two-tier application on Kubernetes!
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Ass-6

Set A: 
1. Launch MLflow tracking server & also Configure the MLflow Tracking Client. 
2. Search experiments and view the metadata associated with the Experiments that are on the 
server. 
3. Display default experiment name and life cycle stage. 
4. Create apples experiment with meaningful tags & Use search_experiments() to search on 
the project_name tag key. 
5. Generates a synthetic dataset for predicting apple sales demand with seasonality and 
inflation. 
6. Using MLflow Tracking Train and log the model. 
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Prerequisites: Install Required Libraries
Before you begin, you'll need to install MLflow and some common data science libraries.

Bash

pip install mlflow scikit-learn pandas numpy
1. Launch MLflow Tracking Server & Configure Client
You need one terminal to run the server and another to run your scripts.

Terminal 1 (Run the Server): Start the MLflow server. This will host the UI and store all your experiment data.

Bash

# This creates a local 'mlruns' directory to store data
mlflow server --host 127.0.0.1 --port 8080
You can now access the MLflow UI by opening http://127.0.0.1:8080 in your browser.

Terminal 2 (Configure Your Client): Open a new terminal. You must tell your Python scripts where to log their data by setting this environment variable.

Bash

# For WSL/Linux/macOS
export MLFLOW_TRACKING_URI="http://127.0.0.1:8080"

# (If you are using Windows PowerShell, use this instead):
# $env:MLFLOW_TRACKING_URI = "http://127.0.0.1:8080"
All Python scripts in the steps below should be run from this second terminal.

2. & 3. Search Experiments & View Default Metadata
Let's create a script to view all experiments. This will show you the "Default" experiment that MLflow creates.

Create a file named search_experiments.py:

Bash

nano search_experiments.py
Paste this code inside:

Python

import mlflow
from mlflow.entities import ViewType

# MLFLOW_TRACKING_URI is already set as an environment variable
client = mlflow.tracking.MlflowClient()

# Search for all experiments
experiments = client.search_experiments(view_type=ViewType.ALL)

print("--- All Experiments ---")
for exp in experiments:
    print(f"Name: {exp.name}")
    print(f"Experiment ID: {exp.experiment_id}")
    print(f"Lifecycle Stage: {exp.lifecycle_stage}")
    print(f"Artifact Location: {exp.artifact_location}")
    print("-----------------------")
Run the script:

Bash

python search_experiments.py
Expected Output: You will see the details for the "Default" experiment, including its name and "active" lifecycle stage.

4. Create "Apples" Experiment & Search for It
Now, let's create the specific "apples experiment" with the tags mentioned in your lab manual.

Create a file named create_experiment.py:

Bash

nano create_experiment.py
Paste this code inside:

Python

import mlflow

client = mlflow.tracking.MlflowClient()

# 1. Define tags for the experiment
experiment_tags = {
    "project_name": "grocery-forecasting",
    "store_dept": "produce",
    "team": "stores-ml",
    "project_quarter": "Q3-2023",
    "mlflow.note.content": "This experiment contains the produce models for apples."
}

try:
    # 2. Create the experiment
    experiment_name = "Apple_Models"
    experiment_id = client.create_experiment(
        name=experiment_name, tags=experiment_tags
    )
    print(f"Successfully created experiment '{experiment_name}' with ID: {experiment_id}")

except mlflow.exceptions.RestException as e:
    print(f"Experiment '{experiment_name}' already exists. {e}")

# 3. Search for the experiment using its tag
print("\n--- Searching for 'grocery-forecasting' project ---")
experiments = client.search_experiments(
    filter_string="tags.`project_name` = 'grocery-forecasting'"
)

for exp in experiments:
    print(f"Found: {exp.name} (ID: {exp.experiment_id})")
Run the script:

Bash

python create_experiment.py
Check your MLflow UI (http://127.0.0.1:8080): You will now see your new "Apple_Models" experiment in the list!

5. & 6. Generate Dataset, Train, and Log the Model
This final script will do everything: generate the synthetic data, train a simple model, and log all the parameters, metrics, and the model itself to your "Apple_Models" experiment.

Create a file named train_model.py:

Bash

nano train_model.py
Paste this code inside:

Python

import mlflow
import mlflow.sklearn
import pandas as pd
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

# --- Task 5: Generate Synthetic Dataset ---
def get_apple_sales_data():
    """Generates a synthetic dataset for apple sales."""
    print("Generating synthetic apple sales data...")
    np.random.seed(42)
    num_days = 365
    days = pd.date_range(start="2023-01-01", periods=num_days)

    # Seasonality (sales higher in autumn)
    seasonality = 100 + 50 * np.sin(2 * np.pi * (days.dayofyear - 270) / 365)

    # Inflation/Trend (sales slowly increasing)
    inflation = 0.1 * np.arange(num_days)

    # Noise
    noise = np.random.normal(0, 25, num_days)

    # Price (randomly fluctuating)
    price = np.random.uniform(1.0, 1.5, num_days)

    # Calculate sales
    sales = (seasonality + inflation - 50 * price + noise).astype(int)

    df = pd.DataFrame({
        "day_of_year": days.dayofyear,
        "price": price,
        "sales": sales
    })
    df = df.clip(lower=0) # No negative sales
    print("Dataset generated.")
    return df

# --- Task 6: Train and Log Model ---
def train():
    df = get_apple_sales_data()

    # Set the experiment we want to log to
    mlflow.set_experiment("Apple_Models")

    # Start a new MLflow run
    with mlflow.start_run(run_name="Simple Linear Regression") as run:
        print(f"Starting run: {run.info.run_name}")
        print(f"Run ID: {run.info.run_id}")

        # Prepare data
        X = df[["day_of_year", "price"]]
        y = df["sales"]
        X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

        # 1. Log Parameters
        mlflow.log_param("model_type", "LinearRegression")
        mlflow.log_param("test_size", 0.2)

        # Train model
        model = LinearRegression()
        model.fit(X_train, y_train)
        preds = model.predict(X_test)

        # 2. Log Metrics
        rmse = np.sqrt(mean_squared_error(y_test, preds))
        r2 = r2_score(y_test, preds)

        mlflow.log_metric("rmse", rmse)
        mlflow.log_metric("r2", r2)
        print(f"Logged Metrics: RMSE={rmse:.2f}, R2={r2:.2f}")

        # 3. Log the Model
        mlflow.sklearn.log_model(model, "model")
        print("Model logged successfully.")

        # Log a dataset artifact (optional)
        df.to_csv("apple_sales.csv", index=False)
        mlflow.log_artifact("apple_sales.csv")
        print("Artifact (dataset) logged.")

if __name__ == "__main__":
    train()
Run the script:

Bash

python train_model.py
7. View the Final Result
Go back to your MLflow UI at http://127.0.0.1:8080. Click on the "Apple_Models" experiment. You will now see your first run, "Simple Linear Regression". Click on it to see the parameters, metrics (RMSE, R2), and the saved model artifact. You have successfully completed Set A!

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

Set B: 
1. Launch MLflow tracking server for Hyperparameter Tuning & Deployment. 
2. The Challenge: Wine Quality Prediction 
We'll optimize a neural network that predicts wine quality from chemical properties. Our 
goal is to minimize Root Mean Square Error (RMSE) by finding the optimal combination 
of: 
● Learning Rate: How aggressively the model learns 
● Momentum: How much the optimizer considers previous updates 
3. Step 1: Prepare Your Data 
4. Step 2: Define Your Model Architecture 
5. Step 3: Set Up Hyperparameter Optimization
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

This is a fantastic and very practical assignment. You'll be combining MLflow's tracking capabilities with a hyperparameter optimization library (hyperopt) to find the best neural network for predicting wine quality.

Here is the complete, self-contained solution for Set B.

Phase 1: Setup
You'll need one terminal to run the MLflow server and a second to run your Python script.

1. Install Required Libraries: This script requires mlflow, tensorflow (for the neural network), scikit-learn (for data prep), pandas, and hyperopt (for the tuning).

Bash

pip install mlflow tensorflow scikit-learn pandas hyperopt
2. Task 1: Launch MLflow Tracking Server  In your first terminal, start the MLflow server. This will host the UI and store all your experiment runs.

Bash

# This creates a local 'mlruns' directory to store data
mlflow server --host 127.0.0.1 --port 8080
Keep this terminal running. You can open the MLflow UI in your browser at http://127.0.0.1:8080.

3. Configure Your Client: In your second terminal, set the environment variable so your Python script knows where to log its data.

Bash

# For WSL/Linux/macOS
export MLFLOW_TRACKING_URI="http://127.0.0.1:8080"

# (If using Windows PowerShell, use this instead):
# $env:MLFLOW_TRACKING_URI = "http://127.0.0.1:8080"
Phase 2: The Hyperparameter Tuning Script
In your second terminal, create a single Python file named tune_wine_model.py. This script will perform all the steps (2 through 6) from your assignment.

Bash

nano tune_wine_model.py
Copy and paste the entire block of code below into this file.

Python

import mlflow
import mlflow.tensorflow
import pandas as pd
import tensorflow as tf
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from hyperopt import fmin, tpe, hp, Trials, STATUS_OK
import numpy as np
import warnings

# Suppress warnings
warnings.filterwarnings("ignore")

# --- Task 3: Prepare Your Data ---
def prepare_data():
    print("Preparing data...")
    # Load the well-known wine quality dataset
    data_url = "http://archive.ics.uci.edu/ml/machine-learning-databases/wine-quality/winequality-red.csv"
    data = pd.read_csv(data_url, sep=';')

    # Split into features (X) and target (y)
    X = data.drop(columns=["quality"])
    y = data["quality"]

    # Create training and validation sets
    X_train, X_val, y_train, y_val = train_test_split(X, y, test_size=0.2, random_state=42)

    # Scale the features
    scaler = StandardScaler()
    X_train_scaled = scaler.fit_transform(X_train)
    X_val_scaled = scaler.transform(X_val)
    
    return X_train_scaled, X_val_scaled, y_train, y_val

# --- Task 4: Define Your Model Architecture ---
def build_model(learning_rate, momentum):
    model = tf.keras.models.Sequential([
        tf.keras.layers.Dense(64, activation='relu', input_shape=[X_train_scaled.shape[1]]),
        tf.keras.layers.Dense(32, activation='relu'),
        tf.keras.layers.Dense(1) # Output layer for regression
    ])

    # Compile the model with the optimizer using our tuning parameters
    optimizer = tf.keras.optimizers.SGD(learning_rate=learning_rate, momentum=momentum)
    
    model.compile(
        optimizer=optimizer,
        loss='mean_squared_error',
        metrics=[tf.keras.metrics.RootMeanSquaredError(name='rmse')] # Goal: minimize RMSE
    )
    return model

# --- Task 5: Set Up Hyperparameter Optimization ---
def objective(params):
    """
    The function that Hyperopt will try to minimize.
    It trains a model and returns the validation RMSE.
    """
    learning_rate = params['learning_rate']
    momentum = params['momentum']

    # Start a new MLflow run for this specific trial
    with mlflow.start_run() as run:
        mlflow.log_params(params)

        model = build_model(learning_rate, momentum)

        # Train the model, automatically logging metrics to MLflow
        model.fit(
            X_train_scaled, y_train,
            validation_data=(X_val_scaled, y_val),
            epochs=20,
            batch_size=32,
            callbacks=[mlflow.tensorflow.autolog(every_n_iter=1, log_models=False)], # Autolog metrics
            verbose=0
        )

        # Evaluate the final model and get the validation RMSE
        results = model.evaluate(X_val_scaled, y_val, verbose=0)
        val_rmse = results[1] # Index 1 is 'rmse' as defined in compile()
        
        print(f"  Trial complete: LR={learning_rate:.4f}, Momentum={momentum:.2f} -> RMSE={val_rmse:.4f}")

        # Log the final validation RMSE (our target metric)
        mlflow.log_metric("val_rmse", val_rmse)
        
        # Log the trained model as an artifact
        mlflow.sklearn.log_model(model, "model")

        # Hyperopt needs a 'loss' value to minimize
        return {'loss': val_rmse, 'status': STATUS_OK}

# --- Main script execution ---

# 1. Prepare data once
X_train_scaled, X_val_scaled, y_train, y_val = prepare_data()

# 2. Set the MLflow Experiment
experiment_name = "Wine_Quality_Optimization"
mlflow.set_experiment(experiment_name)
print(f"Logging runs to MLflow experiment: '{experiment_name}'")

# 3. Define the Hyperopt search space for our parameters
space = {
    'learning_rate': hp.loguniform('learning_rate', np.log(0.001), np.log(0.1)),
    'momentum': hp.uniform('momentum', 0.0, 1.0)
}

# --- Task 6: Run the Hyperparameter Optimization ---
print("Running hyperparameter optimization...")
trials = Trials()
best_params = fmin(
    fn=objective,
    space=space,
    algo=tpe.suggest, # Tree-structured Parzen Estimator (a smart tuning algorithm)
    max_evals=15,     # Number of different models to train
    trials=trials
)

print(f"\nOptimization complete!")
print(f"Best parameters found: {best_params}")

Phase 3: Run the Script
Now, execute the script from your second terminal.

Bash

python tune_wine_model.py
You will see the script prepare the data and then start training 15 different models, one for each hyperparameter combination. Each trial's results will be logged to your MLflow server.

Phase 4: Analyze and Register (Tasks 7 & 8)
Now, go to your MLflow UI in the browser: http://127.0.0.1:8080.


Task 7: Analyze Results in MLflow UI 

Click on the "Wine_Quality_Optimization" experiment in the left-hand menu.

You will see a table with all 15 runs.

Click the val_rmse column header to sort the runs from lowest (best) to highest (worst).

Visualize the results:

Select several of the best and worst runs by clicking the checkbox next to them.

Click the "Compare" button.

Select the "Parallel coordinates" plot type.

This plot will instantly show you which ranges of learning_rate and momentum led to the lowest val_rmse (the blue lines).


Task 8: Register Your Best Model 

Go back to the experiment's table view.

Click on the Run Name of the best-performing run (the one with the lowest val_rmse).

Scroll down to the "Artifacts" section. You will see the "model" folder that was logged.

Click the "Register Model" button.

Select "Create New Model" and enter the name wine-quality-predictor.

Click "Register".

Your model is now registered! You can go to the "Models" tab in the main MLflow UI to see it. From there, you can transition its version from "None" to "Staging" or "Production".

This completes Set B.
**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************
Set C: 
1. Deploy Your Model Locally (Test your model with a REST API deployment). 
2. Build Production Container (Create a Docker container for deployment and test it). 
Answer:
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

This is the final and most exciting part of the MLOps assignment: deploying your model to make it usable.

We will use the MLflow Tracking Server you already have running (from Set B) and the wine-quality-predictor model you registered.

You will need three separate terminals for this set.

Terminal 1: Your MLflow server (should still be running).

Terminal 2: Your main work terminal (where you ran the Set B script).

Terminal 3: A new, clean terminal for sending test requests.

Prerequisites
Terminal 1: MLflow Server Running Make sure your MLflow server is running from Set B. If not, start it:

Bash

mlflow server --host 127.0.0.1 --port 8080
Terminal 2: Set Tracking URI Open your main work terminal and set the environment variable:

Bash

export MLFLOW_TRACKING_URI="http://127.0.0.1:8080"
1. 🚀 Deploy Your Model Locally (Task 1)
This task uses MLflow's built-in tools to serve your registered model as a local REST API.

In Terminal 2, run the following command. This fetches your "latest" registered model and starts a new web server for it on port 5001.

Bash

mlflow models serve -m "models:/wine-quality-predictor/latest" -p 5001 --no-conda
-m "models:/...": This is the Model Registry URI for your model.

-p 5001: We use port 5001 so it doesn't conflict with the MLflow UI on 8080.

--no-conda: This tells MLflow to use your current Python environment (which already has tensorflow installed) instead of trying to create a new one.

Your Terminal 2 will now be busy running this API server.

In Terminal 3, we will send a test request using curl. The model expects 11 features. We'll send a sample row of data.

Bash

curl -X POST -H "Content-Type:application/json" -d '{
  "dataframe_split": {
    "columns": ["f1", "f2", "f3", "f4", "f5", "f6", "f7", "f8", "f9", "f10", "f11"],
    "data": [
      [0.1, -0.2, 0.3, -0.4, 0.5, -0.6, 0.7, -0.8, 0.9, -1.0, 1.1]
    ]
  }
}' http://127.0.0.1:5001/invocations
Expected Output: You will get a JSON response with a prediction, proving your API is live!

JSON

{
  "predictions": [
    [5.4321] 
  ]
}
(Your prediction value will vary).

2. 🐳 Build Production Container (Task 2)
Now, let's package this entire application (model + web server + dependencies) into a self-contained Docker container.

Stop the local server. Go to Terminal 2 and press Ctrl + C to stop the server you started in the previous step.

Make sure Docker is running. You must have Docker Desktop (or the Docker daemon on Linux) running on your machine.

In Terminal 2, run the MLflow command to build the container:

Bash

mlflow models build-docker -m "models:/wine-quality-predictor/latest" -n "wine-quality-api:v1" --no-conda
-n "wine-quality-api:v1": This sets the name and tag for your new Docker image.

MLflow will now:

Fetch your model.

Create a Dockerfile.

Run the docker build process.

Install tensorflow, mlflow, gunicorn, etc., inside the image.

This may take a few minutes.

Run your new container: Once the build is complete, run your newly built image. This command maps your host's port 5002 to the container's internal port 8080.

Bash

docker run -p 5002:8080 "wine-quality-api:v1"
Your Terminal 2 is now running your production-ready container.

In Terminal 3, test the containerized API. It's the same curl command as before, but now pointed to port 5002.

Bash

curl -X POST -H "Content-Type:application/json" -d '{
  "dataframe_split": {
    "columns": ["f1", "f2", "f3", "f4", "f5", "f6", "f7", "f8", "f9", "f10", "f11"],
    "data": [
      [0.1, -0.2, 0.3, -0.4, 0.5, -0.6, 0.7, -0.8, 0.9, -1.0, 1.1]
    ]
  }
}' http://127.0.0.1:5002/invocations
Final Result: You will get the exact same JSON prediction as before, but this time it's being served from a portable, isolated Docker container.

This completes all of Assignment 6! You have successfully trained, tuned, registered, and deployed a machine learning model.

**************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************




