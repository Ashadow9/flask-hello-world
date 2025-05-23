# flask-hello-world

This project demonstrates a simple Python Flask web application deployed automatically using Jenkins CI/CD. The source code is hosted on Codeberg and the pipeline is triggered via webhook on every push.


## 1. Clone the Repository
Clone this repository to your VM:
```bash
git clone https://codeberg.org/Ashadow9/flask-hello-world.git
```

![image](https://github.com/user-attachments/assets/19c52bb4-ee55-4f57-bd97-5b8717461a3c)

and go into the project directory
```bash
cd flask-hello-world
```
## 2. Set Up Jenkins
Install Jenkins on your VM. 

Start Jenkins: After installation, start Jenkins by accessing http://localhost:8080 on your browser. The first time, you'll need to unlock Jenkins using the initial password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

## 3. Configure Webhook on Codeberg


Go to your repository's settings on Codeberg:



Settings → Webhooks

Add a new webhook with the following details:

Payload URL:```http://<your-jenkins-server>/github-webhook/ ```

Content Type: ```application/json```

Which events would you like to trigger this webhook?: ```Push events```

## 4. Create Jenkins Pipeline


Create a New Item:

Go to Jenkins → ``` New Item ```

Name: ```flask-hello-world-pipeline```


Type: ```Pipeline```

Click ```OK```

Configure Pipeline: In the pipeline configuration page, add the following code in the pipeline script section:

```bash

pipeline {
    agent any

    environment {
        REPO_URL = 'https://codeberg.org/Ashadow9/flask-hello-world.git'
        APP_DIR = 'flask-hello-world'
        VENV_DIR = 'venv'
    }

    stages {
      

        stage('Clone Repository') {
            steps {
                script {
                    if (fileExists(APP_DIR)) {
                        dir(APP_DIR) {
                            sh 'git pull origin main'
                        }
                    } else {
                        sh "git clone ${REPO_URL}"
                    }
                }
            }
        }

        stage('Install Dependencies in Venv') {
            steps {
                dir(APP_DIR) {
                    sh '''
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Restart Flask App') {
            steps {
                dir(APP_DIR) {
                    sh '''
                        pkill -f app.py || true
                        . venv/bin/activate
                        nohup python app.py > app.log 2>&1 &
                    '''
                }
            }
        }
    }
}

```
3.Save the pipeline configuration.

## 5. Trigger Pipeline
Make a change to the README.md file (or any other file in the repository).

Commit and push the changes to Codeberg.

![image](https://github.com/user-attachments/assets/d763e8ca-15e4-495e-b8f7-6ee36db21aec)

```bash
git add .
git commit -m "Trigger Jenkins pipeline"
git push origin main
```

The webhook will trigger Jenkins to start the pipeline and deploy the updated Flask app.
![image](https://github.com/user-attachments/assets/09e9aa3c-4530-464b-ad42-589b0fcf842d)

![image](https://github.com/user-attachments/assets/73389dd5-eed5-415f-961e-1d37b71ea844)


## 6. Verify Deployment


After Jenkins completes the pipeline execution, the Flask app will be running on the server or VM. You can verify the deployment by visiting http://<your-server-ip>:5000 to see the “Hello World from Jenkins!” message.
![image](https://github.com/user-attachments/assets/49600fab-7baf-4288-a2ac-4ecbf348de71)


### Explanation of How the Automation Works

•Jenkins is triggered by a Codeberg webhook

•It pulls the latest code

•Creates a virtual environment using python3 -m venv

•Installs packages using pip install -r requirements.txt

•Starts the Flask app in the background with nohup

•Ensures clean redeployment on every commit

 ### Assumptions and Notes

•The app runs on port 5000; reverse proxy (e.g., Nginx) is not configured

•The app uses nohup for background execution — consider gunicorn or supervisord for production



# Short Write-up – Of My CI/CD Approach

•For this assessment, I implemented a simple CI/CD pipeline using Jenkins and Codeberg to automatically deploy a Python Flask "Hello World" app to a server.

### My Approach:

•Flask App Setup:

I created a minimal Flask application with a single route returning "Hello, World!" and pushed it to a public Codeberg repository.

•Jenkins Installation & Configuration:

I installed Jenkins on an EC2 instance and configured it with the required plugins (Git + Pipeline). I then created a pipeline job that uses a Jenkinsfile to automate deployment.

### Webhook Integration:

I configured a webhook in Codeberg to trigger Jenkins on every git push, enabling automatic deployments.

## Pipeline Logic (Jenkinsfile):

•Clones the Codeberg repo

•Ensures python3-full and venv are installed

•Sets up a Python virtual environment

•Installs app dependencies from requirements.txt

•Starts the Flask app using nohup in the background

•Kills any existing running app to ensure clean restarts

### Verification:
After each push, Jenkins automatically redeploys the latest version. I accessed the app via the server’s IP on port 5000.

##  Tools Used:
•Codeberg (Git hosting)

•Jenkins (CI/CD)

•Flask (Web app)

•Python Virtualenv (Safe dependency management)
