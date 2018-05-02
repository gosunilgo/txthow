echo 'Starting pipeline for Scientific Calculator App'

node('master') {
   stage 'Build Stage'
   git credentialsId: 'aa3fafc1-4552-4c47-b117-3aef5d0a5ad7', url: 'http://10.50.1.72/microservices-lab/ArithmeticOps.git'
   sh 'mvn --settings settings.xml -Dbuildnum=${BUILD_NUMBER} clean test deploy'

   stage 'CI Setup'
   sh 'mvn --settings settings.xml docker:build -DpushImage -Dbuildnum=${BUILD_NUMBER}'

   stage 'QA Deployment'
   sh 'cp pom.xml target'
   git branch: 'feature_task_refactor.vg', credentialsId: 'aa3fafc1-4552-4c47-b117-3aef5d0a5ad7', url: 'http://10.50.1.72/demo-lab/cAPItis.git'
   sh './ecs/checkEnv.py ./env/env_qa02_micro.json'
   sh 'export RELEASE_VERSION=$(mvn help:evaluate -Dexpression=project.version -f target/pom.xml | grep -v "^\\[")_${BUILD_NUMBER}; ./ecs/dockerSpawn.py -t -n qa02-addition-svc -b \${RELEASE_VERSION} -e ./env/env_qa02_micro.json -v ./env/env_qa02_micro_attrs.json'

   stage 'Deploy Approval'
   timeout(time:5, unit:'MINUTES') {
      input 'Awaiting Release Manager Approval?'
   }

   stage 'Prod Deployment'
   //git branch: 'feature_task_refactor.vg', credentialsId: 'aa3fafc1-4552-4c47-b117-3aef5d0a5ad7', url: 'http://10.50.1.72/demo-lab/cAPItis.git'
   sh './ecs/checkEnv.py ./env/env_prod02_micro.json'
   sh 'export RELEASE_VERSION=$(mvn help:evaluate -Dexpression=project.version -f target/pom.xml | grep -v "^\\[")_${BUILD_NUMBER}; ./ecs/dockerSpawn.py -t -n prod02-addition-svc -b \${RELEASE_VERSION} -e ./env/env_prod02_micro.json -v ./env/env_prod02_micro_attrs.json'
}
