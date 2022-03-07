@Library('libpipelines@parallel-conf') _

hose {
    EMAIL = 'cd'
    ANCHORE_TEST = false
    DEPLOYONPRS = true
    GENERATE_QA_ISSUE = true
    ENABLE_CONCURRENT_BUILDS = false
    SHOW_RAW_YAML = false
    VERSIONING_TYPE = "stratioVersion"
//    ITPARAMETERS = """
//    | -DZOOKEEPER_HOSTNAME=%%ZOOKEEPER
//    | """

    DEV = { config ->
        doCompile(config)
        //doUT(config)
	    
	parallel(
		ZOOKEEPER: {
			def zookeeperServices = [
				['ZOOKEEPER': [
            				'image': 'jplock/zookeeper:3.5.2-alpha',
	    				healthcheck: 2181,
            				'env': [
                  				'zk_id=1'],
            				'sleep': 180,
	    				'volumes': ["/tmp2:/tmp2"]
				]]	
			]
			doIT(conf: config, parameters: "-DZOOKEEPER_HOSTNAME=%%ZOOKEEPER", services: zookeeperServices, stageName: 'Zookeeper')		
		},
		SFTP: {
			def sftpServices = [
				['SFTP': [
					image: 'stratio/rocket-sftp-it:0.1.0-M1',
					sleep: 600,
					healthcheck: 2222,
					cmd: 'foo:pass:1001',
					volumes: ['/tmp:/home/foo/tmp']
				]]
			]
			doIT(conf: config, parameters: "-DSFTP_HOSTNAME=%%SFTP", services: sftpServices, stageName: 'SFTP')
		},
		POSTGRES: {
			def postgresServices = [
				['POSTGRES': [
					image: 'postgres:9.6',
					sleep: 60,
					healthcheck: 5432,
					env: ['POSTGRES_USER=admin', 'POSTGRES_PASSWORD=1234', 'POSTGRES_DATABASE=db', 'PGDATA=/pgtmpfs'],
					k8sVolumes: [
						[volumeMount: [mountPath: "/pgtmpfs"], volume: [emptyDir: [medium: "Memory", sizeLimit: "256Mi"]]]
						]
					]]
				]
			doIT(conf: config, parameters: "-DPOSTGRES_HOST=%%POSTGRES", services: postgresServices, stageName: 'Postgres')
		},
		SFTP2: {
			def sftpServices2 = [
				['SFTP2': [
					image: 'stratio/rocket-sftp-it:0.1.0-M1',
					sleep: 600,
					healthcheck: 2222,
					cmd: 'foo:pass:1001',
					volumes: ['%%WORKSPACE:/home/foo/tmp']
				]]
			]
			doIT(conf: config, parameters: "-DSFTP_HOSTNAME=%%SFTP", services: sftpServices2, stageName: 'SFTP2')
		}
	)
	
	/*
	    parallel(UT: {
        	doUT(config)
            }, IT: {
                doIT(config)
            }, failFast: true)
	    */
	//doIT(config)
	    /*
	useClonedVolume(config) { 
		doIT(config)	    
	}
	*/
        doPackage(config)
	//doStaticAnalysis(conf: config)
	doDeploy(conf: config)
	//doDockers(conf:config, dockerImages: [[conf: config, image: "cd-test"]])
	doDocker(conf: config, credentials: 'ATHENS_SSH_KEY')
    }
}
