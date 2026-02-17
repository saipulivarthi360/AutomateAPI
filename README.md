🚀 API Test Automation: Postman, Jenkins, & GitHub

This repository contains an automated API testing suite. Every time a change is pushed to the Postman collection via Git, Jenkins automatically detects the change, pulls the latest code, and executes the tests using the Newman CLI.
🏗️ Environment Setup
1. Jenkins Installation (Ubuntu)

Jenkins was installed manually on Ubuntu 24.04/22.04 using the official Long-Term Support (LTS) repository.

    Java Prerequisite: OpenJDK 17.

    Key Security: The Jenkins GPG key was imported to /usr/share/keyrings/jenkins-keyring.asc to bypass "unsigned repository" errors.

    Installation Commands:
    Bash

    sudo apt update
    sudo apt install net-tools jenkins -y
    sudo systemctl enable --now jenkins

2. The Testing Engine: Node.js & Newman

To run Postman collections in a headless environment, we upgraded the Node.js engine to v22.x to meet modern dependency requirements.

    Upgrade Node.js:
    Bash

    curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
    sudo apt install -y nodejs

    Install Newman:
    Bash

    sudo npm install -g newman

🔗 Integration Flow
1. Postman to GitHub

The Postman Collection is linked directly to this repository via the Postman Git Integration.

    Workflow: Edit in Postman → Commit & Push via Postman Sidebar → GitHub Repo Updated.

    Format: Exported as .json in the /tests directory.

2. GitHub to Jenkins (Automation)

We utilize the SCM Polling method to bridge the local Jenkins server with GitHub.

    Mechanism: Jenkins "Polls" GitHub every minute (* * * * *).

    Credentials: GitHub Personal Access Token (PAT) stored as "Username with Password" in Jenkins.

    Build Trigger: "Poll SCM" enabled.

⚙️ Jenkins Job Configuration

    Job Type: Freestyle Project.

    NodeJS Tooling: Configured in Global Tool Configuration to use the system Node.js (/usr).

    Execution Script (Execute Shell):
    Bash

    # Ensure Newman is ready
    if ! command -v newman &> /dev/null; then
        npm install newman
    fi

    # Run the collection and generate a JUnit report
    newman run ./tests/your-collection.json --reporters cli,junit --reporter-junit-export results.xml

    Post-build Actions: * Publish JUnit test result report: Targets results.xml for visual trend graphs.

🛠️ How to Add New Tests

    Open the collection in Postman.

    Add your requests and write tests in the Tests tab (e.g., pm.response.to.have.status(200)).

    Save the collection.

    Click the Git icon in the right sidebar.

    Commit your changes and Push.

    Wait 60 seconds; Jenkins will automatically trigger the build and report the results.

📋 Troubleshooting History

    GPG Errors: Solved by manually downloading the Jenkins .asc key and updating the signed-by path in sources.list.d.

    EBADENGINE: Solved by removing default Ubuntu Node.js and installing v22.x via Nodesource.

    Dependency Issues: Solved using sudo apt-get install -f to catch the net-tools requirement.
