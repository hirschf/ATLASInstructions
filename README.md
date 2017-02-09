# ATLAS Installation Instructions
Installation instructions for the open source tool ATLAS

# Step 1: Install PostgreSQL using Homebrew

Begin by making sure you have the latest version of Homebrew installed

`brew update`

Next, install PostgreSQL

`brew install postgresql`

Confirm PostgreSQL was successfully installed by checking the version

`postgres --help`

# Step 2: Create a database

To create a database for the first time, use the following command

`createdb your_database_name `

Login to your database

`psql`

This will prompt you with an output that looks something like this:

>psql (9.6.1)
>Type "help" for help.
>
>User=#

From here, you can type: \l, \password, or \q to list your databases, change your password, and quit respectively

# Step 3: Install Maven

`brew install maven`

To confirm that maven was successfully installed

`mvn -version`

# Step 4: Clone the WebAPI project

For this next step, navigate to a directory you'd like your project to be placed and run

`git clone https://github.com/OHDSI/WebAPI.git`