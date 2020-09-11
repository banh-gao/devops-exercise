# Provisioning delle VM
Il provisioning delle VM viene fatto in locale con Vagrant e appoggiandosi al provider libvirt. Questo permette di poter eseguire e provare i playbook Ansible in locale in modo riproducibile e senza la necessità di configurare una ambiente cloud remoto.

Il Vagrantfile si occupa di creare due VM, di attaccargli un disco aggiuntivo ad ognuno ed infine di eseguire il loro provisioning con il playbook Ansible (nella realizzazione di playbook reali [è consigliabile](https://max.engineer/six-ansible-practices#build-a-convenient-local-playground) configurare gli host e ssh per poter chiamare ansible manualmente).

# Setup di Docker
Il ruolo docker-storage prepara il disco che verrà montato sulla partizione di storage usata da docker.Questo evita il rischio di saturare lo spazio della partizione di root.
