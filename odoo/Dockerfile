# Latest odoo system requirements
FROM thelemmon/odoo-system-base:latest

# Define odoo args to build the image 
ARG GIT_BRANCH
ARG GITHUB_TOKEN
ARG ODOO_VERSION
ARG DEFAULT_DEPTH

# Install Odoo
RUN set -x; \
        curl -o odoo.deb -sSL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.latest_all.deb \
        && dpkg --force-depends -i odoo.deb \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* odoo.deb

# SET DEFAULT DIR
WORKDIR /home/odoo/

# Install custom python requirements
COPY requirements.txt ./requirements.txt
RUN pip3 install -r ./requirements.txt

# Expose Odoo services
# Web service - Longpolling - XML-RPC
EXPOSE 8069 8071 8072

# Reeemplace wait-for-psql.py
COPY wait-for-psql.py /usr/local/bin/wait-for-psql.py
RUN chmod -R 755 /usr/local/bin/wait-for-psql.py

# Clone addons and addons-external
COPY ./repositories.yml ./repositories.yml
RUN gitaggregate -c repositories.yml --expand-env

VOLUME ["/var/lib/odoo"]

# Copy Odoorc and set as default config file
COPY ./entrypoint.sh bin/
RUN chmod +x bin/entrypoint.sh

COPY .odoorc /home/odoo.conf
ENV ODOO_RC /home/odoo.conf

ENTRYPOINT ["bin/entrypoint.sh"]
CMD ["odoo"]