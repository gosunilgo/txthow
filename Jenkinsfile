echo 'app'

node('master') {
   stage 'Build Stage'
   checkout scm 
   sh 'ls'
   step([$class: 'LastChangesPublisher', since:'PREVIOUS_REVISION',specificRevision: '', format: 'LINE', matchWordsThreshold: '0.25', matching: 'NONE', matchingMaxComparisons: '1000', showFiles: true, synchronisedScroll: true])
   stage("last-changes") {
       def publisher = LastChanges.getLastChangesPublisher "PREVIOUS_REVISION", "SIDE", "LINE", true, true, "", "", "", "", ""
       publisher.publishLastChanges()
       def changes = publisher.getLastChanges()
       println(changes.getEscapedDiff())
       for (commit in changes.getCommits()) {
                 println(commit)
                 sh 'git diff --name-only' +  commit
                 def commitInfo = commit.getCommitInfo()
                 println(commitInfo)
                 println(commitInfo.getCommitMessage())
                 println(commit.getChanges())
       }
                                                              }
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
