@Library('libpipelines@preproduction') _

hose {
    EMAIL = 'cd'
    DEPLOYONPRS = false
    BUILDTOOL = 'docker'
    GENERATE_QA_ISSUE = true
    JIRAPROJECT = 'cd-test-project'
    GRYPE_TEST = true
    DEV = { config ->
        doCustomStage(conf:config, buildToolOverride: [CREDENTIALS_ID: "STRATIOCOMMIT-TEST_GH_API_TOKEN", CUSTOM_COMMAND: 'python python/test.py'], stageName: "Running python scripts", runOnFinal: true)
        doDockers(conf:config, dockerImages:[[conf:config, dockerfile: "Dockerfile", image: "cd-test-mbuilder1", target: "build1"]])
       
    }
}
