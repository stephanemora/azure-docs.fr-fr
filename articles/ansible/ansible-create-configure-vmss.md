---
title: 'Tutoriel : Configurer des groupes identiques de machines virtuelles dans Azure avec Ansible'
description: Découvrez comment utiliser Ansible pour créer et configurer des groupes de machines virtuelles identiques dans Azure.
keywords: ansible, azure, devops, bash, playbook, machine virtuelle, groupe de machines virtuelles identiques, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e1cc40459988fb9bc38e3dbbcde563cebb531e3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156540"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutoriel : Configurer des groupes de machines virtuelles identiques dans Azure avec Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurer les ressources d’une machine virtuelle
> * Configurer un groupe identique
> * Mettre à l’échelle le groupe identique en augmentant ses instances de machines virtuelles 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Configurer un groupe identique

Le code de playbook dans cette section définit les ressources suivantes :

* **Groupe de ressources** dans lequel toutes les ressources seront déployées
* **Réseau virtuel** dans l’espace d’adressage 10.0.0.0/16
* **Sous-réseau** au sein du réseau virtuel
* **Adresse IP publique** qui vous permet d’accéder aux ressources via Internet
* **Groupe de sécurité réseau** qui contrôle le flux du trafic réseau en direction et en provenance du groupe identique
* **Équilibreur de charge** qui répartit le trafic sur un ensemble de machines virtuelles définies en utilisant des règles d’équilibreur de charge.
* **Groupe de machines virtuelles identiques** qui utilise toutes les ressources créées

Il existe deux façons d’obtenir le playbook de l’exemple :

* [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) et enregistrez-le sous `vmss-create.yml`.
* Créez un nouveau fichier nommé `vmss-create.yml` et copiez-y le contenu suivant :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
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

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
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

Avant d’exécuter le playbook, consultez les notes suivantes :

* Dans la section `vars`, remplacez l’espace réservé `{{ admin_password }}` par votre propre mot de passe.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook vmss-create.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Voir le nombre d’instances de machines virtuelles

Le [groupe identique configuré](#configure-a-scale-set) comporte actuellement deux instances. Suivez les étapes ci-dessous pour vérifier cette valeur :

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Accédez au groupe identique que vous avez configuré.

1. Le nom du groupe identique apparaît avec le nombre d’instances entre parenthèses : `Standard_DS1_v2 (2 instances)`.

1. Vous pouvez également vérifier le nombre d’instances avec [Azure Cloud Shell](https://shell.azure.com/) en exécutant la commande suivante :

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    La commande Azure CLI dans Cloud Shell montre qu’il existe maintenant trois instances : 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Effectuer un scale-out d’un groupe identique

Le code du playbook de cette section récupère des informations sur le groupe identique et fait passer sa capacité de deux à trois.

Il existe deux façons d’obtenir le playbook de l’exemple :

* [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) et enregistrez-le sous `vmss-scale-out.yml`.
* Créez un nouveau fichier nommé `vmss-scale-out.yml` et copiez-y le contenu suivant :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
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

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook vmss-scale-out.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
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

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Vérifier les résultats

Vérifiez le résultat de votre travail sur le Portail Azure :

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Accédez au groupe identique que vous avez configuré.

1. Le nom du groupe identique apparaît avec le nombre d’instances entre parenthèses : `Standard_DS1_v2 (3 instances)`. 

1. Vous pouvez également vérifier la modification avec [Azure Cloud Shell](https://shell.azure.com/) en exécutant la commande suivante :

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    La commande Azure CLI dans Cloud Shell montre qu’il existe maintenant trois instances : 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Tutoriel : Déployer des applications sur des groupes de machines virtuelles identiques dans Azure avec Ansible](./ansible-deploy-app-vmss.md)