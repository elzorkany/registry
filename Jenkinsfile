#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    stage('check java') {
        bat "java -version"
    }

    stage('clean') {
        bat "chmod +x mvnw"
        bat "./mvnw clean"
    }

    stage('install tools') {
        bat "./mvnw com.github.eirslett:frontend-maven-plugin:install-node-and-yarn -DnodeVersion=v10.15.3 -DyarnVersion=v1.15.2"
    }

    stage('yarn install') {
        bat "./mvnw com.github.eirslett:frontend-maven-plugin:yarn"
    }

    stage('backend tests') {
        try {
            bat "./mvnw verify"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/test-results/**/TEST-*.xml'
        }
    }

    stage('frontend tests') {
        try {
            bat "./mvnw com.github.eirslett:frontend-maven-plugin:yarn -Dfrontend.yarn.arguments='run test'"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/test-results/TESTS-*.xml'
        }
    }

    stage('packaging') {
        bat "./mvnw verify -Pprod -DskipTests"
        archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
    }
}
