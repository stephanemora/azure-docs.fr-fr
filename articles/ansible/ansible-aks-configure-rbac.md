---
title: 'Tutoriel : Configurer les rôles de contrôle d’accès en fonction du rôle (RBAC) dans Azure Kubernetes Service (AKS) à l’aide d’Ansible'
description: Découvrez comment utiliser Ansible pour configurer RBAC dans un cluster Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, manuel, aks, conteneur, aks, kubernetes, azure AD, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836964"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Tutoriel : Configurer les rôles de contrôle d’accès en fonction du rôle (RBAC) dans Azure Kubernetes Service (AKS) à l’aide d’Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS peut être configuré pour utiliser [Azure Active Directory (AD)](/azure/active-directory/) pour l’authentification utilisateur. Une fois configuré, votre jeton d’authentification Azure AD vous permet de vous connecter au cluster AKS. Le RBAC peut être basé sur l’identité d’un utilisateur ou l’appartenance à un groupe de répertoire.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer un cluster AKS compatible Azure AD
> * Configurer un rôle RBAC dans le cluster

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Installer la bibliothèque RedHat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Configurer Azure AD pour l’authentification AKS

Lorsque vous configurez Azure AD pour l’authentification AKS, deux applications Azure AD sont configurées. Cette opération doit être effectuée par un administrateur de locataire Azure. Pour plus d’informations, consultez [Intégrer Azure Active Directory avec AKS](/azure/aks/aad-integration#create-the-server-application). 

À partir de l’administrateur du locataire Azure, obtenez les valeurs suivantes :

- Secret de l’application serveur
- ID de l’application serveur
- ID de l’application cliente 
- ID client

Ces valeurs sont nécessaires pour exécuter l’exemple de playbook.  

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Dans cette section, vous créez un service AKS avec l’[application Azure AD](#configure-azure-ad-for-aks-authentication).

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Le playbook charge `ssh_key` à partir de `~/.ssh/id_rsa.pub`. Si vous le modifiez, utilisez le format single-line en commençant par « ssh-rsa » (sans les guillemets).
- Les valeurs `client_id` et `client_secret` sont chargées à partir de `~/.azure/credentials`, qui est le fichier d’informations d’identification par défaut. Vous pouvez définir ces valeurs pour votre service principal ou les charger à partir de variables d’environnement :

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Enregistrez le playbook suivant en tant que `aks-create.yml` :

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Obtenir l’ID objet Azure AD

Pour créer une liaison RBAC, vous devez d’abord obtenir l’ID d’objet Azure Active Directory. 

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Dans le champ de recherche en haut de la page, entrez `Azure Active Directory`. 

1. Cliquez sur `Enter`.

1. Dans le menu **Gérer**, sélectionnez **Utilisateurs**.

1. Dans le champ des noms, recherchez votre compte.

1. Dans la colonne **Nom**, sélectionnez le lien vers votre compte.

1. Dans la section **Identité**, copiez l’**ID d’objet**.

    ![Copiez l’ID d’objet Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Créer une liaison RBAC

Dans cette section, vous créez une liaison de rôle ou une liaison de rôle de cluster dans AKS. 

Enregistrez le playbook suivant en tant que `kube-role.yml` :

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Remplacez l’espace réservé `<your-aad-account>` par votre locataire Azure AD [ID d’objet](#get-the-azure-ad-object-id).

Enregistrez le manifeste suivant – qui déploie votre nouveau rôle dans AKS – en tant que `aks-kube-deploy.yml` :

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Exécutez l’exemple de playbook

Cette section répertorie l’exemple de playbook complet qui appelle les tâches de création dans cet article. 

Enregistrez le playbook suivant en tant que `aks-rbac.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

Dans la section `vars`, remplacez les espaces réservés suivants par vos informations Azure AD :

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Exécutez le playbook complet en utilisant la commande `ansible-playbook` :

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Vérifier les résultats

Dans cette section, vous utilisez kubectl pour répertorier les nœuds de création dans cet article.

Entrez la commande suivante à l’invite du terminal :

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

La commande vous orientera vers une page d’authentification. Connectez-vous à votre compte Azure.

Une fois authentifié, kubectl répertorie les nœuds de la même façon pour les résultats suivants :

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

Enregistrez le code suivant en tant que `cleanup.yml` :

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ansible sur Azure](/azure/ansible/)
