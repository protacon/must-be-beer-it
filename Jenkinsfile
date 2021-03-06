@Library("PTCSLibrary@savpek") _

// Podtemplate and node must match, dont use generic names like 'node', use more specific like projectname or node + excact version number.
// This is because CI environment reuses templates based on naming, if you create node 7 environment with name 'node', following node 8 environment
// builds may fail because they reuse same environment if label matches existing.
podTemplate(label: 'must-be-beer-it', idleMinutes:30,
  containers: [
    containerTemplate(name: 'docker', image: 'ptcos/docker-client:latest', alwaysPullImage: true, ttyEnabled: true, command: '/bin/sh -c', args: 'cat')
  ]
) {
    def project = 'must-be-beer-it'
    def branch = (env.BRANCH_NAME)
    def namespace = "must-be-beer-it"

    node('must-be-beer-it') {
        stage('Checkout') {
            checkout_with_tags()
        }
        stage('Package') {
            container('docker') {
                def published = publishContainerToGcr(project, branch);

                toK8sTestEnv() {
                    sh """
                        kubectl apply -f ./k8s/${branch}.yaml
                        kubectl set image deployment/$project-$branch $project-$branch=$published.image:$published.tag --namespace=$namespace
                    """
                }
            }
        }
    }
  }