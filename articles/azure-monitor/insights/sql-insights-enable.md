---
title: Activer SQL Insights
description: Activer SQL Insights dans Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 385bf6382fd25406fc9927df806f35dbf973d8fa
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142528"
---
# <a name="enable-sql-insights-preview"></a>Activer SQL Insights (préversion)
Cet article explique comment activer [SQL Insights](sql-insights-overview.md) pour analyser vos déploiements SQL. L’analyse est effectuée à partir d’une machine virtuelle Azure qui établit une connexion à vos déploiements SQL et utilise des vues de gestion dynamique (DMV) pour collecter les données d’analyse. Vous pouvez contrôler les jeux de données qui sont collectés et la fréquence de collecte à l’aide d’un profil d’analyse.

> [!NOTE]
> Pour activer SQL Insights en créant le profil de surveillance et la machine virtuelle à l’aide d’un modèle Resource Manager, consultez [Exemples de modèles Resource Manager pour SQL Insights](resource-manager-sql-insights.md).

## <a name="create-log-analytics-workspace"></a>Créer un espace de travail Log Analytics
SQL Insights stocke ses données dans un ou plusieurs [espaces de travail Log Analytics](../logs/data-platform-logs.md#log-analytics-workspaces).  Avant de pouvoir activer SQL Insights, vous devez soit [créer un espace de travail](../logs/quick-create-workspace.md), soit en sélectionner un existant. Un seul espace de travail peut être utilisé avec plusieurs profils d’analyse, mais l’espace de travail et les profils doivent se trouver dans la même région Azure. Pour activer les fonctionnalités et y accéder dans SQL Insights, vous devez avoir le [rôle de contributeur Log Analytics](../logs/manage-access.md) dans l’espace de travail. 

## <a name="create-monitoring-user"></a>Créer des règles d’analyse 
Vous avez besoin d’un utilisateur sur les déploiements SQL que vous souhaitez analyser. Suivez les procédures ci-dessous pour les différents types de déploiements SQL.

Les instructions ci-dessous décrivent le processus par type de SQL que vous pouvez surveiller.  Pour effectuer cette opération avec un script sur plusieurs ressources SQL à la fois, reportez-vous au [fichier LISEZMOI](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) et à l’[exemple de script](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1) suivants.


### <a name="azure-sql-database"></a>Base de données Azure SQL
Ouvrez Azure SQL Database avec [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou [Éditeur de requête (préversion)](../../azure-sql/database/connect-query-portal.md) dans le Portail Azure.

Exécutez le script suivant pour créer un utilisateur avec les autorisations requises. Remplacez *utilisateur* par un nom d’utilisateur et *mystrongpassword* par un mot de passe.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Créez un script utilisateur Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Vérifiez que l’utilisateur a été créé.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Vérifiez le script utilisateur Telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Connectez-vous à Azure SQL Managed Instance et utilisez [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou un outil similaire pour exécuter le script suivant afin de créer l’utilisateur d’analyse avec les autorisations nécessaires. Remplacez *utilisateur* par un nom d’utilisateur et *mystrongpassword* par un mot de passe.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Connectez-vous à votre machine virtuelle Azure exécutant SQL Server et utilisez [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou un outil similaire pour exécuter le script suivant afin de créer l’utilisateur d’analyse avec les autorisations nécessaires. Remplacez *utilisateur* par un nom d’utilisateur et *mystrongpassword* par un mot de passe.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Vérifiez que l’utilisateur a été créé.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Créer une machine virtuelle Azure 
Vous devrez créer une ou plusieurs machines virtuelles Azure qui seront utilisées pour collecter des données afin d’analyser SQL.  

> [!NOTE]
>  Les [profils d’analyse](#create-sql-monitoring-profile) spécifient les données que vous recueillerez parmi les différents types de SQL que vous souhaitez analyser. Chaque ordinateur virtuel d’analyse ne peut être associé qu’à un seul profil d’analyse. Si vous avez besoin de plusieurs profils d’analyse, vous devez créer une machine virtuelle pour chacun d’entre eux.

### <a name="azure-virtual-machine-requirements"></a>Configuration requise des machines virtuelles Azure
La configuration requise est la suivante pour les machines virtuelles Azure.

- Système d’exploitation : Ubuntu 18.04 
- Tailles recommandées des machines virtuelles Azure : Standard_B2s (2 processeurs, 4 Gio de mémoire) 
- Régions prises en charge : toutes les [régions prises en charge par l’agent de Azure Monitor](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> La taille de machine virtuelle Standard_B2s (2 processeurs, 4 Gio de mémoire) prend en charge jusqu’à 100 chaînes de connexion. Vous ne devez pas allouer plus de 100 connexions à une seule machine virtuelle.

En fonction des paramètres réseau de vos ressources SQL, les machines virtuelles devront peut-être être placées dans le même réseau virtuel que vos ressources SQL afin qu’elles puissent établir des connexions réseau pour collecter les données d’analyse.  

## <a name="configure-network-settings"></a>Configurer les paramètres réseau
Chaque type de SQL offre des méthodes pour que votre machine virtuelle d’analyse accède en toute sécurité à SQL.  Les sections ci-dessous couvrent les options basées sur le type de SQL.

### <a name="azure-sql-databases"></a>Bases de données SQL Azure  

SQL Insights prend en charge l’accès à votre Azure SQL Database via son point de terminaison public, ainsi qu’à partir de son réseau virtuel.

Pour accéder via le point de terminaison public, vous devez ajouter une règle dans la page **Paramètres de pare-feu** et dans la section [Paramètres du pare-feu IP](../../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules) .  Pour spécifier l’accès à partir d’un réseau virtuel, vous pouvez définir des [règles de pare-feu de réseau virtuel](../../azure-sql/database/network-access-controls-overview.md#virtual-network-firewall-rules) et définir les [balises de service requises par l’agent de Azure Monitor](../agents/azure-monitor-agent-overview.md#networking).  [Cet article](../../azure-sql/database/network-access-controls-overview.md#ip-vs-virtual-network-firewall-rules) décrit les différences entre ces deux types de règles de pare-feu.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Définir le pare-feu du serveur" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Paramètres du pare-feu." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Instances Azure SQL Managed Instance 

Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel que vos ressources SQL MI, consultez [Se connecter à l’intérieur du même réseau virtuel](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-the-same-vnet). Si votre machine virtuelle d’analyse se trouve dans le réseau virtuel différent de vos ressources SQL MI, consultez [Se connecter à l’intérieur d’un autre réseau virtuel](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Machine virtuelle Azure et machine virtuelle Azure SQL  
Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel que les ressources de votre machine virtuelle SQL, consultez [Se connecter à SQL Server au sein d’un réseau virtuel](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-within-a-virtual-network). Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel que les ressources de votre machine virtuelle SQL, consultez  [Se connecter à SQL Server via Internet](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Stocker le mot de passe d’analyse dans Key Vault
Vous devez stocker vos mots de passe de connexion utilisateur SQL dans un Key Vault au lieu de les entrer directement dans vos chaînes de connexion de profil d’analyse.

Lorsque vous configurez votre profil pour l’analyse SQL, vous devez disposer de l’une des autorisations suivantes sur la ressource Key Vault que vous souhaitez utiliser :

- Microsoft.Authorization/roleAssignments/write 
- Autorisations Microsoft.Authorization/roleAssignments/delete telle que Administrateur d’accès utilisateur ou Propriétaire 

Une nouvelle stratégie d’accès sera automatiquement créée dans le cadre de la création de votre profil SQL Monitoring qui utilise le Key Vault que vous avez spécifié. Utilisez *Autoriser l’accès à partir de tous les réseaux* pour les paramètres réseau Key Vault.


## <a name="create-sql-monitoring-profile"></a>Créer un profil d’analyse SQL
Ouvrez SQL Insights en sélectionnant **SQL (préversion)** de la section **Insight** du menu **Azure Monitor** dans le Portail Azure. Cliquez sur **Créer un profil**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Créer un nouveau profil." lightbox="media/sql-insights-enable/create-new-profile.png":::

Le profil stocke les informations que vous souhaitez collecter à partir de vos systèmes SQL.  Il dispose de paramètres spécifiques pour : 

- Azure SQL Database 
- Instances Azure SQL Managed Instance 
- Exécution de SQL Server sur des machines virtuelles  

Par exemple, vous pouvez créer un profil nommé *Production SQL* et un autre nommé *Mise en lots SQL* avec des paramètres différents pour la fréquence de collecte des données, les données à collecter et l’espace de travail auquel les données doivent être envoyées. 

Le profil est stocké en tant que ressource de [règle de collecte de données](../agents/data-collection-rule-overview.md) dans l’abonnement et le groupe de ressources que vous sélectionnez. Chaque profil a besoin des éléments suivants :

- Nom. Ne peut pas être modifié une fois créé.
- Lieu. Il s’agit d’une région Azure.
- Espace de travail Log Analytics pour stocker les données d’analyse.
- Paramètres de collecte pour la fréquence et le type de données d’analyse SQL à collecter.

> [!NOTE]
> L’emplacement du profil doit se trouver au même emplacement que l’espace de travail Log Analytics auquel vous envisagez d’envoyer les données d’analyse.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Détails du profil." lightbox="media/sql-insights-enable/profile-details.png":::

Cliquez sur **Créer un profil d’analyse** une fois que vous avez entré les détails de votre profil d’analyse. Le déploiement du fichier peut prendre jusqu’à une minute.  Si vous ne voyez pas le nouveau profil répertorié dans la zone de liste modifiable **Profil d’analyse**, cliquez sur le bouton Actualiser. il doit apparaître une fois le déploiement terminé.  Une fois que vous avez sélectionné le nouveau profil, sélectionnez l’onglet **Gérer le profil** pour ajouter une machine d’analyse qui sera associée au profil.

### <a name="add-monitoring-machine"></a>Ajouter une machine d’analyse
Sélectionnez **Ajouter une machine d’analyse** pour ouvrir un panneau contextuel et choisir la machine virtuelle à configurer pour analyser vos instances SQL et fournir les chaînes de connexion.

Sélectionnez l’abonnement et le nom de votre machine virtuelle d’analyse. Si vous utilisez Key Vault pour stocker votre mot de passe pour l’utilisateur d’analyse, sélectionnez les ressources Key Vault avec ces secrets, puis entrez l’URL et le nom du secret à utiliser dans les chaînes de connexion. Pour plus d’informations sur l’identification de la chaîne de connexion pour différents déploiements SQL, consultez la section suivante.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Ajouter une machine d’analyse." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Ajouter des chaînes de connexion 
La chaîne de connexion spécifie le nom d’utilisateur que SQL Insights doit utiliser lors de la connexion à SQL pour exécuter les vues de gestion dynamique. Si vous utilisez un Key Vault pour stocker le mot de passe de votre utilisateur d’analyse, indiquez l’URL et le nom du secret à utiliser. 

La chaîne de connexions varie en fonction de chaque type de ressource SQL :

#### <a name="azure-sql-databases"></a>Bases de données SQL Azure 
Entrer la chaîne de connexion dans le formulaire :

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Obtenez les détails de l’élément de menu **Chaînes de connexion** pour la base de données.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Chaîne de connexion à la base de données SQL" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Pour analyser un secondaire accessible en lecture, incluez la valeur de clé `ApplicationIntent=ReadOnly` dans la chaîne de connexion. SQL Insights prend en charge l’analyse d’un seul secondaire. Les données collectées seront marquées pour refléter le primaire ou le secondaire. 


#### <a name="azure-virtual-machines-running-sql-server"></a>Machines virtuelles Azure exécutant SQL Server 
Entrer la chaîne de connexion dans le formulaire :

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Si votre machine virtuelle d’analyse se trouve dans le même réseau virtuel, utilisez l’adresse IP privée du serveur.  Dans le cas contraire, utilisez l’adresse IP publique. Si vous utilisez une machine virtuelle Azure SQL, vous pouvez voir le port à utiliser ici sur la page **Sécurité** de la ressource.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Sécurité de la machine virtuelle SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::


### <a name="azure-sql-managed-instances"></a>Instances Azure SQL Managed Instance 
Entrer la chaîne de connexion dans le formulaire :

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Obtenez les détails de l’élément de menu **Chaînes de connexion** pour l’instance gérée.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="Chaîne de connexion SQL Managed Instance" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Pour analyser un secondaire accessible en lecture, incluez la valeur de clé `ApplicationIntent=ReadOnly` dans la chaîne de connexion. SQL Insights prend en charge l’analyse d’un seul secondaire, et les données collectées seront marquées pour refléter le primaire ou le secondaire. 


## <a name="monitoring-profile-created"></a>Création d’un profil d’analyse 

Sélectionnez **Ajouter une analyse de machine virtuelle** pour configurer la machine virtuelle afin de collecter les données de vos ressources SQL. Ne revenez pas à l’onglet **Vue d’ensemble**. Dans quelques minutes, la colonne État doit changer pour indiquer « En cours de collecte », et vous devez voir les données des ressources SQL que vous avez choisi d’analyser.

Si vous ne voyez pas les données, consultez [Résolution des problèmes liés à SQL Insights](sql-insights-troubleshoot.md) pour identifier le problème. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profil créé" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Si vous devez mettre à jour votre profil d’analyse ou les chaînes de connexion sur vos machines virtuelles d’analyse, vous pouvez le faire via l’onglet **Gérer le profil** de SQL Insights. Une fois que vos mises à jour ont été enregistrées, les modifications sont appliquées dans les cinq minutes.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Résolution des problèmes liés à SQL Insights](sql-insights-troubleshoot.md) si SQL Insights ne fonctionne pas correctement après avoir été activé.