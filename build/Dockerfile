FROM quay.io/operator-framework/ansible-operator:v0.17.0

COPY requirements.yml ${HOME}/requirements.yml
user root 
RUN ansible-galaxy collection install -r ${HOME}/requirements.yml \
 && chmod -R ug+rwx ${HOME}/.ansible && chmod ug+rwX  /opt/

COPY watches.yaml ${HOME}/watches.yaml

COPY roles/ ${HOME}/roles/
