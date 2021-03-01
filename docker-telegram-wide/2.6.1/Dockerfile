FROM nubs/arch-build as buildstage

USER root

ADD mirrorlist /etc/pacman.d/mirrorlist
RUN sed -i "/\[multilib\]/,/Include/"'s/^#//' /etc/pacman.conf
RUN pacman -Syyu --noconfirm
RUN pacman --sync --noconfirm cmake ninja range-v3 tl-expected kwayland libtg_owt python-commonmark ffmpeg microsoft-gsl hunspell minizip openal gtk3

RUN curl -o rlottie.tar.gz https://aur.archlinux.org/cgit/aur.git/snapshot/rlottie-tdesktop.tar.gz
RUN tar -xvzf rlottie.tar.gz && chown -R build:build rlottie-tdesktop
WORKDIR rlottie-tdesktop
USER build
RUN makepkg
USER root
RUN pacman -U --noconfirm rlot*

RUN curl -o td.tar.gz https://aur.archlinux.org/cgit/aur.git/snapshot/telegram-desktop-wide.tar.gz
RUN tar -xvzf td.tar.gz && chown -R build:build telegram-desktop-wide
WORKDIR telegram-desktop-wide
USER build
RUN makepkg -d
USER root
RUN mv ./tele*zst /tele.zst

FROM scratch AS export
COPY --from=buildstage /tele.zst .
