# Setup di un cluster Docker Swarm con Ansible
[![Build Status](https://travis-ci.com/banh-gao/devops-exercise.svg?branch=master)](https://travis-ci.com/banh-gao/devops-exercise)


## Provisioning delle VM
Il provisioning delle VM viene fatto in locale con Vagrant e appoggiandosi al provider libvirt. Questo permette di poter eseguire e provare i playbook Ansible in locale in modo riproducibile e senza la necessità di configurare una ambiente cloud remoto.

Il Vagrantfile si occupa di creare due VM e di attaccargli un disco aggiuntivo ad ognuno che verrà poi usato per lo storage di Docker.

Il successivo provisioning delle singole VM con ansible viene poi fatto successivamente invocando ansible-playbook manualmente su un inventory statico. Questo perchè altrimenti Vagrant invocherebbe ansible separatamente per ogni singola VM, introducendo problemi di sincronizzazione nell'esecuzione dei task dei playbook.

Il plugin `hostmanager` di Vagrant permette di risolvere gli hostname delle VM dalla macchina host. Lo script `vagrant_key_authorization.rb`, eseguito durante la creazione delle VM, autorizza le chiavi ssh specificate nel Vagrantfile ad accedere alle VM. 
Aggiungendo inoltre questa configurazione al proprio ~/.ssh/config è possibile connettersi via ssh senza specificare ulteriori opzioni:
```
Host *.test
  StrictHostKeyChecking no
  UserKnownHostsFile=/dev/null
  User root
  LogLevel ERROR
```
Queste due azioni permettono ad ansible di accedere alle VM specificate nell'inventory tramite SSH.
Per dettagli su questa configurazione vedi https://max.engineer/six-ansible-practices#build-a-convenient-local-playground.

Per creare le VM con Vagrant eseguire:
```
vagrant up
```

## Setup con Ansible
Il playbook `swarm.yml` si occupa di creare un cluster swarm controllabile da remoto con API REST autenticate. In particolare il setup è diviso in tre ruoli:
- **docker_remote** si occupa di generare le chiavi e i certificati server e client che verranno usati per autenticarsi alle API REST. In una cartella locale genera anche il materiale crittografico necessario per l'autenticazione di un client.
- **docker_storage** perpara il disco attaccato da Vagrant che verrà montato sulla partizione di storage usata da docker. Questo evita il rischio di saturare lo spazio della partizione di root.
- **ansible-dockerswarm** si occupa del setup di docker e della creazione di un cluster swarm. Per il setup viene usato il ruolo [atosatto.docker-swarm](https://galaxy.ansible.com/atosatto/docker-swarm). Questo ruolo installa docker sui due nodi e crea un cluster Swarm con un manager e un worker.

Prima di eseguire il playbook scaricare le dipendenze da Ansible galaxy:
```
ansible-galaxy install -r requirements.yml
```

Per eseguire il playbook invocare questo comando ansible:
```
ansible-playbook -i inventory swarm.yml
```

## Connessione al demone Docker remoto
Il demone docker remoto può essere invocato specificando i file di autenticazione generati durante il setup nella cartella `swarm-auth`.

Ad esempio per mostrare la lista dei nodi nel cluster eseguire:
```
docker --tlsverify --tlscacert=swarm-auth/swarm_CA.pem --tlscert=swarm-auth/cert.pem --tlskey=swarm-auth/key.pem -H=node1.test:2376 node ls
```