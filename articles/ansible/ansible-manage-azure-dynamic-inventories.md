---
title: 'Tutoriel : Configurer des inventaires dynamiques de ressources Azure avec Ansible'
description: Découvrez comment utiliser Ansible pour gérer vos inventaires dynamiques Azure
keywords: ansible, azure, devops, bash, cloudshell, inventaire dynamique
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247857"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Tutoriel : Tutoriel : Configurer des inventaires dynamiques de ressources Azure avec Ansible

Ansible peut être utilisé pour extraire des informations d’inventaire de diverses sources (y compris les sources cloud comme Azure) dans un *inventaire dynamique*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurer deux machines virtuelles de test 
> * Ajouter des balises à l’une des machines virtuelles
> * Installer Nginx sur les machines virtuelles avec balises
> * Configurer un inventaire dynamique comportant les ressources Azure configurées

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Créer les machines virtuelles de test

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Ouvrez [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Créez un groupe de ressources Azure pour héberger les machines virtuelles utilisées dans ce didacticiel.

    > [!IMPORTANT]  
    > Le groupe de ressources Azure que vous créez dans cette étape doit avoir un nom entièrement en minuscules. Sinon, la génération de l’inventaire dynamique échouera.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Créez deux machines virtuelles de Linux sur Azure en utilisant l’une des méthodes suivantes :

    - **Playbook Ansible** : l’article [Créer une machine virtuelle Linux de base dans Azure avec Ansible](./ansible-create-vm.md) montre comment créer une machine virtuelle à partir d’un playbook Ansible. Si vous utilisez un playbook pour définir une machine virtuelle ou les deux, assurez-vous que la connexion SSH est utilisée plutôt qu’un mot de passe.

    - **Azure CLI** : émettez toutes les commandes suivantes dans Cloud Shell pour créer les deux machines virtuelles :

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Ajouter des balises à une machine virtuelle

Vous pouvez [utiliser des étiquettes pour organiser vos ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) par catégories définies par l’utilisateur. 

### <a name="using-ansible-version--28"></a>Avec une version d’Ansible antérieure à 2.8
Entrez la commande suivante [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) pour étiqueter la machine virtuelle `ansible-inventory-test-vm1` avec la clé `nginx` :

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Avec une version d’Ansible supérieure ou égale à 2.8
Entrez la commande suivante [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) pour étiqueter la machine virtuelle `ansible-inventory-test-vm1` avec la clé `Ansible=nginx` :

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Générer un inventaire dynamique

Une fois que vos machines virtuelles sont définies (et étiquetées), il est temps de générer l’inventaire dynamique.

### <a name="using-ansible-version--28"></a>Avec une version d’Ansible antérieure à 2.8

Ansible fournit un script Python nommé [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) qui génère un inventaire dynamique des ressources Azure. Les étapes suivantes vous expliquent comment utiliser le script `azure_rm.py` pour vous connecter à vos deux machines virtuelles de test Azure :

1. Utilisez la commande `wget` GNU pour récupérer le script `azure_rm.py` :

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Utilisez la commande `chmod` pour modifier les autorisations d’accès au script `azure_rm.py`. La commande suivante utilise le paramètre `+x` pour permettre l’exécution du fichier spécifié (`azure_rm.py`) :

    ```python
    chmod +x azure_rm.py
    ```

1. Utilisez la [commande ansible](https://docs.ansible.com/ansible/2.4/ansible.html) pour vous connecter à votre groupe de ressources : 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Une fois connecté, vous constatez des résultats similaires à ceux de la sortie suivante :

    ```output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Avec Ansible 2.8 ou une version ultérieure

À compter d’Ansible 2.8, Ansible fournit un [plug-in d’inventaire dynamique Azure](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py), qui s’utilise ainsi :

1. Le plug-in d’inventaire a besoin d’un fichier config, qui doit se terminer par `azure_rm` et avoir l’extension `yml` ou `yaml`. Dans l’exemple de ce tutoriel, enregistrez le playbook suivant sous le nom `myazure_rm.yml` :

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Exécutez la commande suivante pour effectuer un test ping des machines virtuelles du groupe de ressources :

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. La commande précédente devrait provoquer l’erreur suivante :

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Si vous recevez l’erreur « Host key verification », ajoutez la ligne suivante au fichier de configuration Ansible, qui se trouve dans `/etc/ansible/ansible.cfg` ou `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Le playbook devrait alors donner une sortie de ce type :
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Activer la balise de machine virtuelle

### <a name="if-youre-using-ansible--28"></a>Si vous utilisez une version d’Ansible antérieure à la version 2.8 :

- Une fois définie, une balise doit être activée. Pour cela, vous pouvez l’exporter dans une variable d’environnement `AZURE_TAGS` avec la commande `export` :

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Exécutez la commande suivante :

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Une seule machine virtuelle apparaît (celle dont la balise correspond à la valeur exportée dans la variable d’environnement `AZURE_TAGS`) :

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Si vous utilisez une version d’Ansible supérieure ou égale à la version 2.8

- Exécutez la commande `ansible-inventory -i myazure_rm.yml --graph` pour obtenir la sortie suivante :

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Vous pouvez également exécuter la commande suivante pour tester la connexion à la machine virtuelle Nginx :
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurer Nginx sur la machine virtuelle étiquetée

L’objectif des étiquettes est de travailler rapidement et facilement avec des sous-groupes de vos machines virtuelles. Par exemple, supposons que vous souhaitez installer Nginx uniquement sur les machines virtuelles auxquelles vous avez assigné une étiquette `nginx`. Les étapes suivantes illustrent la facilité de réalisation de cette tâche :

1. Créer un fichier nommé `nginx.yml` :

   ```console
   code nginx.yml
   ```

1. Collez l’exemple de code ci-après dans l’éditeur :

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Enregistrez le fichier et quittez l’éditeur.

1. Exécutez le playbook à l’aide de la commande `ansible-playbook` :

   - Version d’Ansible antérieure à 2.8 :

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible 2.8 ou une version ultérieure :

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

    ```output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Tester l’installation de Nginx

Cette section présente une technique de test de l’installation de Nginx sur votre machine virtuelle.

1. Utilisez la commande [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) pour récupérer l’adresse IP de la machine virtuelle `ansible-inventory-test-vm1`. La valeur renvoyée (l’adresse IP de la machine virtuelle) est ensuite utilisée comme paramètre de la commande SSH pour vous connecter à la machine virtuelle.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Lorsque vous êtes connecté à la machine virtuelle `ansible-inventory-test-vm1`, exécutez la commande [nginx - v](https://nginx.org/en/docs/switches.html) pour déterminer si Nginx est installé.

    ```console
    nginx -v
    ```

1. Une fois que vous exécutez la commande `nginx -v`, vous voyez la version de Nginx (deuxième ligne) qui indique que Nginx est installé.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Appuyez sur la combinaison de touches `<Ctrl>D` pour déconnecter la session SSH.

1. Si vous répétez les étapes précédentes pour la machine virtuelle `ansible-inventory-test-vm2`, vous obtenez un message d’information indiquant où télécharger Nginx (ce qui implique qu’il n’est pas encore installé) :

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Démarrage rapide : Configurer des machines virtuelles Linux dans Azure avec Ansible](./ansible-create-vm.md)