FROM ghcr.io/zahidsultan1/wso2-prod/wso2/wso2mi:4.1.0-ol7

COPY extras/axis2.xml ${WSO2_SERVER_HOME}/conf/axis2/
COPY deployment.toml ${WSO2_SERVER_HOME}/conf/
COPY extras/mysql-connector-java-8.0.29.jar ${WSO2_SERVER_HOME}/lib/

USER root
RUN rm -f /etc/localtime
RUN ln -sv /usr/share/zoneinfo/Asia/Karachi /etc/localtime
RUN echo "Asia/Karachi" > /etc/timezone
RUN chown 802:802 ${WSO2_SERVER_HOME}/conf/axis2/axis2.xml
RUN chmod 644 ${WSO2_SERVER_HOME}/conf/axis2/axis2.xml
RUN chown 802:802 ${WSO2_SERVER_HOME}/conf/deployment.toml
RUN chmod 644 ${WSO2_SERVER_HOME}/conf/deployment.toml

RUN chown -R 802:802 ${WSO2_SERVER_HOME}/lib/
RUN chmod -R 777 ${WSO2_SERVER_HOME}/lib/
RUN chown -R 802:802 ${WSO2_SERVER_HOME}/dropins/
RUN chmod -R 777 ${WSO2_SERVER_HOME}/dropins/

COPY application.car /tmp/application.car
RUN cp /tmp/application.car ${WSO2_SERVER_HOME}/repository/deployment/server/carbonapps/application.car
COPY mi-dashboard.conf /tmp/mi-dashboard.conf
RUN cat /tmp/mi-dashboard.conf >> ${WSO2_SERVER_HOME}/conf/deployment.toml
