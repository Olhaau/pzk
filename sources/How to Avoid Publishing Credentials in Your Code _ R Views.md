---
title: How to Avoid Publishing Credentials in Your Code · R Views
---

# How to Avoid Publishing Credentials in Your Code · R Views

url:: https://rviews.rstudio.com/2019/03/21/how-to-avoid-publishing-credentials-in-your-code/
up: [[sources]]

#source

by Roland Stevenson

*Roland Stevenson is a data scientist and consultant who may be reached on [Linkedin](https://www.linkedin.com/in/roland-stevenson/).*

When accessing an API or database in R, it is often necessary to provide credentials such as a login name and password. You may find yourself being prompted with something like this:

![Figure: Providing credentials via an interactive prompt](https://rviews.rstudio.com/post/2019-03-15-roland_files/roland.png)

When writing an R script that requires a user to provide credentials, you will want a way to have the script prompt the user or, better yet, programatically provided the credentials in the R script. Either way, be careful! You don’t want to put your credentials out there in the clear for all the world to see. Best practices emphatically state:
> As with every programming language, it is important to **avoid publishing code with your credentials in plain text**.

So, how can we provide credentials without putting them in the script itself? There are a variety of options described in RStudio’s [“Databases using R”](https://db.rstudio.com/best-practices/managing-credentials/).

I will focus on two cases:

* simply prompting for credentials via [`rstudioapi`](https://cran.r-project.org/package=rstudioapi)
  * suitable for simple credential management
* storing sets of encrypted credentials in a local file via the R [`keyring`](https://cran.r-project.org/package=keyring) package
  * suitable for more complicated credential management

### Prompting for a username and password

If an R Script requires only one set of credentials and those credentials are easy to remember, it may be easiest to prompt the user for them using `rstudioapi`. A typical example would be prompting users for their username and password to access a corporate database:

    username <- rstudioapi::askForPassword("Database username")
    password <- rstudioapi::askForPassword("Database password")
This method may also be convenient if the user’s credentials tend to change over time.

### The R Keyring package

A more sophisticated option is to use the R `keyring` package to store and access encrypted credentials locally. This might be more suitable if multiple credentials exist to access a variety of services (think multiple access tokens). With `keyring`, one password unlocks the keyring which then provides access to all the credentials.

To use the `keyring` package, a user only needs to to install and load the package and define three strings: the keyring name, a keyring service, and the username that we want to associate our secret credentials with.

The following example shows how to create a keyring name `my_keyring`, with credentials to access `my_database` as `my_username`. We first create a `backend_file` type of keyring which will store the encrypted credentials in the user’s home directory ( `~/.config/r-keyring`). With `keyring_create`, we prompt for the password that will unlock the keyring. Finally, we store a credential in the keyring with `set` before locking it with `keyring_lock`.

    library(keyring)

    # Set variables to be used in keyring.
    kr_name <- "my_keyring"
    kr_service <- "my_database"
    kr_username <- "my_username"

    # Create a keyring and add an entry using the variables above
    kb <- keyring::backend_file$new()
    # Prompt for the keyring password, used to unlock keyring
    kb$keyring_create(kr_name)
    # Prompt for the credential to be stored in the keyring
    kb$set(kr_service, username=kr_username, keyring=kr_name)
    # Lock the keyring
    kb$keyring_lock(kr_name)

    # The encrypted keyring file is now stored at ~/.config/r-keyring/ and can be
    # accessed by any R program that provides the keyring password
We can store credentials for multiple usernames per service, and multiple services per keyring. This is ideal in the case of an application that must access a variety of services via access tokens. The encrypted credentials file can either be published with the code, or perhaps for extra security, distributed via a separate channel.

## Retrieving credentials

To retrieve credentials, set the same three variables and use the `keyring` `get()` function, which will prompt us for the keyring password that we set when we called `create`. A retrieval script might look like this:

    library(keyring)
    library(DBI)

    # Set variables to be used in keyring.
    kr_name <- "my_keyring"
    kr_service <- "my_database"
    kr_username <- "my_username"

    # Output the stored password: normally you would not want to do this
    keyring::backend_file$new()$get(service = kr_service,
                                    user = kr_username,
                                    keyring = kr_name)


    # Establish connection to Teradata retrieving the password from the keyring.
    dbConnect(drv = odbc::odbc(),
                    dsn = "my_dsn", # set DSN options in ~/.odbc.ini
                    pwd = keyring::backend_file$new()$get(service = kr_service,
                                                          user = kr_username,
                                                          keyring = kr_name))

With this, we are able to retrieve arbitrary credentials for a particular username and service, allowing us to manage much more complicated sets of credentials with a single password.

So, which is the best way to ensure that plain text credentials are not published with code? If your code relies on a limited number of credentials, an interactive prompt may be the more suitable choice: code users know what their username and password are and can easily enter them interactively.

If the code requires multiple, hard-to-remember, or cumbersome to provide credentials, you might want to consider using keyrings. Users will only need to provide one password, which will unlock the keyring and provide access to all credentials.
