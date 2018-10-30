---
title: Créer et configurer un serveur Azure Database pour MySQL à l’aide d’Ansible (préversion)
description: Découvrez comment utiliser Ansible pour créer et configurer un serveur Azure Database pour MySQL
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, mysql, base de données
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: b549aeaf24bd774245ee1f2ff6924ac1f6dbeee3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427894"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible-preview"></a>Créer et configurer un serveur Azure Database pour MySQL à l’aide d’Ansible (préversion)
[Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/) est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MySQL hautement disponibles dans le cloud. Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. 

Ce démarrage rapide vous montre comment utiliser Ansible pour créer un serveur Azure Database pour MySQL et configurer sa règle de pare-feu. Vous pouvez accomplir ces tâches en environ cinq minutes à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 est nécessaire pour exécuter les exemples de playbooks suivants dans ce didacticiel. Vous pouvez installer la version 2. 7 RC d’Ansible en exécutant `sudo pip install ansible[azure]==2.7.0rc2`. Ensuite, vous n’aurez plus besoin de préciser la version ici, car la version 2.7 sera celle par défaut.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.  

L’exemple suivant crée un groupe de ressources nommé **myResourceGroup** à l’emplacement **eastus** :

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

Enregistrez le playbook précédent en tant que **rg.yml**. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Créer un serveur MySQL et une base de données
L’exemple suivant crée un serveur MySQL nommé **mysqlserveransible** et une Azure Database pour MySQL nommée **mysqldbansible**. Il s’agit d’un serveur à usage général de base de 5e génération avec un vCore. 

La valeur de **mysqlserver_name** doit être unique. Consultez la documentation des [niveaux tarifaires](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) pour comprendre les valeurs valides par région et par niveau. Remplacez `<server_admin_password>` par un mot de passe.

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

Enregistrez le playbook précédent en tant que **mysql_create.yml**. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurer une règle de pare-feu
Une règle de pare-feu au niveau du serveur permet à une application externe de se connecter à votre serveur via le pare-feu du service Azure MySQL. Un exemple d’une application externe est l’outil de ligne de commande **mysql** ou MySQL Workbench.
L’exemple suivant crée une règle de pare-feu appelée **extenalaccess** qui autorise les connexions à partir de n’importe quelle adresse IP externe. 

Entrez vos propres valeurs pour **startIpAddress** et **endIpAddress**. Utilisez la plage d’adresses IP qui correspondent à l’emplacement de votre connexion. 

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

> [!NOTE]
> Les connexions à la base de données Azure pour MySQL communiquent via le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.
> 

Ici, le module **azure_rm_resource** est utilisé pour effectuer cette tâche. Il permet l’utilisation directe de l’API REST.

Enregistrez le playbook précédent en tant que **mysql_firewall.yml**. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Se connecter au serveur avec l’outil en ligne de commande
Vous pouvez [télécharger MySQL](https://dev.mysql.com/downloads/) et l’installer sur votre ordinateur. Une autre possibilité consiste à cliquer sur le bouton **Essayer** des exemples de code ou sur le bouton **>_** de la barre d’outils supérieure droite du Portail Azure et à ouvrir **Azure Cloud Shell**.

Tapez les commandes suivantes : 

1. Se connecter au serveur avec l’outil en ligne de commande **mysql** :
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. Afficher l’état du serveur :
```sql
 mysql> status
```

Si tout se déroule correctement, l’outil en ligne de commande doit générer le texte suivant :

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

## <a name="using-facts-to-query-mysql-servers"></a>Utiliser des faits pour interroger les serveurs MySQL
L’exemple suivant interroge les serveurs MySQL dans **myResourceGroup**, puis toutes les bases de données sur le serveur :

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

Enregistrez le playbook précédent en tant que **mysql_query.yml**. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook mysql_query.yml
```

Vous verrez ensuite la sortie suivante pour le serveur MySQL : 
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

Vous verrez également la sortie suivante pour la base de données MySQL :
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

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin de ces ressources, vous pouvez les supprimer en exécutant l’exemple ci-dessous. Il supprime un groupe de ressources appelé **myResourceGroup**. 

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

Enregistrez le playbook précédent sous le nom **rg_delete.yml**. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook rg_delete.yml
```

Si vous souhaitez supprimer uniquement le serveur MySQL nouvellement créé, exécutez l’exemple suivant :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Enregistrez le playbook précédent sous **mysql_delete.yml**. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Ansible sur Azure](https://docs.microsoft.com/azure/ansible/)
