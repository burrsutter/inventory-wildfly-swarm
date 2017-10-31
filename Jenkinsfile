podTemplate(name: 'maven33', label: 'maven33', cloud: 'openshift', containers: [
    containerTemplate(name: 'jnlp',
                image: 'openshift/jenkins-slave-maven-centos7',
                workingDir: '/tmp',
                envVars: [
                    envVar(key: 'MAVEN_MIRROR_URL',value: 'http://nexus/nexus/content/groups/public/')
                ],
                cmd: '',
                args: '${computer.jnlpmac} ${computer.name}')
]){
node("maven") {
  stage("Build JAR") {
    git url: "https://github.com/burrsutter/inventory-wildfly-swarm"
    sh "mvn clean package"
    stash name:"jar", includes:"target/inventory-1.0-SNAPSHOT-swarm.jar"
  }

  stage("Build Image") {
    unstash name:"jar"
    sh "oc start-build inventory-s2i --from-file=target/inventory-1.0-SNAPSHOT-swarm.jar"
    openshiftVerifyBuild bldCfg: "inventory-s2i", waitTime: '19', waitUnit: 'min'
  }

  stage("Deploy") {
    openshiftDeploy deploymentConfig: "inventory"
  }
}
}