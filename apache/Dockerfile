FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install Apache2 and ModSecurity
RUN apt-get update && \
    apt-get install -y \
    apache2 \
    libapache2-mod-security2 \
    wget \
    unzip \
    curl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Enable required modules
RUN a2enmod security2

# Setup ModSecurity
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf

# Install OWASP CRS 3.3.0
RUN mkdir -p /etc/modsecurity/crs && \
    cd /etc/modsecurity/crs && \
    wget -q https://github.com/coreruleset/coreruleset/archive/refs/tags/v3.3.0.zip && \
    unzip -q v3.3.0.zip && \
    mv coreruleset-3.3.0/* . && \
    rm -rf coreruleset-3.3.0 v3.3.0.zip

# Create test page
RUN echo "<html><body><h1>Apache WAF is Working!</h1><p>ModSecurity + OWASP CRS is active</p></body></html>" > /var/www/html/index.html

# Copy only necessary configs (NO crs-setup.conf)
COPY modsecurity.conf /etc/modsecurity/modsecurity.conf
COPY vhost.conf /etc/apache2/sites-available/000-default.conf

# Enable site
RUN a2ensite 000-default.conf

EXPOSE 80

CMD ["apache2ctl", "-D", "FOREGROUND"]

