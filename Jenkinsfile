  
pipeline {
  agent any
  stages {
    stage('CheckOut') {
      parallel {
        stage('CheckOut') {
          steps {
            sh '''pwd
sudo cp -r -v -f * /jendata'''
          }
        }

        stage('Debug') {
          steps {
            sh '''if ls /jendata | grep hello-world.txt
then
  echo "success"
else
  exit 1
fi'''
          }
        }

      }
    }
    
    stage('Confirmation') {
      input {
        message 'Should we continue?'
        id 'Yes, we should.'
        submitter 'adarsh'
        parameters {
          string(name: 'PERSON', defaultValue: 'My Name', description: 'You are')
        }
      }
      steps {
        echo "Called by: ${params.name}"
        sh 'echo "Jenkins is setting up the kubernetes setup"'
      }
    }
    
    stage('Deploying') {
      steps {
        sh '''if sudo kubectl get pvc | grep my-httpd-pvc
then
	echo "PVC already present"
	echo "changing PVC"
	sudo kubectl apply -f  /jendata/mypvc.yml
else
	echo "Creating PVC"
	sudo kubectl create -f /jendata/mypvc.yml
fi
if sudo kubectl get svc | grep myserver
then
	echo "Already present, hence changing conf"
	sudo kubectl apply -f /jendata/svc.yml
else
        sudo kubectl create -f /jendata/svc.yml
fi
if sudo kubectl get deployment | grep mywebdeploy
then
	echo "Deployment already present, hence, rolling out"
	sudo kubectl rollout restart deployment/mywebdeploy
else
	echo "creating deployment"
	sudo kubectl create -f /jendata/deployment.yml
fi'''
      }
    }

    stage('Testing') {
      steps {
        sh '''kubectl get service > kubegetfile.txt
siteaddress=$(awk \'/my-service/ {print $4":8080"}\' kubegetfile.txt)
status=$(curl -o /dev/null -s -w "%{http_code}" $(siteaddress))
if status==200
then
    echo "everything running file"
else
    echo "not file"
    exit 1
fi'''
      }
    }

  }
}
