1 - Create and prepare the new maven repo
Create a new repository from your Bitbucket space (e.g. “maven-repo” for the name). Choose a public access level for now.

2 - Deploy an artifact
Create a local simple maven project.
In the pom.xml, add the following snippet (it use the wagon-git plugin):

				`<pluginRepositories>`
					`<pluginRepository>`
						`<id>synergian-repo</id>`
						`<url>https://raw.github.com/synergian/wagon-git/releases</url>`
					`</pluginRepository>`
				`</pluginRepositories>`

				`<build>`
					`<extensions>`
						`<extension>`
							`<groupId>ar.com.synergian</groupId>`
							`<artifactId>wagon-git</artifactId>`
							`<version>0.1.6</version>`
						`</extension>`
					`</extensions>`
				`</build>   `

				`<distributionManagement>`
					`<repository>`
						`<id>username-bitbucket</id>`
						`<name>Bitbucket repository</name>`
						`<url>git:master://git@bitbucket.org:username/yourbitbucketreponame.git</url>`
					`</repository>`
				`</distributionManagement>`


And with the command mvn deploy you can push the artifact (snapshot or not) in the Bitbucket maven repository.

You can see the structure of my public Bitbucket maven repository.

3 - Get an artifact
From your project, in your pom.xml, you can insert this part:

`<repositories>`
    `<repository>`
        `<id>username-bitbucket</id>`
        `<name>My Bitbucket maven repository</name>`
        `<url>https://bitbucket.org/username/yourbitbucketreponame/raw/master/</url>`
        `<layout>default</layout> `
    `</repository>          `
`</repositories>`

You can execute the command mvn clean package to test.

Work with a private Bitbucket repository
If you want to work with a private Bitbucket repository, you just have to tell your Bitbucket password to maven in plain text or crypted. Without this setting maven will download artifacts with the contents of the Bitbucket login page.

Plain text password
No secure choice but fast to test. Insert the following snippet in your ~/.m2/settings.xml:

 `<servers>`
    `<server>`
        `<id>username-bitbucket</id>`
        `<username>yourbitbucketusername</username>`
        `<password>yourbitbucketpassword</password>   `
    `</server>`
  `</servers>`

The id tag must be the same in the settings.xml and pom.xml. Now you can set the Bitbucket repository access level to private and execute the command mvn clean package from your maven project to test.

## Crypted password

Secure choice. First, you have to generate the crypted maven master password. The following bash help you to do that.
Just copy/past/execute the code (change mav3n with another password if you want):

[ -f ~/.m2/settings-security.xml ] && echo "You already have a maven master password" || mvn --encrypt-password mav3n | awk -F " " '{print "<settingsSecurity><master>" $1 "</master></settingsSecurity>" }' > ~/.m2/settings-security.xml | echo "~/.m2/settings-security.xml created with the master hash password" 
Then you can get your crypted Bitbucket password for maven with the following command:

mvn --encrypt-password yourBitbucketPassword 
In your ~/.m2/settings.xml, change your plain text Bitbucket password with the crypted Bitbucket password.







- https://web.archive.org/web/20131014101143/http://blog.chrissom.com/dev/bitbucket-as-a-maven-repository.html