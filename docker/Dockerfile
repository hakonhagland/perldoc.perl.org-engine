FROM debian:stable AS osbase
LABEL maintainer OpusVL <support@opusvl.com>

# Become god
USER root
WORKDIR /root

# Add in ssh configuration for use with pushing git repos
RUN mkdir /toor
COPY ssh /root/.ssh
COPY ssh /toor/.ssh

# Add in essentials for this project
RUN apt-get update \
    && apt-get install -y build-essential   \
        bison                               \
        byacc                               \
        unzip                               \
        git                                 \
        cpanminus                           \
        libssl-dev                          \
        zlib1g-dev

# Fix permissions and configure git
RUN cd /root \
    && chown -R root .ssh \
    && chmod -R 700 .ssh \
    && echo "Host github.com" >> .ssh/config \
    && echo "\tStrictHostKeyChecking no" >> .ssh/config 

# Setup for using CPAN
ENV PERL_MM_USE_DEFAULT=1

RUN cpanm Net::SSLeay || cat /root/.cpanm/work/*/build.log

# Install base libraries
RUN cpanm inc::latest       \
    LWP::Protocol::https    \
    App::cpanminus

# Add in a few missing deps
RUN cpanm Getopt::Long      \   
    JSON                    \
    JSON::MaybeXS           \
    Data::Dumper            \
    LWP::UserAgent          \
    HTML::TreeBuilder       \
    File::Path              \
    Crypt::Digest::SHA256   \
    Archive::Tar            \
    Capture::Tiny           \
    Try::Tiny               \
    File::Find              \
    Template                \
    JSON::MaybeXS           \
    Devel::PatchPerl@1.56   \
    Perl::Tidy@20101217     \
    File::Copy::Recursive

COPY dumb-init_* /dumbinit
COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh
RUN chmod +x /dumbinit 

RUN cp -Rvp /root/. /toor

# Kickstart the script into action
ENTRYPOINT [ "/dumbinit", "--", "/entrypoint.sh" ]
