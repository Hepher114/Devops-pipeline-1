# SonarQube Integration with Jenkins

This guide provides step-by-step instructions for configuring SonarQube (SonarCloud) with Jenkins for continuous code quality analysis.

## Prerequisites
- Jenkins instance with administrator access
- SonarCloud account (free tier available)
- GitHub, GitLab, or Bitbucket repository for your project

## Configuration Steps

### 1. SonarCloud Account Setup

1. **Create Account**:
   - Navigate to [SonarCloud.io](https://sonarcloud.io)
   - Sign up using your preferred version control provider (GitHub, GitLab, Bitbucket, etc.)

2. **Generate Authentication Token**:
   - Click on your profile picture → "My Account"
   - Navigate to "Security" tab
   - Under "Generate Tokens", enter a descriptive name (e.g., "Jenkins Integration")
   - Click "Generate" and copy the token (store it securely - you won't be able to see it again)

### 2. Jenkins Credentials Setup

1. **Add SonarQube Token to Jenkins**:
   - In Jenkins, go to `Manage Jenkins` → `Manage Credentials`
   - Under "System", select `Global credentials (unrestricted)`
   - Click `Add Credentials`:
     - **Kind**: Secret text
     - **Scope**: Global
     - **Secret**: Paste your SonarQube token here
     - **ID**: `sonarqube-key` (recommended for consistency)
     - **Description**: "SonarQube Authentication Token"

### 3. Plugin Installation

1. **Install SonarQube Scanner Plugin**:
   - Go to `Manage Jenkins` → `Manage Plugins`
   - Select the `Available` tab
   - Search for "SonarQube Scanner"
   - Check the box and click `Install without restart`

### 4. SonarQube Server Configuration

1. **Configure Server Connection**:
   - Navigate to `Manage Jenkins` → `Configure System`
   - Scroll to the `SonarQube servers` section
   - Click `Add SonarQube`:
     - **Name**: `sonar-server` (or your preferred identifier)
     - **Server URL**: `https://sonarcloud.io`
     - **Server authentication token**: Select the `sonarqube-key` credential from the dropdown

### 5. SonarQube Scanner Installation

1. **Configure Scanner Tool**:
   - Go to `Manage Jenkins` → `Global Tool Configuration`
   - Find the `SonarQube Scanner` section
   - Click `Add SonarQube Scanner`:
     - **Name**: `sonar-scanner` (recommended)
     - **Install automatically**: (Recommended) Check this box
     - **Version**: Select the latest stable version

### 6. Project Configuration (Optional)

For Jenkins pipeline projects, add this to your Jenkinsfile:

```groovy
stage('SonarQube Analysis') {
  steps {
    withSonarQubeEnv('sonar-server') {
      sh 'mvn clean verify sonar:sonar'
      // Or for other languages:
      // sh 'sonar-scanner -Dsonar.projectKey=YOUR_PROJECT_KEY'
    }
  }
}
