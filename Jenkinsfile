echo 'app'

node('master') {
   stage 'Build Stage'
   lastChanges()
   lastChanges since: 'LAST_SUCCESSFUL_BUILD', format:'SIDE',matching: 'LINE'

   sh 'mvn --settings settings.xml -Dbuildnum=${BUILD_NUMBER} clean test deploy'

   stage 'CI Setup'
   sh 'mvn --settings settings.xml docker:build -DpushImage -Dbuildnum=${BUILD_NUMBER}'

   stage 'QA Deployment'

   stage 'Deploy Approval'
   timeout(time:5, unit:'MINUTES') {
      input 'Awaiting Release Manager Approval?'
   }

   stage 'Prod Deployment'
}
