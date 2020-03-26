---
title: 'Démarrage rapide : Configurer des machines virtuelles Linux dans Azure à l’aide d’Ansible'
description: Dans ce démarrage rapide, apprenez à créer une machine virtuelle Linux dans Azure à l’aide d’Ansible
keywords: ansible, azure, devops, machine virtuelle
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 111003718fc82683f1756276132def0aea7b7375
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239569"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Démarrage rapide : Configurer des machines virtuelles Linux dans Azure à l’aide d’Ansible

À l’aide d’un langage déclaratif, Ansible vous permet d’automatiser la création, la configuration et le déploiement de ressources Azure par le biais de *playbooks* Ansible. Cet article présente un exemple de playbook Ansible pour la configuration de machines virtuelles Linux. Le [playbook Ansible complet](#complete-sample-ansible-playbook) est fourni à la fin de cet article.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Ansible a besoin d’un groupe de ressources dans lequel vos ressources seront déployées. L’exemple ci-après de section d’un playbook Ansible crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `eastus` :

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Lorsque vous créez une machine virtuelle Azure, vous devez créer un [réseau virtuel](/azure/virtual-network/virtual-networks-overview) ou en utiliser un existant. Vous devez également déterminer la manière dont vos machines virtuelles doivent être accessibles sur le réseau virtuel. L’exemple ci-après de section d’un playbook Ansible crée un réseau virtuel nommé `myVnet` dans l’espace d’adressage `10.0.0.0/16` :

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Toutes les ressources Azure déployées dans un réseau virtuel sont déployées dans un [sous-réseau](/azure/virtual-network/virtual-network-manage-subnet) de ce réseau virtuel. 

L’exemple ci-après de section d’un playbook Ansible crée un sous-réseau nommé `mySubnet` dans le réseau virtuel `myVnet` :

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique





Les [adresses IP publiques](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) permettent aux ressources Internet de communiquer avec les ressources Azure (communication entrante). Les adresses IP publiques permettent également aux ressources Azure de communiquer avec les services Azure publics (communication sortante). Dans les deux scénarios, vous accédez à une adresse IP attribuée à la ressource. L’adresse est dédiée à la ressource jusqu’à ce que vous annuliez son attribution. Si une adresse IP publique n’est pas attribuée à une ressource, la ressource peut encore communiquer avec Internet (communication sortante). La connexion est établie par Azure, qui affecte dynamiquement une adresse IP disponible. L’adresse attribuée dynamiquement n’est pas dédiée à la ressource.

L’exemple ci-après de section d’un playbook Ansible crée une adresse IP publique nommée `myPublicIP` :

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Les [groupes de sécurité réseau](/azure/virtual-network/security-overview) filtrent le trafic réseau entre les ressources Azure d’un réseau virtuel. Des règles de sécurité sont définies et régissent le trafic entrant et sortant vers et depuis les ressources Azure. Pour plus d’informations sur les ressources Azure et les groupes de sécurité réseau, consultez [Intégration d’un réseau virtuel pour les services Azure](/azure/virtual-network/virtual-network-for-azure-services)

Le playbook suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`. Le groupe de sécurité réseau inclut une règle qui autorise le trafic SSH sur le port TCP 22.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```

## <a name="create-a-virtual-network-interface-card"></a>Créer une carte d’interface réseau virtuelle

Une carte d’interface réseau virtuelle connecte votre machine virtuelle à un réseau virtuel, à une adresse IP publique et à un groupe de sécurité réseau spécifiques. 

La section ci-après d’un exemple de section d’un playbook Ansible crée une carte d’interface réseau virtuelle nommée `myNIC` qui est connectée aux ressources de réseau virtuel que vous avez créées :

```yaml
- name: Create virtual network interface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

L’étape finale consiste à créer une machine virtuelle qui utilise toutes les ressources que vous avez créées dans les sections précédentes de cet article. 

L’exemple de section d’un playbook Ansible présentée dans cette section crée une machine virtuelle nommée `myVM` et y attache la carte d’interface réseau virtuelle nommée `myNIC`. Remplacez l’espace réservé &lt;your-key-data> par vos propres données de clé publique complètes.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Exemple de playbook Ansible complet

Cette section présente l’exemple de playbook Ansible complet que vous avez élaboré dans le cadre de cet article. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network interface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Exécuter l’exemple de playbook Ansible

Cette section vous guide tout au long de l’exécution de l’exemple de playbook Ansible présenté dans cet article.

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Ouvrez [Cloud Shell](/azure/cloud-shell/overview).

1. Créez un fichier (pour héberger votre playbook) nommé `azure_create_complete_vm.yml`, puis ouvrez-le dans l’éditeur VI, comme suit :

   ```bash
   vi azure_create_complete_vm.yml
   ```

1. Passez en mode insertion en sélectionnant la touche **I**.

1. Collez [l’exemple complet de playbook Ansible](#complete-sample-ansible-playbook) dans l’éditeur.

1. Quittez le mode insertion en sélectionnant la touche **Échap**.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Exécutez l’exemple de playbook Ansible.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. Vous devriez obtenir une sortie semblable à celle qui suit, indiquant qu’une machine virtuelle a été créée :

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network interface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. La commande SSH permet d’accéder à votre machine virtuelle Linux. Remplacez l’espace réservé &lt;ip-address> par l’adresse IP de l’étape précédente.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Démarrage rapide : Gérer une machine virtuelle Linux dans Azure à l’aide d’Ansible](./ansible-manage-linux-vm.md)
