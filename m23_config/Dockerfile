FROM mike/debian_config
MAINTAINER chandlerbing7@gmail.com

ARG SHOP_VERSION=2.3.4
ARG BASE_URL="<URL-STRING>"

RUN composer create-project --repository=https://repo.magento.com/ magento/project-community-edition /var/www/html/magento2ce/ ${SHOP_VERSION} --prefer-dist
#RUN composer create-project --repository=https://repo.magento.com/ magento/project-enterprise-edition /var/www/html/magento2ce/
RUN mkdir /var/www/html/magento2ce/var/composer_home/
RUN touch /var/www/html/magento2ce/var/composer_home/auth.json
RUN cp /root/.composer/auth.json /var/www/html/magento2ce/var/composer_home/auth.json

# Magento2 and Sample Data
RUN cd /var/www/html/magento2ce/ && \
	composer install && \
	php bin/magento sampledata:deploy && \
	bin/magento setup:upgrade ; \
	mv Gruntfile.js.sample Gruntfile.js && \
	mv package.json.sample package.json
RUN /etc/init.d/mysql start && \
	cd /var/www/html/magento2ce/ &&  \
	php bin/magento setup:install --base-url="${BASE_URL}" \
	--admin-firstname=admin --admin-lastname=admin --backend-frontname=admin \
	--admin-email=chandlerbing7@gmail.com --admin-user=admin \
	--admin-password=asdasd123 --db-name=m2 --db-host=localhost --db-user=root \
	--use-rewrites=1 --admin-use-security-key=0 ; \
	php bin/magento index:reindex ; \
	php bin/magento c:c ; \
	apt install -y nodejs && \
	npm install -g grunt-cli && \
	npm install && \
	grunt exec && \
	grunt less --force

RUN chown -R www-data:www-data /var/www/html/magento2ce/

RUN apt --purge autoremove -y ; apt clean

ENTRYPOINT 	/etc/init.d/mysql start && service sendmail restart && \
			service apache2 restart && bash

RUN service cron start

EXPOSE 80