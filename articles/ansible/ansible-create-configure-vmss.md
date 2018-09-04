---
title: Créer des groupes de machines virtuelles identiques dans Azure avec Ansible
description: Découvrez comment utiliser Ansible pour créer et configurer un groupe de machines virtuelles identiques dans Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, machine virtuelle, groupe de machines virtuelles identiques, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: f3b08c41d3bf083c7cca5897cee11a1a4b9c9092
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918573"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Créer des groupes de machines virtuelles identiques dans Azure avec Ansible
Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer votre groupe de machines virtuelles identiques dans Azure comme vous le feriez pour toute autre ressource Azure. Cet article vous montre comment utiliser Ansible pour créer et augmenter le nombre d’instances d’un groupe de machines virtuelles identiques. 

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.6 est nécessaire pour exécuter les exemples de playbooks suivants dans ce tutoriel. 

## <a name="create-a-vmss"></a>Créer un groupe de machines virtuelles identiques
Cette section présente un exemple de playbook Ansible qui définit les ressources suivantes :
- **Groupe de ressources** dans lequel toutes vos ressources seront déployées
- **Réseau virtuel** dans l’espace d’adressage 10.0.0.0/16
- **Sous-réseau** au sein du réseau virtuel
- **Adresse IP publique** qui vous permet d’accéder aux ressources via Internet
- **Groupe de sécurité réseau** qui contrôle le flux du trafic réseau en direction et en provenance de votre groupe de machines virtuelles identiques.
- **Équilibreur de charge** qui répartit le trafic sur un ensemble de machines virtuelles définies en utilisant des règles d’équilibreur de charge.
- **Groupe de machines virtuelles identiques** qui utilise toutes les ressources créées

Entrez votre propre mot de passe pour la valeur de *admin_password*.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Pour créer l’environnement du groupe de machines virtuelles identiques avec Ansible, enregistrez le playbook précédent en tant que `vmss-create.yml` ou [téléchargez l’exemple de playbook Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

  ```bash
  ansible-playbook vmss-create.yml
  ```

Une fois le playbook exécuté, une sortie similaire à l’exemple suivant montre que le groupe de machines virtuelles identiques a été correctement créé :

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Augmenter le nombre d’instances d’un groupe de machines virtuelles identiques
Le groupe de machines virtuelles identiques créé a deux instances. Si vous accédez au groupe de machines virtuelles identiques dans le portail Azure, vous voyez **Standard_DS1_v2 (2 instances)**. Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/) en exécutant la commande suivante dans Cloud Shell :

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Vous obtenez des résultats similaires à la sortie suivante :

  ```bash
  {
    "capacity": 2,
  }
  ```

Augmentons maintenant le nombre d’instances de deux à trois. Le code du playbook Ansible suivant récupère les informations sur le groupe de machines virtuelles identiques et fait passer sa capacité de deux à trois. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Pour augmenter le nombre d’instances du groupe de machines virtuelles identiques que vous avez créé, enregistrez le playbook précédent en tant que `vmss-scale-out.yml` ou [téléchargez l’exemple de playbook Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

La commande suivante exécute le playbook :

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

La sortie de l’exécution du playbook Ansible montre que l’augmentation du nombre d’instances du groupe de machines virtuelles identiques a été effectuée correctement :

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Si vous accédez au groupe de machines virtuelles identiques que vous avez configuré dans le portail Azure, vous voyez **Standard_DS1_v2 (3 instances)**. Vous pouvez également vérifier la modification avec [Azure Cloud Shell](https://shell.azure.com/) en exécutant la commande suivante :

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Les résultats de l’exécution de la commande dans Cloud Shell montrent qu’il existe maintenant trois instances. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Exemple de playbook Ansible pour un groupe de machines virtuelles identiques](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)