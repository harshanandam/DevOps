#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG='devops@consulting.com'
    def SFDC_HOST ='https://login.salesforce.com'
    def JWT_KEY_CRED_ID ='4e7641c5-7327-4e6c-b286-39385563d52d'
    def CONNECTED_APP_CONSUMER_KEY='3MVG9fe4g9fhX0E5OQ5io6ZEuFQFyKM21dJCnk4UDFdgWf10iYIbnzW0bw7RMnj55Sk7RIz0u8VCWiuu9qgIr'
    def serverkey = '-----BEGIN RSA PRIVATE KEY-----
MIIEpgIBAAKCAQEAw7XBChpt3JPe7LwJzbreYqc2vOzwSkyYKO6iN7GKQAtI3G4y
bA1/hbhyhtFtyXWxr9XxZg+CXYR+aN5DLPCj0kt3jdoLNxpAKVsMTnV/YYk9mWbl
ek65eNWKAO2ZXIxRGRE5fpRQkJ88GknZ1JXqD1ZpFPOSacdjWP56f7OFgT8jAl90
pNumNxpYrF+Q6gVZDtxN0O8Gn3hjXhqSzN8AkyqeRtzfv83koOOFjyD76YhYxdZN
ind8C6BL9gwqXO9rygCmcCGwxpKmh4tpas3OojzsPN2RwGuPAN2mqZvhWIXZUlwd
tLdhyhZSkJcW6v8TW3Sxn1wWb5CyZnWFxjv59QIDAQABAoIBAQCnYeSG8pGxjJql
mDE+Tity7pZnQLJGqXmd0HLW9TZLjhszw9/GAElnoZf57FZcbheZTn5Wjr8tomrG
4AlN/0XtTvQiUzEyYHYtqJw+4kker1UKxTFQyNHiIagVISEAQVX+/XdR5iF9f2LV
DQKLyefUVFAtRiCb7Zbvfz5fx7dQEKPCKKjqNVDBv5zOf2JQldObuJRUuNaQxLpw
6Va1MZM4PIgAVvwI4rvMNGApdF1t619h3IcZfaiXwFzzmwm32emqetpfdPugdblb
rVe4h36L4nrHj0kdrL13hh2iJ9NSxFngdno69oCLFDNk20BqG/CQVIs3jzBiQ2Q1
S/sdEH3hAoGBAOMun0on3a9fle4c6BcI6JTwLGZAoPtC35o8xfGlZs44qEG/AiAh
nDIQt3Xhed506rUg1aWxthThz+udx97GGfyApNFdP7ETGTZDiAyFsC349HB1gYPi
839ZEPEMt9zRzd2rMRAk77lcn7Yjy5xFX70WdoeSpQCM0BUpj39Xdr9ZAoGBANyJ
H8bfRP1vzCWx001I4CFRKxVj6jBZaFNzmztqc6jVfRgkF3kkGS+5ktiQ3OZWICby
qQYNFXMRflOjh+YEqPOjwHIDSdza+HNXmVT3f313/jijxD1XkQLxjJtp7CUt9I5G
4ksP/lbq3jVPOluBc9zWmfg2DmXyqnFJ2bbnEvf9AoGBAI6Bsl37/+2AkjYZX+UD
K5IxzkgeBl1Wp5jCwpBAZuq5U6JaROUO8EHfhpWlaKMIyCnyfNJBVaxnsdtkz/mI
XsirkbfbPJbBGjGVzwO74LYV0o+wxhuLA59AqYXrqnIUEYUZW996q/2kgnLopVJT
miisrJGChYqAyg5cUa1Zmh2RAoGBANLIoT2kgr01EMtqdoqkv8w4V1R/hgOmQOea
VGFqspdJTeI4BNN9ZDFQToF9nBTvdpWjU1f8Q2cS7kokwCxigdU0yyht6jgUdmlP
7bbfQ9R5Ttt184ep3WkR1BFrIRC8JsWiDIIwDCmpHK+ZRS7WwRXva4RorkRUtwHG
0zdVDrVFAoGBALxyv+30uSbzVSpjR54FOXzyf3ejHEMzP4xHYg6HkWg3dRfkCq5b
aP0dAqMJNUAlr55RYHAtiUE2GrnjceXLRepeTWhIx0+cPntWMrXMG6eRugz9wWY/
ga96wNUKzexw8VbzZ5UdFt8epPyMQT5tOj8APVPITT1YyYj4PrDFVWPE
-----END RSA PRIVATE KEY-----'

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'salesforce-cli'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
	
        stage('Deploye Code') {
            if (isUnix()) {
		rdf = sh "echo $jwt_key_file"
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${serverkey} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${serverkey}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}
			  
            printf rmsg
            
            println(rmsg)
        }
    }
}
