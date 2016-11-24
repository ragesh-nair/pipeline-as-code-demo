#!groovy

stage ('Dev') {
node ('swarm') {
    checkout scm
    mvn 'clean package'
    dir('target') {stash name: 'war', includes: 'x.war'}
}
}

stage ('QA') {
parallel(longerTests: {
    runTests(30)
}, quickerTests: {
    runTests(20)
})
}

stage (name: 'Staging', concurrency: 1) {
node ('swarm') {
    deploy 'staging'
try {
    checkpoint('Before production')
} catch (NoSuchMethodError _) {
    echo 'Checkpoint feature available in CloudBees Jenkins Enterprise.'
}
}
}

}

def mvn(args) {
    sh "${tool 'mvn'}/bin/mvn ${args}"
}

def runTests(duration) {
    node {
        sh "sleep ${duration}"
        }
    }

def deploy(id) {
    unstash 'war'
    sh "cp x.war /tmp/${id}.war"
}

def undeploy(id) {
    sh "rm /tmp/${id}.war"
}
