JIRAChangelogBuilder
====================

Communicates with [Atlassians JIRA](https://www.atlassian.com/software/jira) and generates a changelog based on a customisable template file for a given [JIRA query (JQL)](https://confluence.atlassian.com/display/JIRA/Advanced+Searching).

Compilation
-----------

Run 'mvn install' from the base directory to generate build/jira-changelog-builder.jar. This will also automatically execute the bundled test case.

Usage
-----

From the command line:

    java -jar jira-changelog-builder.jar <JIRA_URL> <JIRA_username> <JIRA_password> <JIRA_project_key> <version> <template_list> [<flags>]
  
Where the arguments are used as follows:
  
  *  `<JIRA_URL>`: The URL of the JIRA instance (e.g. https://somecompany.atlassian.net).
  *  `<JIRA_username>`: The username used to log into JIRA.
  *  `<JIRA_password>`: The password used to log into JIRA.
  *  `<JIRA_project_key>`: The key of the project in JIRA.
  *  `<version>`: Specifies up to which version the changelog should be generated.
  *  `<template_root>`: The path on disk to the directory containing the mustache template files. All files in `<template_list>` are relative to this path.
  *  `<template_list>`: A CSV list of paths to template files. Each templated changelog is saved into a new file which can be processed at a later stage.
  *  `<flags>` (optional): One or more of the following flags:
    * `--jql "some arbitrary JQL"`: Append the given JQL to the issue filter. eg status = "Ready for Build".
    * `--object-cache-path /some/path`: The path on disk to the cache, if you do not use this, no cache will be used. Using a cache is highly recommended.
    * `--debug`: Print debug/logging information to standard out. This will also force errors to go to the standard out and exit with code 0 rather than 1.
    * `--changelog-description-field "field_name"`: The name of the field in JIRA you wish to use as the changelog description field. If you do not use this, it will default to the summary field.
    * `--changelog-file-name /some/path/filename`: A CSV list of paths on disk to the files you wish to output the file changelogs to. If you do not use this, the file changelog will be written to changelog#.txt in the working directory by default (where # is the changelog file number).
* `--eol-style (NATIVE|CRLF|LF)`: The type of line endings you wish the changelog files to use. Valid values are NATIVE (system line endings), CRLF (Windows line endings) or LF (UNIX line endings). If you do not use this, the changelogs will use the default system line endings (NATIVE).

When specifying `--object-cache-path` the Java objects used to store the changelog information for a single version are serialized. The cache will always be used for any version other that the current version that has been specified in `<version>` however sometimes you need to rebuild an older version from JIRA (i.e. the version has changed in some way). This can be done simply by removing the serialised file, this fill will be found in the path specified by `--object-cache-path` with a file name of `<project>_<version>.ser` where `<project>` and `<version>` match the command line agruments. The JIRAChangelogBuilder will then regenerate these as required.

Testing
-------

In order to execute the unit tests properly (and build/install the program), you must create a file named credentials.properties and place it in the base directory. In this file, add the following values:
  
  * `url = <URL>` where `<URL>` is the web address of the JIRA instance.
  * `username = <username>` where `<username>` is the username of a user that can access the JIRA instance.
  * `password = <password>` where `<password>` is the password for the specified user.
  * `project = <project>` where `<project>` is the identifier (key) of the JIRA project.
  * `version = <version>` where `<version>` is the version up to which the changelog should be generated.

Tests can be manually executed by running 'mvn test' from the base directory.
