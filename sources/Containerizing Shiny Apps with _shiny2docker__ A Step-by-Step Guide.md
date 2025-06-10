---
title: Containerizing Shiny Apps with {shiny2docker}: A Step-by-Step Guide
---

# Containerizing Shiny Apps with {shiny2docker}: A Step-by-Step Guide

url:: https://www.r-bloggers.com/2025/06/containerizing-shiny-apps-with-shiny2docker-a-step-by-step-guide/?utm_source=phpList&utm_medium=email&utm_campaign=R-bloggers-daily&utm_content=HTML
up: [[sources]]

#source

[This article was first published on
**[Rtask](https://rtask.thinkr.fr/containerizing-shiny-apps-with-shiny2docker-a-step-by-step-guide/)**

, and kindly contributed to
[R-bloggers](https://www.r-bloggers.com/)

]. (You can report issue about the content on this page
[here](https://www.r-bloggers.com/contact-us/)

)

---

Want to share your content on R-bloggers?
[click here](https://www.r-bloggers.com/add-your-blog/)

if you have a blog, or
[here](http://r-posts.com/)

if you don't.

You can read the original post in its original format on [Rtask](https://rtask.thinkr.fr/) website by ThinkR here: [Containerizing Shiny Apps with {shiny2docker}: A Step-by-Step Guide](https://rtask.thinkr.fr/containerizing-shiny-apps-with-shiny2docker-a-step-by-step-guide/)

Deploying a Shiny application to different servers or sharing it with others can be challenging due to differences in R package versions and system requirements. Containerization offers a solution: by packaging your Shiny app and its environment into a Docker container, you ensure it runs consistently anywhere Docker is available. However, not every Shiny developer is familiar with writing Dockerfiles from scratch, and not everyone uses the [{golem}](https://github.com/ThinkR-open/golem) framework, which includes deployment tools natively. This is where the [**{shiny2docker}**](https://github.com/VincentGuyader/shiny2docker) package comes in. In this post, we’ll introduce {shiny2docker} – a tool that automates Dockerfile creation for Shiny apps – and walk through a simple example of using it to containerize a Shiny application. The tutorial is beginner-friendly, with plenty of commentary, but also informative for DevOps professionals looking to streamline Shiny deployments.  

## Why Dockerize Shiny Apps?

**Reproducibility and Consistency:** One major reason to containerize your Shiny app is to capture a consistent R environment. Docker containers bundle the operating system, R installation, packages, and all dependencies your app needs. This eliminates the classic “works on my machine” problem – if it works in the container, it will work on any host running Docker. By containerizing, you ensure your Shiny app has the same R package versions and system libraries wherever it’s deployed.

**Ease of Deployment:** With a Docker image of your app, deploying is straightforward. You (or your DevOps team) can run the image on any server or cloud service that supports Docker. This can simplify migrating from development to production or scaling up instances of your Shiny application. Instead of manually configuring servers with R and packages, you just ship the container.

**Isolation and Stability:** Docker provides isolation from other processes on the host system. Your Shiny app runs in its own environment without risking interference from other software. This isolation helps maintain stability and can improve security as well.

In short, containerization allows Shiny developers to package **everything** the app needs to run – code, packages, system libraries – into a portable unit. Now, let’s see how {shiny2docker} simplifies this process.  

## Introducing {shiny2docker}

**{shiny2docker}** is an R package designed to streamline the process of containerizing Shiny applications using Docker. It automates the generation of the essential Docker files and manages R package dependencies with **{renv}** , making it much easier to deploy Shiny apps in reproducible. In other words, {shiny2docker} will **write a Dockerfile for you**, based on your Shiny app’s requirements, and even help set up continuous integration for building the Docker image if needed.

Some key features of {shiny2docker} include:

* **Automated Dockerfile Creation:** The main function `shiny2docker()` inspects your Shiny application and generates a Dockerfile tailored to it. It leverages your app’s `renv.lock` file (if available) to pin R package versions. If you haven’t been using {renv}, don’t worry – {shiny2docker} can create a lockfile for you on the fly (using `attachment::create_renv_for_prod` under the hood). This ensures all necessary R packages for your app are accounted for in the Docker image.

* **.dockerignore Generation:** To keep your Docker image lean, {shiny2docker} automatically creates a `.dockerignore` file. This file lists patterns of files/folders to exclude from the build context (such as your local data, documentation, etc.), which can significantly reduce build times and image size.

* **Dependency Management with {renv}:** By integrating with {renv}, the package ensures that the versions of R packages inside the container match those you developed with, achieving reproducibility and consistency across different environments. The container build process will use the lockfile to install the exact package versions your app needs.

* **CI/CD Integration:** For more advanced users, {shiny2docker} provides helpers to set up continuous integration pipelines. With a single function call, you can add a GitLab CI configuration or a GitHub Actions workflow to your project. For example, `set_gitlab_ci()` adds a GitLab CI YAML file configured to build and push your Docker image to the registry, and `set_github_action()` places a GitHub Actions workflow file to build and deploy your image on GitHub Container Registry. This is a boon for DevOps engineers looking to automate Shiny app deployment in a CI/CD pipeline.

In summary, {shiny2docker} handles the heavy lifting of containerization: it writes Dockerfiles, manages R package versions, and even helps automate builds. Now let’s get our hands dirty with a practical example.  

## Getting Started: Installation and Setup

Before we begin, make sure you have a recent version of **R** installed and that **Docker** is installed and running on your system (you dont need docker on your system to create a Dockerfile, but if you want to build/run your app using docker you need docker). You don’t need deep Docker knowledge to follow along, but you should have Docker up and running (for example, Docker Desktop on Windows/Mac or the Docker Engine on Linux).

Next, install the **{shiny2docker}** package from CRAN (it’s a recent package, first released in 2025):

```
install.packages("shiny2docker")
```

This will also install any required dependencies. Once installed, load the package:

```
library(shiny2docker)
```

Also, ensure you have **{shiny}** installed (for developing the app) and **{renv}** to manage dependencies.

Finally, you should of course have a Shiny application that you want to containerize. For this tutorial, we’ll create a simple example app from scratch.  

## Example: Containerizing a Shiny App Step-by-Step

Let’s go through the process step by step by containerizing a simple Shiny app. Our example app will be minimal – a histogram of the famous Old Faithful geyser data – but the process applies to any Shiny app (large or small).  

### Step 1: Prepare a Simple Shiny Application

First, we need a Shiny app to containerize. We’ll write a basic `app.R` that defines a UI and server. In a real project, you might have separate `ui.R` and `server.R` files or a more complex app structure, but {shiny2docker} works with any standard Shiny app directory.

```
# app.R -- a simple Shiny app 
# (Make sure this file is in its own directory, e.g., "myapp/app.R")

library(shiny)

# Define UI for the application
ui <- fluidPage(
  titlePanel("Hello Shiny!"),
  sidebarLayout(
    sidebarPanel(
      # Input: Slider for the number of bins in the histogram
      sliderInput("bins", "Number of bins:",
                  min = 1, max = 50, value = 30)
    ),
    mainPanel(
      # Output: Histogram plot
      plotOutput("distPlot")
    )
  )
)

# Define server logic for the histogram
server <- function(input, output) {
  output$distPlot <- renderPlot({
    # Draw the histogram using the 'waiting' column of the faithful dataset
    x <- faithful$waiting  
    bins <- seq(min(x), max(x), length.out = input$bins + 1)
    hist(x, breaks = bins,
         col = "#75AADB", border = "white",
         xlab = "Waiting time to next eruption (mins)",
         main = "Histogram of Old Faithful waiting times")
  })
}

# Combine the UI and server into an app and run it
shinyApp(ui = ui, server = server)
```

A quick explanation of the above code: It’s the classic Shiny example where the user can adjust the number of bins for a histogram. We use `faithful$waiting` (the Old Faithful geyser dataset built into R) as the data. The app has a slider input for the number of bins and displays a histogram that updates as the slider changes. If you save this code as `app.R` and run `shiny::runApp("app.R")`, you should see the app running locally.

Now that we have our Shiny app ready in a directory (say, we saved `app.R` in a folder called `myapp/`), we’re ready to containerize it with {shiny2docker}.  

### Step 2: Generate a Dockerfile with {shiny2docker}

The {shiny2docker} package will inspect our application and produce a Dockerfile (and a corresponding `.dockerignore`). Make sure your working directory is set to the folder containing the Shiny app (for example, set it to the `myapp` directory we created).

In an R session, run the following:

```
# In the R console, with working directory set to the Shiny app folder (e.g., "myapp")
library(shiny2docker)

# Generate a Dockerfile for the Shiny app in the current directory
shiny2docker(path = ".")
```

After running `shiny2docker(path = ".")`, you should see some messages in R indicating that a Dockerfile is being created. By default, this will create two files in your app directory:

* **Dockerfile** – a text file with instructions to build an image for your Shiny app.
* **.dockerignore** – a text file specifying files to exclude from the Docker build context (common entries might include `.Rproj.user`, `renv/library/`, etc., which {shiny2docker} populates automatically).

Let’s break down what {shiny2docker} does for us when generating the Dockerfile:

* It checks for an existing `renv.lock` in the directory. If it finds one (meaning you already used {renv} to lock dependencies), it will use that. If not, **{shiny2docker} will create a lockfile for you** by analyzing your app’s `library()` calls to figure out which packages (and versions) are needed. In our simple app, the main package dependency is **shiny** itself (plus any default R packages like datasets). The lockfile ensures the container installs the same version of **shiny** (and any other packages) that you used locally.

* It writes a Dockerfile that typically uses a base Docker image suitable for Shiny. In many cases, this will be the [**rocker/geospatial**](https://hub.docker.com/r/rocker/geospatial) image, which comes with R,Shiny and lot of stuff pre-installed. Using a pre-made base image saves us from manually setting up R and Shiny in the Dockerfile. The Dockerfile will `FROM` a specific version of rocker/geospatial (for example, `rocker/geospatial:4.2.2` if you’re using R 4.2.2).

* The Dockerfile will then include instructions to install system libraries and R packages. Thanks to the lockfile, {shiny2docker} knows exactly which R packages (and versions) to install in the container. Typically, the Dockerfile will do something like:

  * Install **renv** in the container (so it can use it to restore packages).
  * Copy the `renv.lock` file (and possibly your R project files) into the container.
  * Run `renv::restore()` to install all the required R packages at the locked versions.
  * Copy your Shiny app files into the container (e.g., the `app.R` or `ui.R`/`server.R` and any other app resources).
  * Set appropriate file permissions and environment variables for the Shiny app (if needed).
* Finally, the Dockerfile will specify a command to launch the Shiny app when the container runs. Usually, this is done by calling `shiny::runApp()` or by using the Shiny server default command. For example, it might use an entrypoint that runs something like:

  ```
  R -e "shiny::runApp('/srv/shiny-server', port=3838, host='0.0.0.0')"
  ```

  To peek at the generated Dockerfile, open it in a text editor. It should look somewhat like this (your exact file may differ in minor details):

  ```
  FROM rocker/geospatial:4.4.2
  RUN apt-get update -y && apt-get install -y  make zlib1g-dev git && rm -rf /var/lib/apt/lists/*
  RUN mkdir -p /usr/local/lib/R/etc/ /usr/lib/R/etc/
  RUN echo "options(renv.config.pak.enabled = FALSE, repos = c(CRAN = 'https://cran.rstudio.com/'), download.file.method = 'libcurl', Ncpus = 4)" | tee /usr/local/lib/R/etc/Rprofile.site | tee /usr/lib/R/etc/Rprofile.site
  RUN R -e 'install.packages("remotes")'
  RUN R -e 'remotes::install_version("renv", version = "1.0.3")'
  COPY renv.lock renv.lock
  RUN --mount=type=cache,id=renv-cache,target=/root/.cache/R/renv R -e 'renv::restore()'
  WORKDIR /srv/shiny-server/
  COPY . /srv/shiny-server/
  EXPOSE 3838
  CMD R -e 'shiny::runApp("/srv/shiny-server",host="0.0.0.0",port=3838)'
  ```

  The above is an **illustrative example** of what the Dockerfile might contain. The key points are that it uses the `rocker/geospatial` base image (matching your R version), copies the lockfile and app, installs packages via `renv::restore()`, and sets the container to run a Shiny Server that serves the app. Notice that port 3838 is exposed – this is the default port where Shiny Server serves apps.

At this stage, without writing any Docker instructions ourselves, we have a Dockerfile ready to go. Now let’s use it to build a Docker image.  

### Step 3: Build the Docker Image

With the Dockerfile in place, the next step is to build the Docker image for your Shiny app. This step is done in the terminal (not in R). Open a terminal (or use RStudio’s Terminal tab) and navigate to the directory containing the Dockerfile (our `myapp` folder). Then run:

```
# Make sure you are in the directory with the Dockerfile
# Replace 'myshinyapp' with the name you want for your Docker image.
docker build -t myshinyapp .
```

Let’s break down this command:

* `docker build` is the command to build a Docker image.
* `-t myshinyapp` tags the image with the name “myshinyapp” (you can choose any name; this will be the reference you use to run the container).
* `.` (dot) at the end tells Docker to use the current directory as the build context, looking for the Dockerfile there.

Docker will then step through the instructions in the Dockerfile. The first time you run this, it may download the base image (rocker/geospatial), which can be a few hundred MB. Then it will install R packages as needed. This process can take a while, especially if your app has many packages, but for our simple app (which just needs base R and shiny) it should be fairly quick.

Keep an eye on the output in the terminal. You should see Docker downloading layers, installing packages, etc., and hopefully finishing with a message indicating it built the image and tagged it as “myshinyapp:latest”. If something goes wrong (e.g., a package failing to install), the error logs will appear. In our case, it should build successfully.

**Tip:** If you change your app or add packages and want to rebuild, you might need to run `docker build` again. Docker cache will help speed up rebuilds (unchanged steps are cached), but if you add new R packages, the `renv::restore()` step will install the new ones on rebuild.  

### Step 4: Run the Shiny App in a Docker Container

Now that the image is built, you’re ready to run your Shiny app in a container. Running the container will start a Shiny Server that hosts your app. Use the following `docker run` command:

```
docker run -d -p 80:3838 --name myshinyapp_container myshinyapp
```

Here’s what each part means:

* `docker run` is the command to start a new container from an image.
* `-d` runs the container in “detached” mode (in the background). You can omit `-d` if you want to run it in the foreground to see logs, but then you’ll need a separate terminal.
* `-p 80:3838` maps port 3838 inside the container to port 80 on your local machine. This is crucial: the Shiny app is served on container port 3838, and this option exposes it so you can access it via `http://localhost:80` in your web browser.
* `--name myshinyapp_container` gives the container a friendly name (optional but useful for managing containers). We chose “myshinyapp_container” here.
* `myshinyapp` at the end is the name of the image to run (the one we built and tagged in Step 3).

After running this, Docker will start the container. You can check that it’s running by executing `docker ps` (which should list the running container). Now open your web browser and go to **[http://localhost:80](http://localhost/)**. You should see your Shiny histogram app running, just as it did locally! 🎉 Adjust the slider and the histogram will update, but now note that it’s running inside a Docker container.

If you don’t see anything or get an error, a few things to check: – Ensure Docker is actually running and that the container is up (`docker ps` should show it). – If you’re on a remote server, make sure to visit the correct host or have the port forwarded. The above assumes local development. – Check container logs with `docker logs myshinyapp_container` to see if the Shiny app or server printed any error messages during startup.

At this point, you’ve successfully containerized a Shiny app using {shiny2docker}. The app is running in an isolated environment with all its dependencies managed by renv and installed in the container.

When you’re done, you can stop the container with `docker stop myshinyapp_container` and remove it with `docker rm myshinyapp_container`. The Docker image “myshinyapp” will remain on your system (viewable with `docker images`), and you can re-run it anytime or push it to a container registry if you want to deploy it elsewhere.  

## Tips for a Smooth Containerization Process

Containerizing apps is much easier with {shiny2docker}, but here are some additional tips and best practices to consider:

* **Keep Your App Directory Clean:** Since {shiny2docker} will package everything in your app folder (except what’s excluded by `.dockerignore`), make sure you don’t have large unused files in there. Use the generated `.dockerignore` to exclude things like local datasets, caches, or R project settings that aren’t needed in the container.

* **Use {renv} for Dependency Management:** Even though {shiny2docker} can create a lockfile for you, it’s a good practice to use {renv} in your Shiny project from the start. Using {renv} while developing (call `renv::init()` and regularly `renv::snapshot()`) means you’re explicitly tracking package versions. This not only helps {shiny2docker} but also documents your environment for anyone collaborating on your project.

* **Test Locally Before CI/CD:** If you plan to integrate with CI/CD (using the provided `set_gitlab_ci()` or `set_github_action()` functions), test building and running your Docker image locally first (as we did above). This helps catch any issues early. Once it works locally, you can confidently add the CI configuration and let your CI pipeline build the image on each

* **Security Considerations:** When your Shiny app is running in Docker, treat the container as you would a server. Exposing port 3838 is fine for development, but in production you might put a proxy in front or use authentication if needed. Also, avoid including any sensitive credentials in the image. If your app needs API keys or passwords, use environment variables or external configuration rather than hard-coding them in the app or Dockerfile.

* **Learn Docker Basics:** While {shiny2docker} abstracts away Dockerfile details, having a basic understanding of Docker is beneficial. Knowing how to build, run, stop containers, and how Docker layers work will help you troubleshoot and optimize your containerized apps. The Dockerfile generated is a great learning resource – read through it to see how R packages are installed and how the app is launched. Over time, you might customize it (using the returned `dockerfiler` object from `shiny2docker()` if needed).

## Conclusion

Containerizing a Shiny application might seem intimidating at first, especially if you’re not familiar with Docker. The {shiny2docker} package bridges the gap between Shiny developers and DevOps, providing an easy way to create Docker images for Shiny apps without writing Dockerfiles manually. In our example, we saw how a simple Shiny app can be containerized in just a few steps: generate a Dockerfile, build the image, and run a container. The result is a portable app that runs the same everywhere, which is incredibly useful for deployment and sharing.

Both Shiny developers and DevOps engineers can appreciate this workflow: developers don’t need to become Docker experts to deploy their apps, and DevOps professionals get a reproducible environment defined by code. With containerization, scaling and managing Shiny apps in production (using tools like Kubernetes, ShinyProxy, or cloud services) becomes much more manageable, since each app is encapsulated in its own image.

We encourage you to try {shiny2docker} on one of your own Shiny projects. You’ll save time and avoid the pitfalls of environment configuration. For further reading, check out the **{shiny2docker}** documentation and vignette for more advanced usage, including how to customize the Dockerfile or integrate CI/CD pipelines. Happy containerizing!

This post is better presented on its original ThinkR website here: [Containerizing Shiny Apps with {shiny2docker}: A Step-by-Step Guide](https://rtask.thinkr.fr/containerizing-shiny-apps-with-shiny2docker-a-step-by-step-guide/)
