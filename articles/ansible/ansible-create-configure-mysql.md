---
title: 'Tutoriel : Configurer des bases de données dans Azure Database pour MySQL avec Ansible'
description: Découvrez comment utiliser Ansible pour créer et configurer un serveur Azure Database pour MySQL
keywords: ansible, azure, devops, bash, playbook, mysql, base de données
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: f068b3022c94466a20b524240dc293392b1f42ff
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603111"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Tutoriel : Configurer des bases de données dans Azure Database pour MySQL avec Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database pour MySQL](/azure/mysql/overview) est un service de base de données relationnelle qui s’appuie sur MySQL Community Edition et permet de gérer des bases de données MySQL dans des applications web.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer un serveur MySQL
> * Créer une base de données MySQL
> * Configurer une règle de pare-feu autorisant une application externe à se connecter au serveur
> * Se connecter au serveur MySQL avec Azure Cloud Shell
> * Interroger les serveurs MySQL disponibles
> * Lister toutes les bases de données des serveurs connectés

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Le code de playbook dans cette section crée un groupe de ressources Azure. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.  

Enregistrez le playbook suivant en tant que `rg.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Avant d’exécuter le playbook, consultez les notes suivantes :

* Un groupe de ressources nommé `myResourceGroup` est créé.
* Le groupe de ressources est créé à l’emplacement `eastus` :

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Créer un serveur MySQL et une base de données

Le code du playbook de cette section crée un serveur MySQL et une instance Azure Database pour MySQL. Le nouveau serveur MySQL est un serveur à usage de base de cinquième génération comportant 1 vCore et nommé `mysqlserveransible`. L’instance de base de données se nomme `mysqldbansible`.

Pour plus d’informations sur les niveaux tarifaires, voir [Niveaux tarifaires d’Azure Database pour MySQL](/azure/mysql/concepts-pricing-tiers). 

Enregistrez le playbook suivant en tant que `mysql_create.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Avant d’exécuter le playbook, consultez les notes suivantes :

* Dans la section `vars`, la valeur de `mysqlserver_name` doit être unique.
* Dans la section `vars`, remplacez `<server_admin_password>` par un mot de passe.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurer une règle de pare-feu

Une règle de pare-feu au niveau du serveur permet à une application externe de se connecter au serveur à travers le pare-feu du service Azure MySQL. Cette application externe peut être par exemple l’outil en ligne de commande `mysql` ou MySQL Workbench.

Le code du playbook de cette section crée une règle de pare-feu nommée `extenalaccess` qui autorise les connexions provenant d’une adresse IP externe. 

Enregistrez le playbook suivant en tant que `mysql_firewall.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

Avant d’exécuter le playbook, consultez les notes suivantes :

* Dans la section vars, remplacez `startIpAddress` et `endIpAddress`. Utilisez la plage d’adresses IP correspondant à celle qui vous servira à vous connecter.
* Les connexions à la base de données Azure pour MySQL communiquent via le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.
* Le playbook utilise le module `azure_rm_resource`, qui permet une utilisation directe de l’API REST.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Connexion au serveur

Cette section utilise Azure Cloud Shell pour se connecter au serveur créé précédemment.

1. Sélectionnez le bouton **Essayer** dans le code suivant :

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Dans l’invite, entrez la commande suivante pour interroger l’état du serveur :

    ```sql
    mysql> status
    ```
    
    Si tout se passe bien, la sortie est de ce type :
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>Interroger des serveurs MySQL

Le code du playbook de cette section interroge les serveurs MySQL de `myResourceGroup` et liste les bases de données des serveurs qu’il trouve.

Enregistrez le playbook suivant en tant que `mysql_query.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook mysql_query.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

La sortie suivante s’affiche également pour la base de données MySQL :

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

Enregistrez le playbook suivant en tant que `cleanup.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Ansible sur Azure](/azure/ansible/)