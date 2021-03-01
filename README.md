This repo contains Dockerfile, which creates a container based on "nubs/arch-build" repo and builds telegram-desktop (by utilizing PKGBUILDS, AUR and makepkg) to be installed later on.

### telegram-builders

The reason behind Docker builds for Telegram is that we want to get a reproducable build of Telegram codebase (with patches as well) that isn't dependent on libraries installed in the host system. 

In other words, I've experienced sometimes conditions when installed libraries and software on host machines, such as with Manjaro, for example, we are unable to build telegram from the same PKGBUILD. But when we take same PKGBUILD to a clean and fresh ArchLinux install, it works perfectly.

So I decided to just continue with Docker containers as they allow us to easily create reproducible containers and use them to build specialized Telegram versions.

### What is in this repo?
* Currently, there's only 1 telegram version and 1 linux target to build.
* In the future, this might be expanded to more telegram versions, as well as other linux targets, if needed or desired.

### Is this vanilla telegram?
* Yes and no. The vanilla telegram is taken, then two patches are applied (in the telegram-desktop-wide variety): one for "wide chat ballons" and another one for "custom fonts". The applied scripts are taken from according projects without any modifications. Then resulting codebase is built.

### How things do work here?
* We take "nubs/arch-build" Docker Hub image as basis
* We utilize PKGBUILD file (downloading it from AUR) to build telegram-desktop
* We export result image by using docker layers

### Which versions are here?
Currently, for telegram it's only 2.6.1. Later it might be updated.

### What is required to run this?
Only (recent) Docker installed in the system and we also need to activate DOCKER_BUILDKIT functionality to export the results. To install the package, we then use normal pacman.
* ArchLinux / Manjaro / Other Arch-based Linuxes
* Docker with Docker-buildkit setting on TRUE
* Pacman installed
* Normal linux CLI available

### Where will be resulting package?
In a folder "out", as file "tele.zst".

### Ok well... I want to use it... How?
1) Clone repo: `git clone https://github.com/sxiii/telegram-builders`
2) Enter the folder: `cd telegram-builders/docker-telegram-wide/2.6.1/` (or other in the future)
3) Run the docker build with buildkit function and output directory as "out": `DOCKER_BUILDKIT=1 docker build -t tdw . --output out`
4) Wait for everything to build... Grab a coffee. Takes awhile.
5) Install the resulting package: `sudo pacman -U out/tele.zst`
6) Run telegram-desktop from your system menu. Enjoy full-width telegram with latest version!

### What is the build basis of this?
1) Dockerfile is in the repo itself
2) Inside of the Dockerfile, we build by using "nubs/arch-build" Docker Hub image as well as my own PKGBUILD script from here: https://aur.archlinux.org/pkgbase/telegram-desktop-wide/

### Can I add my own build scripts here or improve the project?
Sure! It's created for everybody. Feel free to create pull requests as well as issues in this repo.

### I don't want to build telegram, can I have binary plz?
Sure! Just take the package from the container (github packages). Or download, export and install it just like that. Save this as Dockerfile:
```code=Dockerfile
FROM docker.pkg.github.com/sxiii/telegram-builders/tdw:2.6.1 as tdw
FROM scratch AS export
COPY --from=tdw /tele.zst .
```
And run this: `DOCKER_BUILDKIT=1 sudo docker build . --output=out && sudo pacman -U ./out/tele.zst`
Now your telegram is installed to your Arch/Manjaro. ;) Enjoy
