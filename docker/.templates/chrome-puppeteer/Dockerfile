# https://www.ubuntuupdates.org/package/google_chrome/stable/main/base/google-chrome-stable
ARG CHROME_VERSION="110.0.5481.177-1"
ARG CHROME_DISTRIBUTIVE="google-chrome-stable_${CHROME_VERSION}_amd64.deb"

# We use Chrome instead of puppeteer's Chromium
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD true
ENV PUPPETEER_SKIP_DOWNLOAD true

# for https
RUN apt-get install -yyq ca-certificates

# tools
RUN apt-get install -yyq gconf-service lsb-release wget xdg-utils

# fonts
RUN apt-get install -yyq fonts-liberation

RUN wget https://dl.google.com/linux/chrome/deb/pool/main/g/google-chrome-stable/${CHROME_DISTRIBUTIVE} && \
        apt-get install -y ./${CHROME_DISTRIBUTIVE} && \
        rm -f ${CHROME_DISTRIBUTIVE}

RUN groupadd -r chrome && useradd -r -g chrome -G audio,video chrome \
    && mkdir -p /home/chrome/Downloads && chown -R chrome:chrome /home/chrome

# Install node.js
RUN rm -rf /var/lib/apt/lists/ && curl -sL https://deb.nodesource.com/setup_20.x | bash -
RUN apt-get install nodejs -y
RUN npm install npm@latest -g
