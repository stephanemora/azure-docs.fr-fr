---
title: 'Tutoriel : Configurer des laboratoires dans Azure DevTest Labs à l’aide d’Ansible'
description: Guide pratique pour configurer un laboratoire dans Azure DevTest Labs à l’aide d’Ansible
keywords: ansible, azure, devops, bash, playbook, devtest labs
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b6981ee94c4d82997c574db037befb9782465d08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156279"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Tutoriel : Configurer des laboratoires dans Azure DevTest Labs à l’aide d’Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) permet aux développeurs d’automatiser la création d’environnements de machines virtuelles pour leurs applications. Ces environnements peuvent être configurés pour le développement, le test et l’apprentissage d’applications. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Création d’un laboratoire
> * Définir les stratégies du laboratoire
> * Définir les planifications du laboratoire
> * Créer le réseau virtuel du laboratoire
> * Définir une source d’artefact pour le laboratoire
> * Créer une machine virtuelle dans le laboratoire
> * Répertorier les sources d’artefact et les artefacts du laboratoire
> * Obtenir des informations Azure Resource Manager pour les sources d’artefact
> * Créer l’environnement du laboratoire
> * Créer l’image du laboratoire
> * Supprimer le laboratoire

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Créer un groupe de ressources

L’extrait de code de l’exemple de playbook crée un groupe de ressources Azure. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>Créer le laboratoire

La tâche suivante crée l’exemple de laboratoire.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Définir les stratégies du laboratoire

Vous pouvez définir les paramètres de stratégie du laboratoire. Les valeurs suivantes peuvent être définies :

- `user_owned_lab_vm_count` représente le nombre de machines virtuelles qu’un utilisateur peut posséder
- `user_owned_lab_premium_vm_count` représente le nombre de machines virtuelles premium qu’un utilisateur peut posséder
- `lab_vm_count` représente le nombre maximal de machines virtuelles d’un laboratoire
- `lab_premium_vm_count` représente le nombre maximal de machines virtuelles premium d’un laboratoire
- `lab_vm_size` représente les tailles de machines virtuelles autorisées pour un laboratoire
- `gallery_image` représente les images de galerie autorisées
- `user_owned_lab_vm_count_in_subnet` représente le nombre maximum de machines virtuelles de l’utilisateur dans un sous-réseau
- `lab_target_cost` représente le coût cible du laboratoire

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>Définir les planifications du laboratoire

L’exemple de tâche dans cette section configure la planification de laboratoire. 

Dans l’extrait de code suivant, la valeur `lab_vms_startup` est utilisée pour spécifier le délai de démarrage d’une machine virtuelle. De même, la valeur `lab_vms_shutdown` définit l’heure d’arrêt de la machine virtuelle du laboratoire.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>Créer le réseau virtuel du laboratoire

La tâche suivante crée le réseau virtuel par défaut du laboratoire.

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Définir une source d’artefact pour le laboratoire

Une source d’artefacts est un référentiel GitHub correctement structuré contenant une définition de l’artefact et des modèles Azure Resource Manager. Chaque laboratoire est fourni avec des artefacts publics prédéfinis. Les tâches suivantes vous montrent comment créer une source d’artefact pour un laboratoire.

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Créer une machine virtuelle dans le laboratoire

Créez une machine virtuelle dans le laboratoire.

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Répertorier les sources d’artefact et les artefacts du laboratoire

Pour répertorier toutes les sources d’artefacts personnalisées et par défaut du laboratoire, utilisez la tâche suivante :

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

La tâche suivante répertorie tous les artefacts :

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Obtenir des informations Azure Resource Manager pour les sources d’artefact

Pour répertorier tous les modèles Azure Resource Manager dans `public environment repository`, utilisez le référentiel prédéfini avec des modèles :

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

La tâche suivante récupère les détails d’un modèle Azure Resource Manager spécifique à partir du référentiel :

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>Créer l’environnement du laboratoire

La tâche suivante crée l’environnement du laboratoire selon l’un des modèles du référentiel d’environnement public.

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>Créer l’image du laboratoire

La tâche suivante crée une image à partir d’une machine virtuelle. L’image vous permet de créer des machines virtuelles identiques.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Supprimer le laboratoire

Pour supprimer le laboratoire, utilisez la tâche suivante :

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>Obtenir l’exemple de playbook

Il existe deux façons d’obtenir l’exemple de playbook complet :
- [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) et enregistrez-le sous `devtestlab-create.yml`.
- Créez un nouveau fichier nommé `devtestlab-create.yml` et copiez-y le contenu suivant :

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>Exécuter le playbook

Dans cette section, exécutez le playbook pour tester les différentes fonctionnalités présentées dans cet article.

Avant d’exécuter le playbook, effectuez les modifications suivantes :
- Dans la section `vars`, remplacez l’espace réservé `{{ resource_group_name }}` par le nom de votre groupe de ressources.
- Stockez le jeton GitHub comme une variable d’environnement nommée `GITHUB_ACCESS_TOKEN`.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

Enregistrez le code suivant en tant que `cleanup.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
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
> [Ansible sur Azure](/azure/ansible/)