# Use jbossdemocentral/developer as the base
FROM jbossdemocentral/developer

# Maintainer details
MAINTAINER Andrew Block <andy.block@gmail.com>

# Environment Variables 
ENV BRMS_HOME /opt/jboss/brms/jboss-eap-6.4
ENV BRMS_VERSION_MAJOR 6
ENV BRMS_VERSION_MINOR 1
ENV BRMS_VERSION_MICRO 0
ENV BRMS_VERSION_BUILD GA

ENV EAP_VERSION_MAJOR 6
ENV EAP_VERSION_MINOR 4
ENV EAP_VERSION_MICRO 0

# ADD Installation Files
COPY support/installation-brms support/installation-eap support/installation-brms.variables support/installation-eap.variables installs/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_BUILD-installer.jar installs/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar  /opt/jboss/

# Update Permissions on Installers
USER root
RUN chown jboss:jboss /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar /opt/jboss/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_BUILD-installer.jar 
USER jboss

# Prepare and run installer and cleanup installation components
RUN sed -i "s:<installpath>.*</installpath>:<installpath>$BRMS_HOME</installpath>:" /opt/jboss/installation-eap \
    && sed -i "s:<installpath>.*</installpath>:<installpath>$BRMS_HOME</installpath>:" /opt/jboss/installation-brms \
	&& java -jar /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar  /opt/jboss/installation-eap -variablefile /opt/jboss/installation-eap.variables \
	&& java -jar /opt/jboss/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_BUILD-installer.jar  /opt/jboss/installation-brms -variablefile /opt/jboss/installation-brms.variables \
	&& rm -rf /opt/jboss/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_BUILD-installer.jar /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar /opt/jboss/installation-brms /opt/jboss/installation-brms.variables /opt/jboss/installation-eap /opt/jboss/installation-eap.variables $BRMS_HOME/standalone/configuration/standalone_xml_history/


# Add support files
COPY support/application-roles.properties support/standalone.xml $BRMS_HOME/standalone/configuration/
COPY support/userinfo.properties $BRMS_HOME/standalone/deployments/business-central.war/WEB-INF/classes/

# Swtich back to root user to perform cleanup
USER root

# Fix permissions on support files
RUN chown -R jboss:jboss $BRMS_HOME/standalone/configuration/standalone.xml $BRMS_HOME/standalone/deployments/business-central.war/WEB-INF/classes/userinfo.properties

# Run as JBoss 
USER jboss

# Expose Ports
EXPOSE 9990 9999 8080

# Run BRMS
CMD ["/opt/jboss/brms/jboss-eap-6.4/bin/standalone.sh","-c","standalone.xml","-b", "0.0.0.0","-bmanagement","0.0.0.0"]
