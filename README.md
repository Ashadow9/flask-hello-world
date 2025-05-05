# flask-hello-world

1. Clone the Repository
Clone this repository to your VM:
```bash
git clone https://codeberg.org/Ashadow9/flask-hello-world.git
```

![image](https://github.com/user-attachments/assets/19c52bb4-ee55-4f57-bd97-5b8717461a3c)

and go into the project directory
```bash
cd flask-hello-world
```
2. Set Up Jenkins
Install Jenkins on your VM. 

Start Jenkins: After installation, start Jenkins by accessing http://localhost:8080 on your browser. The first time, you'll need to unlock Jenkins using the initial password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
3. Configure Webhook on Codeberg
Go to your repository's settings on Codeberg:


Settings → Webhooks

Add a new webhook with the following details:

Payload URL:```http://<your-jenkins-server>/github-webhook/ ```

Content Type: application/json

Which events would you like to trigger this webhook?: Push events

4. Create Jenkins Pipeline
Create a New Item:

Go to Jenkins → New Item

Name: Flask-Hello-World

Type: Pipeline

Click OK

Configure Pipeline: In the pipeline configuration page, add the following code in the pipeline script section:
