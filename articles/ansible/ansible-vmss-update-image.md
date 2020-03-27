---
title: 'Tutoriel : Mettre à jour l’image personnalisée de groupes identiques de machines virtuelles Azure avec Ansible'
description: Découvrez comment utiliser Ansible pour mettre à jour des groupes de machines virtuelles identiques dans Azure avec une image personnalisée.
keywords: ansible, azure, devops, bash, playbook, machine virtuelle, groupe de machines virtuelles identiques, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b7d3053c09d2dcb667a4fc407035f4814f786932
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155851"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Tutoriel : Mettre à jour l’image personnalisée de groupes de machines virtuelles identiques Azure avec Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Une fois déployée, une machine virtuelle est configurée avec le logiciel nécessaire à l’application. Au lieu d’effectuer cette tâche de configuration pour chaque machine virtuelle, vous pouvez créer une image personnalisée. Il s’agit d’une capture instantanée d’une machine virtuelle qui comporte tous les logiciels installés. Lorsque vous [configurez un groupe identique](./ansible-create-configure-vmss.md), vous spécifiez l’image à utiliser pour ses machines virtuelles. Avec une image personnalisée, toutes les instances de machines virtuelles de l’application sont configurées à l’identique. Il est parfois nécessaire de mettre à jour l’image personnalisée du groupe identique. C’est sur cette tâche que porte ce tutoriel.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurer deux machines virtuelles avec HTTPD
> * Créer une image personnalisée à partir d’une machine virtuelle
> * Créer un groupe identique à partir d’une image
> * Mettre à jour l’image personnalisée

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Configurer deux machines virtuelles

Le code du playbook de cette section crée deux machines virtuelles sur lesquelles HTTPD est installé. 

La page `index.html` de chaque machine virtuelle affiche une chaîne de test :

* La première machine virtuelle affiche la valeur `Image A`.
* La deuxième machine virtuelle affiche la valeur `Image B`.

Cette chaîne est destinée à simuler la configuration de chaque machine virtuelle avec différents logiciels.

Il existe deux façons d’obtenir le playbook de l’exemple :

* [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) et enregistrez-le sous `create_vms.yml`.
* Créez un nouveau fichier nommé `create_vms.yml` et copiez-y le contenu suivant :

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Exécutez le playbook avec la commande `ansible-playbook`, en remplaçant `myrg` par le nom de votre groupe de ressources :

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

En raison des sections `debug` du playbook, la commande `ansible-playbook` imprime l’adresse IP de chaque machine virtuelle. Copiez-les pour plus tard.

![Adresse IP des machines virtuelles](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Se connecter aux deux machines virtuelles

L’objectif de cette section est de se connecter à chaque machine virtuelle. Comme on l’a vu dans la section précédente, les chaînes `Image A` et `Image B` simulent deux machines virtuelles distinctes avec différentes configurations.

Avec les adresses IP de la section précédente, connectez-vous aux deux machines virtuelles :

![Capture d’écran de la machine virtuelle A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Capture d’écran de la machine virtuelle B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Créer des images à partir de chaque machine virtuelle

Vous avez maintenant deux machines virtuelles présentant une configuration légèrement différente (le fichier `index.html`).

Le code du playbook de cette section crée une image personnalisée pour chaque machine virtuelle :

* `image_vmforimageA` : l’image personnalisée créée pour la machine virtuelle qui affiche `Image A` sur sa page d’accueil.
* `image_vmforimageB` : l’image personnalisée créée pour la machine virtuelle qui affiche `Image B` sur sa page d’accueil.

Il existe deux façons d’obtenir le playbook de l’exemple :

* [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) et enregistrez-le sous `capture-images.yml`.
* Créez un nouveau fichier nommé `capture-images.yml` et copiez-y le contenu suivant :

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Exécutez le playbook avec la commande `ansible-playbook`, en remplaçant `myrg` par le nom de votre groupe de ressources :

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Créer un groupe identique avec l’image A

Le playbook de cette section permet de configurer les ressources Azure suivantes :

* Adresse IP publique
* Équilibrage de charge
* Groupe identique faisant référence à `image_vmforimageA`

Il existe deux façons d’obtenir le playbook de l’exemple :

* [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) et enregistrez-le sous `create-vmss.yml`.
* Créez un fichier nommé `create-vmss.yml` et copiez-y le contenu suivant :

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Exécutez le playbook avec la commande `ansible-playbook`, en remplaçant `myrg` par le nom de votre groupe de ressources :

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

En raison des sections `debug` du playbook, la commande `ansible-playbook` imprime l’adresse IP du groupe identique. Copiez-la pour plus tard.

![Adresse IP publique](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Se connecter au groupe identique

L’objectif de cette section est de se connecter au groupe identique. 

Avec l’adresse IP de la section précédente, connectez-vous au groupe identique.

Comme on l’a vu dans la section précédente, les chaînes `Image A` et `Image B` simulent deux machines virtuelles distinctes avec différentes configurations.

Le groupe identique fait référence à l’image personnalisée nommée `image_vmforimageA`, créée à partir de la machine virtuelle dont la page d’accueil affiche `Image A`.

Par conséquent, la page d’accueil affiche `Image A` :

![Le groupe identique est associé à la première machine virtuelle.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Laissez la fenêtre du navigateur ouverte tandis que vous passez à la section suivante.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Modifier l’image personnalisée du groupe identique et mettre à niveau les instances

Le code du playbook de cette section remplace l’image du groupe identique `image_vmforimageA` par `image_vmforimageB`. En outre, toutes les machines virtuelles actuellement déployées par le groupe identique sont mises à jour.

Il existe deux façons d’obtenir le playbook de l’exemple :

* [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) et enregistrez-le sous `update-vmss-image.yml`.
* Créez un nouveau fichier nommé `update-vmss-image.yml` et copiez-y le contenu suivant :

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Exécutez le playbook avec la commande `ansible-playbook`, en remplaçant `myrg` par le nom de votre groupe de ressources :

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Revenez sur votre navigateur et actualisez la page. 

L’image personnalisée sous-jacente de la machine virtuelle est mise à jour.

![Le groupe identique est associé à la deuxième machine virtuelle.](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

Enregistrez le code suivant en tant que `cleanup.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Ansible sur Azure](/azure/ansible)