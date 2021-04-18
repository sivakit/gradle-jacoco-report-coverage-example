A simple and self explain example that show you how to use **sonarqube** and **jacoco** to generate:

* Unit tests coverage
* Line coverage
* Unit tests success report

# Used framworks
* [Jacoco coverage gradle plugin](https://docs.gradle.org/current/userguide/jacoco_plugin.html)
* [Sonar gradle plugin ](https://docs.gradle.org/current/userguide/sonar_plugin.html)
* [How to configure sonar plugin](http://docs.sonarqube.org/display/SONAR/Analyzing+with+SonarQube+Scanner+for+Gradle)

# Running it

    $ sonar.sh start
    $ gradle sonarqube
    # open browser on http://localhost:9000

You can use `gradle sonarqube -x test` too


# Print
![](http://i.imgur.com/xg80Jbd.png)
