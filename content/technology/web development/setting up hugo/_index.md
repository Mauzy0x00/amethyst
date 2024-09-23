# Setting Up Hugo on Ubuntu LTS server

# Intro
You don't need to read this part really :3 Just me blabing.
I wrote this as I installed and setup Hugo for the first time and documented issues I had along the way. Despite it being documentation for myself, this can also be used as a guide! I decided to use Hugo because I wanted to create a blog section for my website. I tried using Jekyll but I'm not a fan of Ruby. Also, Jekyll and its documentation felt dated compared to Hugo; which is written in Go. Being a fan of Rust I also looked at Zola; However Hugo felt like a better fit. At the end there is an install script.
- Note: The `$` denotes a terminal command. Anything before `$` denotes the current working directory.
# Always update && upgrade first!
`~$ sudo apt update && sudo apt upgrade -y`

# Install Hugo to Host
- If you wish do do development on this device, you will want to do the full installation of Hugo on the host for viewing quick updates. Otherwise, the standard version is all you need so you can initialize the Hugo directory for your website.
`~$ sudo apt install hugo`
**WARN! :** Apt installs version 0.92.2
- This is older than the required (at least) v0.112.0 of Hugo. It is annoying how many packages in apt are so old. I had this issue with Jekyll and NodeJS on other projects; creating many headaches. I will consider switching my server to Arch Linux in the future. For now I will remove Hugo and install and build directly from GitHub. 

## Remove Hugo
`~$ sudo apt autoremove hugo`

## Prerequisites
### Install Go
1. Download Go
	`~$ wget https://go.dev/dl/go1.23.1.linux-amd64.tar.gz`
- Remove any existing Go installations and expand the archive in `/usr/local/`. Un-taring the archive into an existing `/usr/local/go` tree is known to produce broken Go installations.
	`~$ sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.23.1.linux-amd64.tar.gz`
2. Add `/usr/local/go/bin/` to the PATH environment variable
	`~$ export PATH=$PATH:/usr/local/go/bin`
3. Verify installation 
	`~$ go version`
### Install Dart Sass
1. Download and expand the Linux-x64.tar release from GitHub
	`~$ wget https://github.com/sass/dart-sass/releases/download/1.78.0/dart-sass-1.78.0-linux-x64.tar.gz && sudo tar -C /usr/local -xzf dart-sass-1.78.0-linux-x64.tar.gz`
2. Add `/usr/local/dart-sass` to the PATH environment variable
	`~$ export PATH=$PATH:/usr/local/dart-sass`
3. Verify installation
	`~$ sass --version`
### Clean up!
`~$ rm go1.23.1.linux-amd64.tar.gz dart-sass-1.78.0-linux-x64.tar.gz`

## Build and install Hugo
**WARN! :** Change the Hugo build and install directory so that go isn't installed in `~/`

1. Build the extended edition:
	`~$ CGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@latest`
2. Add the `/go/bin` PATH environment variable. You can move this go directory somewhere else if you wish; Just be sure to correctly configure your environment variables now and in the future. 
	`~$ export PATH=$PATH:~/go/bin`
3. Verify installation 
	`~$ hugo version`

# Option 2: Create a Docker Container (recommended)
## Install Docker
## Create Dockerfile
## Install Hugo (any) on the Host
# Initialize the website!
1. Create directory structure in the `quickstart` directory and change into that directory
	`~$ hugo new site quickstart`
	`~$ cd quickstart`
2. Initialize a Git repo
	`~$ git init`
3. Set a theme. In this case, 'ananke'
	`~$ git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke`
	`$ echo "theme = 'ananke'" >> hugo.toml`
4. Start the development server to view the new website
	`~$ hugo server`
	- When running this there is a warning:
		- WARN  deprecated: resources.ToCSS was deprecated in Hugo v0.128.0 and will be removed in a future release. Use css.Sass instead.
		- I will be ignoring this for now
	- Yay! We can see something on `http://localhost:1313/` :]
	
# Add Content 
- Add a new page
	`~$ hugo new content content/posts/my-first-post.md`
	- This creates a new file in that directory 
	- Link to information about `draft = true` in the my-first-post.md file in regards to draft, future and expired content information:
		- https://gohugo.io/getting-started/usage/#draft-future-and-expired-content 
	- Add some markdown to the file and save 
## Development Server
- Start the development server
	`~/hugo-docker/hugo-site$ sudo ~/.go/bin/hugo server -D`
		or 
	`~/hugo-docker/hugo-site$ ~/.go/bin/hugo server --buildDrafts`
	Hugo is not installed for the root user and many files in the site directory are now owned by the current user. To work around this for development, we will just point to the file path of the Hugo binary and run it that way.
- Starting in development mode will allow you to see updates to your site as you make them and save http://localhost:1313/
## Live Server
1. Build the Docker container 
	`~/hugo-docker$ docker build -t hugo-test .`
2. Start the container 
	`~/hugo-docker$ sudo docker run -p 1313:1313 -v $(pwd)/hugo-site:/app hugo-test`
	
# Configure the Site
# Automated Install Script
- https://github.com/Mauzy0x00/Scripts/blob/main/installHugo.sh
# References
## Hugo
- Hugo GitHub repository
	- https://github.com/gohugoio/hugo?tab=readme-ov-file
- Hugo Quick Start Guide
	- https://gohugo.io/getting-started/quick-start/
- Hugo Documentation
	- https://gohugo.io/documentation/
- Hugo Themes
	- https://themes.gohugo.io/themes
## GoLang
- Download and install 
	- https://go.dev/doc/install
- How to Write Go Code (I didn't really read much of this)
	- https://go.dev/doc/code#Command
## Sass
- This is useless
	- https://sass-lang.com/install/
## Music I listened to while doing this
- ✦ rally house 5.0 ✦ \[house ⊹ techno ⊹ lofi mix]
	- https://www.youtube.com/watch?v=oqXXgf0mbe4