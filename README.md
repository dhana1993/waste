pipeline{
    agent any
    parameters {
        choice(
                name: 'DEPLOY_TO',
                choices: [' ', 'QC' , 'UAT'],
                description: 'Select any one env' )

        string(
                name: 'PATH',
                defaultValue: '',
                description: 'Enter war file path')


    }
    stages{

        stage('test'){
            parallel{
                stage('QC'){
                    when {
                        // Only say hello if a "greeting" is requested
                        expression { params.DEPLOY_TO == 'QC' }
                    }
                    steps {
                        script{
                            echo '*****************    QC     *************'
                            echo "$PATH"
                            sh '''/opt/wildfly/bin/jboss-cli.sh controller=10.91.10.162 -c --user=$WILDFLY_USER --password=$WILDFLY_psw --commands="deploy $PATH/NewFaveo.war --force"   '''
                            echo '*****************    QC  DEPLOYMENT SUCCESS    *************'
                        }
                    }
                }
                stage('UAT'){
                    when {
                        // Only say hello if a "greeting" is requested
                        expression { params.DEPLOY_TO == 'UAT' }
                    }
                    steps {
                        echo "Hello, *************!"
                    }
                }
            }
        }
    }
}
