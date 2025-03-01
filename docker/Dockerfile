FROM debian:testing

ARG APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=1
RUN apt-get update \
    && apt-get install -y --no-install-recommends apt-transport-https dirmngr gnupg ca-certificates \
    && apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF \
    && echo "deb https://download.mono-project.com/repo/debian stable-buster main" > /etc/apt/sources.list.d/mono-official-stable.list \
    && apt-get update \
    && apt-get install -y wget xz-utils build-essential libtool-bin cmake git curl vim powercap-utils \
    libgmp-dev libssl-dev libssl1.1 libxml2 lbzip2 libtinfo5 libtool zlib1g \
    libpcre3-dev libevent-dev libjson-c-dev libblas-dev liblapack-dev \
    libopenblas-dev libnuma-dev \
    libjq-dev libonig-dev libjson-glib-dev \
    python3-numpy python3 python3-ujson python3-pip \
    mono-devel nuget \
    chezscheme gcc-11 gccgo-11 gdc-11 valac cpanminus \
    lua5.4 liblua5.4-dev opam \
    tcl tcllib php elixir clang-13 libc++-13-dev libc++abi-13-dev libomp-13-dev

RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 10 \
	&& update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 10 \
	&& update-alternatives --install /usr/bin/gdc gdc /usr/bin/gdc-11 10 \
	&& update-alternatives --install /usr/bin/gccgo gccgo /usr/bin/gccgo-11 10 \
        && update-alternatives --install /usr/bin/clang clang /usr/bin/clang-13 10 \
        && update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-13 10 \
        && rm -rf /var/lib/apt/lists/* /tmp/*

# Requires for https://crates.io/crates/jq-sys in Debian
ENV JQ_LIB_DIR=/usr/lib/x86_64-linux-gnu/

WORKDIR /opt

RUN curl -Lo /usr/local/bin/coursier https://git.io/coursier-cli \
	&& chmod +x /usr/local/bin/coursier

# https://github.com/MLton/mlton/releases
ARG MLTON=20210117
RUN wget --progress=dot:giga -O - \
	https://github.com/MLton/mlton/releases/download/on-$MLTON-release/mlton-$MLTON-1.amd64-linux-glibc2.31.tgz \
	| tar -xz
ENV PATH="/opt/mlton-$MLTON-1.amd64-linux-glibc2.31/bin/:${PATH}"

# https://www.ruby-lang.org/en/downloads/
RUN export VERSION=ruby-3.1.0 \
	&& wget --progress=dot:giga -O - \
	https://cache.ruby-lang.org/pub/ruby/3.1/$VERSION.tar.gz \
	| tar -xz \
	&& cd $VERSION \
	&& ./configure --prefix=/opt/ruby && make -j && make install \
	&& cd .. && rm -rf $VERSION
ENV PATH="/opt/ruby/bin:${PATH}"

# https://pypy.org/download.html
ARG PYPY=pypy3.8-v7.3.7-linux64
RUN wget --progress=dot:giga -O - \
    https://downloads.python.org/pypy/$PYPY.tar.bz2 \
    | tar -xj \
    && rm /opt/$PYPY/bin/python*
ENV PATH="/opt/$PYPY/bin:${PATH}"

# https://www.scala-lang.org/download/
ARG SCALA=3.1.1
RUN wget --progress=dot:giga -O - \
    https://github.com/lampepfl/dotty/releases/download/$SCALA/scala3-$SCALA.tar.gz \
    | tar -xz
ENV PATH="/opt/scala3-$SCALA/bin/:${PATH}"

# https://jdk.java.net/
ARG JDK=17.0.2
RUN wget --progress=dot:giga -O - \
    https://download.java.net/java/GA/jdk17.0.2/dfd4a8d0985749f896bed50d7138ee7f/8/GPL/openjdk-${JDK}_linux-x64_bin.tar.gz \
    | tar -xz
ENV PATH="/opt/jdk-$JDK/bin:${PATH}"

# https://github.com/crystal-lang/crystal/releases
ARG CRYSTAL=crystal-1.3.2-1
RUN wget --progress=dot:giga -O - \
	https://github.com/crystal-lang/crystal/releases/download/1.3.2/$CRYSTAL-linux-x86_64.tar.gz \
	| tar -xz
ENV PATH="/opt/$CRYSTAL/bin:${PATH}"

# https://github.com/ldc-developers/ldc/releases
ARG LDC=ldc2-1.28.1-linux-x86_64
RUN wget --progress=dot:giga -O - \
	https://github.com/ldc-developers/ldc/releases/download/v1.28.1/$LDC.tar.xz \
	| tar -xJ
ENV PATH="/opt/$LDC/bin/:${PATH}"

# https://nim-lang.org/install_unix.html
ARG NIM=nim-1.6.4
RUN wget --progress=dot:giga -O - \
	https://nim-lang.org/download/$NIM-linux_x64.tar.xz \
	| tar -xJ
ENV PATH="/opt/$NIM/bin/:${PATH}"

# https://dlang.org/download.html
RUN wget --progress=dot:giga -O - \
    https://s3.us-west-2.amazonaws.com/downloads.dlang.org/releases/2021/dmd.2.098.1.linux.tar.xz \
    | tar -xJ
ENV PATH="/opt/dmd2/linux/bin64/:${PATH}"

# https://www.jruby.org/download
ARG JRUBY=9.3.3.0
RUN wget --progress=dot:giga -O - \
	https://repo1.maven.org/maven2/org/jruby/jruby-dist/$JRUBY/jruby-dist-$JRUBY-bin.tar.gz \
	| tar -xz \
	&& ln -s /opt/jruby-$JRUBY/bin/jruby /usr/bin/jruby

# https://julialang.org/downloads/
ARG JULIA=julia-1.7.2
RUN wget --progress=dot:giga -O - \
	https://julialang-s3.julialang.org/bin/linux/x64/1.7/$JULIA-linux-x86_64.tar.gz \
	| tar -xz
ENV PATH="/opt/$JULIA/bin/:${PATH}"

# https://swift.org/download/
ARG SWIFT=swift-5.5.3-RELEASE-ubuntu20.04
RUN wget --progress=dot:giga -O - \
	https://swift.org/builds/swift-5.5.3-release/ubuntu2004/swift-5.5.3-RELEASE/$SWIFT.tar.gz \
	| tar -xz \
	&& ln -s /opt/$SWIFT/usr/bin/swift /usr/bin/swift \
    && ln -s /opt/$SWIFT/usr/bin/swiftc /usr/bin/swiftc
ENV LD_LIBRARY_PATH=/opt/$SWIFT/usr/lib/swift/linux:$LD_LIBRARY_PATH

# https://clojure.org/community/downloads
ARG CLOJURE=1.10.3.1075
RUN wget --progress=dot:giga -O - \
	https://download.clojure.org/install/linux-install-$CLOJURE.sh \
	| bash -s

ENV GHCUP_INSTALL_BASE_PREFIX="/opt"
RUN curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org \
	| BOOTSTRAP_HASKELL_NONINTERACTIVE="y" sh
ENV PATH="/opt/.ghcup/bin/:${PATH}"

# https://www.haskell.org/ghc/
# 9.2.1 is not compatible with hlint and other hackages yet
# https://hackage.haskell.org/package/hlint
ARG GHC_VER=9.0.1
RUN ghcup install ghc $GHC_VER && ghcup set ghc $GHC_VER

# Shared packages for all Haskell code
RUN cabal update \
        && cabal install network raw-strings-qq --lib \
        && cabal install hlint
ENV GHC_PACKAGE_PATH="~/.cabal/store/ghc-${GHC_VER}/package.db:"

# https://github.com/graalvm/graalvm-ce-builds/releases
ARG GRAALVM=22.1.0
RUN wget --progress=dot:giga -O - \
	https://github.com/graalvm/graalvm-ce-builds/releases/download/vm-$GRAALVM/graalvm-ce-java17-linux-amd64-$GRAALVM.tar.gz

ARG GRAALVM=22.1.0
RUN tar -x graalvm-ce-java17-linux-amd64-$GRAALVM.tar.gz && ln -s /opt/graalvm-ce-java17-22.1.0/bin/gu /usr/bin/gu

RUN gu install ruby \
	&& /opt/graalvm-ce-java17-$GRAALVM/languages/ruby/lib/truffle/post_install_hook.sh \
	&& ln -s /opt/graalvm-ce-java17-$GRAALVM/bin/truffleruby /usr/bin/truffleruby

# https://github.com/dotnet/core/tree/master/release-notes
RUN mkdir dotnet && wget --progress=dot:giga -O - \
    https://download.visualstudio.microsoft.com/download/pr/e7acb87d-ab08-4620-9050-b3e80f688d36/e93bbadc19b12f81e3a6761719f28b47/dotnet-sdk-6.0.102-linux-x64.tar.gz \
    | tar -xz -C dotnet
ENV PATH="/opt/dotnet:${PATH}"
ENV DOTNET_ROOT="/opt/dotnet"
ENV DOTNET_CLI_TELEMETRY_OPTOUT=1

# https://nodejs.org/en/download/current/
ARG NODE=v17.5.0
RUN wget --progress=dot:giga -O - \
	https://nodejs.org/dist/$NODE/node-$NODE-linux-x64.tar.xz \
	| tar -xJ
ENV PATH="/opt/node-$NODE-linux-x64/bin/:${PATH}"

# https://golang.org/dl/
RUN wget --progress=dot:giga -O - \
    https://go.dev/dl/go1.17.7.linux-amd64.tar.gz \
    | tar -xz
ENV PATH="/opt/go/bin/:${PATH}"

# https://www.rust-lang.org/tools/install
ENV CARGO_HOME="/opt/.cargo" PATH="/opt/.cargo/bin:${PATH}"
RUN wget -O - https://sh.rustup.rs | sh -s -- -y

# https://ocaml.org/releases/
ARG OCAML=4.13.1
RUN opam init --disable-sandboxing -n --root=/opt/opam --compiler=ocaml-base-compiler.$OCAML
ENV PATH="/opt/opam/$OCAML/bin/:${PATH}"

# https://kotlinlang.org/docs/command-line.html
ARG KOTLIN=1.6.10
RUN wget --progress=dot:giga \
	https://github.com/JetBrains/kotlin/releases/download/v$KOTLIN/kotlin-compiler-$KOTLIN.zip \
	&& unzip kotlin-compiler-$KOTLIN.zip \
	&& rm kotlin-compiler-$KOTLIN.zip
ENV PATH="/opt/kotlinc/bin/:${PATH}"

# https://download.racket-lang.org/
ARG RACKET=8.4
RUN wget --progress=dot:giga \
    https://www.cs.utah.edu/plt/installers/$RACKET/racket-$RACKET-x86_64-linux-cs.sh \
    && sh racket-$RACKET-x86_64-linux-cs.sh --unix-style --dest /usr/ \
    && rm racket-$RACKET-x86_64-linux-cs.sh

# https://github.com/vlang/v/releases
ARG VLANG=weekly.2022.07
RUN wget --progress=dot:giga \
        https://github.com/vlang/v/releases/download/$VLANG/v_linux.zip \
        && unzip v_linux.zip \
        && rm v_linux.zip
ENV PATH="/opt/v/:${PATH}"

# https://ziglang.org/download
ARG ZIG=zig-linux-x86_64-0.9.0
RUN wget --progress=dot:giga -O - \
    https://ziglang.org/download/0.9.0/$ZIG.tar.xz \
    | tar -xJ
ENV PATH="/opt/$ZIG/:${PATH}"

# Using more stable LuaJIT, used in Alpine and other distros
# https://github.com/openresty/luajit2/tags
RUN export VERSION=2.1-20220111 \
    && wget --progress=dot:giga -O - \
    https://github.com/openresty/luajit2/archive/refs/tags/v$VERSION.tar.gz \
    | tar -xz \
    && cd luajit2-$VERSION \
    && make -j && make install PREFIX=/opt/luajit \
    && cd .. && rm -rf luajit2-$VERSION
ENV PATH="/opt/luajit/bin:${PATH}"

ARG SCRIPTS_VER=unknown
COPY *.rb ./

RUN ./versions.rb

ENTRYPOINT ["./run.rb"]

RUN cat /etc/os-release
