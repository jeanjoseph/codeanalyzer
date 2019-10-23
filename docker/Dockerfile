FROM circleci/node:8.9.4
maintainer MGMResorts <sapan.prajapati@publicissapient.com>

user root

####################### Install oracle jdk-8 ########################################

RUN wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz
RUN sudo mkdir -p /usr/local/java
RUN sudo tar xvzf jdk-8u131-linux-x64.tar.gz -C /usr/local/java
RUN sudo rm -r jdk-8u131-linux-x64.tar.gz


########################get maven 3.5.4 and verify its checksum #####################

RUN wget --no-verbose -O /tmp/apache-maven-3.5.4.tar.gz http://archive.apache.org/dist/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz; \
  echo "89eea39183139e5f8a0c1601d495b3b6 /tmp/apache-maven-3.5.4.tar.gz" | md5sum -c

############################install Maven ###########################################

RUN sudo tar xzf /tmp/apache-maven-3.5.4.tar.gz -C /opt/; \
  sudo ln -s /opt/apache-maven-3.5.4 /opt/maven; \
  sudo ln -s /opt/maven/bin/mvn /usr/local/bin; \
  sudo rm -f /tmp/apache-maven-3.5.4.tar.gz

############################### Set environment variables ###########################

ENV MAVEN_HOME /opt/maven
ENV JAVA_HOME /usr/local/java/jdk1.8.0_131
ENV SONAR_SCANNER_VERSION "3.3.0.1492"
ENV SONAR_SCANNER_HOME "/opt/sonar-scanner-${SONAR_SCANNER_VERSION}"
ENV PATH="${PATH}:${HOME}/bin:${JAVA_HOME}/bin:${SONAR_SCANNER_HOME}/bin"

######### Set Environment variable to fix Chromedriver hang issue ###################

ENV DBUS_SESSION_BUS_ADDRESS=/dev/null

################################# Install Sonar Scanner #############################

RUN wget -O ~/sonar-scanner.zip \
    "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}.zip" && \
    sudo unzip ~/sonar-scanner.zip -d /opt && \
    rm ~/sonar-scanner.zip

############################ install Pa11y-ci tool###################################

RUN apt-get update && apt-get install -y wget vim unzip --no-install-recommends \
    && wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list' \
    && apt-get update \
    && apt-get install -y google-chrome-stable --no-install-recommends \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge --auto-remove -y curl \
    && rm -rf /src/*.deb
	
########################### install chrome driver ###################################

#RUN wget -N http://chromedriver.storage.googleapis.com/2.41/chromedriver_linux64.zip -P ~/Downloads \
#    && unzip ~/Downloads/chromedriver_linux64.zip -d ~/Downloads \
#    && chmod +x ~/Downloads/chromedriver \
#    && sudo mv -f ~/Downloads/chromedriver /usr/local/share/chromedriver \
#    && sudo rm -r ~/Downloads/chromedriver_linux64.zip
    
#    && sudo ln -s /usr/local/share/chromedriver /usr/local/bin/chromedriver \
#    && sudo ln -s /usr/local/share/chromedriver /usr/bin/chromedriver
    
# sudo apt-get install libgconf-2-4

RUN sudo apt-get update
RUN sudo apt-get install curl
RUN sudo apt-get install nmap
RUN sudo apt-get install chromedriver

########################### install grunt ###########################################

RUN sudo apt-get install python-software-properties
RUN sudo curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
RUN sudo apt-get install nodejs
RUN sudo npm install -g grunt


################## Use dumb-init to prevent zombie chrome processes #################

ADD https://github.com/Yelp/dumb-init/releases/download/v1.2.0/dumb-init_1.2.0_amd64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init

################ Install puppeteer so it's available in the container ###############

RUN npm config set unsafe-perm true
RUN sudo npm install -g pa11y-ci
RUN npm config set unsafe-perm false

################ Install OpenConnect tool for VPN connection#########################
RUN apt-get update \
 && apt-get install curl vpnc-scripts build-essential gettext pkg-config libssl-dev libxml2-dev liblz4-dev libstoken1 stoken \
 && sudo curl -O ftp://ftp.infradead.org/pub/openconnect/openconnect-8.02.tar.gz \
 && tar -xvf openconnect-8.02.tar.gz \
 && openconnect-8.02/configure --without-gnutls --with-vpnc-script=/usr/share/vpnc-scripts/vpnc-script --without-openssl-version-check \
 && make \
 && make install \ 
 && ldconfig /usr/local/lib \
 && apt-get install iptables
######################## Akamai client installation ############################
RUN wget -O ~/akamai-1.1.4-linuxamd64 https://github.com/akamai/cli/releases/download/1.1.4/akamai-1.1.4-linuxamd64
RUN mv ~/akamai-1.1.4-linuxamd64 /usr/local/bin/akamai
RUN chmod +x /usr/local/bin/akamai

RUN sudo apt-get update
RUN wget https://dl.google.com/go/go1.13.linux-amd64.tar.gz
RUN tar -xvf go1.13.linux-amd64.tar.gz
RUN sudo mv go /usr/local

ENV GOPATH=/usr/local/go GOBIN=/usr/local/go/bin PATH=$PATH:/usr/local/go:/usr/local/go/bin
RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh

RUN akamai install purge
RUN akamai config set cli.enable-cli-statistics "false"
RUN akamai config set cli.last-upgrade-check "ignore"
################## Changing user to circleci ########################################
#user circleci
#####################################################################################
Add ./lvgp.mgmresorts.com.pem /
Add ./connect.sh /
Add ./vpnc-script /
ENTRYPOINT ["/connect.sh"]