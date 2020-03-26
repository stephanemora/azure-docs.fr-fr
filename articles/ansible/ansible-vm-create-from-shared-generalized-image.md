---
title: 'Tutoriel : Créer une machine virtuelle ou un groupe de machines virtuelles identiques à partir d’Azure Shared Image Gallery en utilisant Ansible'
description: Découvrez comment utiliser Ansible pour créer une machine virtuelle ou un groupe de machines virtuelles identiques basé sur une image généralisée dans Shared Image Gallery.
keywords: ansible, azure, devops, bash, playbook, machine virtuelle, groupe de machines virtuelles identiques, shared image gallery
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155827"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Tutoriel : Créer une machine virtuelle ou un groupe de machines virtuelles identiques à partir d’Azure Shared Image Gallery en utilisant Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries) est un service qui permet aux clients de gérer, partager et organiser des images facilement. Cette fonctionnalité est utile pour les scénarios où de nombreuses images sont conservées et partagées. Les images personnalisées peuvent être partagées entre des abonnements et entre des locataires Azure Active Directory. Les images peuvent également être répliquées dans plusieurs régions, pour une mise à l’échelle plus rapide des déploiements.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer une machine virtuelle généralisée et une image personnalisée
> * Créer une galerie Shared Image Gallery
> * Créer une image partagée et une version d’image
> * Créer une machine virtuelle à l’aide de l’image généralisée
> * Créer un groupe de machines virtuelles identiques à l’aide de l’image généralisée
> * Obtenir des informations sur Shared Image Gallery, l’image et la version

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Obtenir les exemples de playbook

Il existe deux façons d’obtenir l’ensemble complet d’exemples de playbook :

- [Télécharger le dossier SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) et l’enregistrer sur votre ordinateur local.
- Créer un fichier pour chaque section et y copier l’exemple de playbook.

Le fichier `vars.yml` contient les variables utilisées par tous les exemples de playbooks pour ce tutoriel. Vous pouvez modifier le fichier pour fournir des noms et des valeurs uniques.

Le premier exemple de playbook `00-prerequisites.yml` crée ce qui est nécessaire pour suivre ce tutoriel :
- Un groupe de ressources, qui est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.
- Un réseau virtuel, un sous-réseau, une adresse IP publique et une carte d’interface réseau pour la machine virtuelle.
- Une machine virtuelle source, utilisée pour créer l’image généralisée.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 00-prerequisites.yml
```

Dans le [portail Azure](https://portal.azure.com), vérifiez le groupe de ressources que vous avez spécifié dans `vars.yml` pour voir la machine virtuelle et les différentes ressources que vous avez créées.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Généraliser la machine virtuelle et créer une image personnalisée

Le playbook suivant, `01a-create-generalized-image.yml`, généralise la machine virtuelle source créée à l’étape précédente, puis crée une image personnalisée basée sur celle-ci.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Vérifiez votre groupe de ressources et assurez-vous que `testimagea` s’affiche.

## <a name="create-the-shared-image-gallery"></a>Créer la galerie Shared Image Gallery

La galerie d’images est le dépôt pour le partage et la gestion des images. L’exemple de code de playbook dans `02-create-shared-image-gallery.yml` crée une galerie Shared Image Gallery dans votre groupe de ressources.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Vous voyez maintenant une nouvelle galerie, `myGallery`, dans votre groupe de ressources.

## <a name="create-a-shared-image-and-image-version"></a>Créer une image partagée et une version d’image

Le playbook suivant, `03a-create-shared-image-generalized.yml`, crée une définition d’image et une version d’image.

Les définitions d’image incluent le type d’image (Windows ou Linux), les notes de publication et les exigences de mémoire maximale et minimale. La version d’image est la version de l’image. La galerie, la définition d’image et la version d’image vous aident à organiser les images en groupes logiques. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Votre groupe de ressources a maintenant une définition d’image et une version d’image pour votre galerie.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Créer une machine virtuelle basée sur l’image généralisée

Enfin, exécutez `04a-create-vm-using-generalized-image.yml` pour créer une machine virtuelle basée sur l’image généralisée que vous avez créée à l’étape précédente.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Créer un groupe de machines virtuelles identiques basé sur l’image généralisée

Vous pouvez également créer un groupe de machines virtuelles identiques basé sur l’image généralisée. Pour ce faire, exécutez `05a-create-vmss-using-generalized-image.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Obtenir des informations sur la galerie

Vous pouvez obtenir des informations sur la galerie, la définition d’image et la version en exécutant `06-get-info.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Supprimer l’image partagée

Pour supprimer les ressources de la galerie, reportez-vous à l’exemple de playbook `07-delete-gallery.yml`. Supprimez les ressources dans l’ordre inverse. Commencez par supprimer la version d’image. Après avoir supprimé toutes les versions de l’image, vous pouvez supprimer la définition de l’image. Après avoir supprimé toutes les définitions de l’image, vous pouvez supprimer la galerie.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

L’exemple de code de playbook dans cette section est utilisé pour :

- Supprimer les deux groupes de ressources créés

Enregistrez le playbook suivant en tant que `cleanup.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Remplacez l’espace réservé `{{ resource_group_name }}` par le nom de votre groupe de ressources.
- Toutes les ressources des deux groupes de ressources spécifiés sont supprimées.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Ansible sur Azure](/azure/ansible/)