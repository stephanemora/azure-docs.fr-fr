---
title: Détection des menaces pour les services de données dans Azure Security Center | Microsoft Docs
description: Cet article présente les alertes des services de données disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 8b99d89e8895cd1c8d8e9fe3961f0db9855fb7ee
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196118"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Détection des menaces pour les services de données dans Azure Security Center

 Azure Security Center analyse les journaux des services de stockage de données, et déclenche des alertes lorsqu’il détecte une menace pour vos ressources de données. Cet article répertorie les alertes générées par Security Center pour les services suivants :

* [Azure SQL Database et Azure SQL Data Warehouse](#data-sql)
* [Stockage Azure](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database et SQL Data Warehouse <a name="data-sql"></a>

SQL Threat Detection identifie les activités anormales qui indiquent des tentatives inhabituelles ou potentiellement dangereuses visant à utiliser des bases de données ou à exploiter leurs failles de sécurité. 

|Alerte|Description|
|---|---|
|**Vulnérabilité possible par injection de code SQL**|Une application a généré une instruction SQL défectueuse dans la base de données. Cela peut indiquer une vulnérabilité aux attaques par injection de code SQL. Il y a deux causes possibles à une instruction défectueuse. Un défaut dans le code d’application peut avoir créé l’instruction SQL défectueuse. Ou bien, le code d’application ou les procédures stockées n’ont pas assaini les entrées utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par l’injection de code SQL.|
|**Injection potentielle de code SQL**|Une attaque active a eu lieu contre une application identifiée vulnérable aux injections SQL. Cela signifie qu’un attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.|
|**Ouverture de session à partir d’un emplacement inhabituel**|Le modèle d’accès à SQL Server a changé, car un utilisateur s’est connecté au serveur à partir d’un emplacement géographique inhabituel. Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé ou attaquant externe).|
|**Connexion à partir d’un principal inhabituel**|Le modèle d’accès à SQL Server a changé. En effet, un utilisateur s’est connecté au serveur avec un principal inhabituel (utilisateur). Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé ou attaquant externe).|
|**Tentative de connexion par une application potentiellement dangereuse**|Une application potentiellement dangereuse a été utilisée pour accéder à la base de données. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque qui utilise des outils courants.|
|**Tentative de force brute SQL potentielle**|Il y a eu un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque par force brute.|

Pour plus d’informations sur les alertes SQL Threat Detection, consultez [Détection des menaces Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). Examinez tout particulièrement la section relative aux alertes de détection des menaces. Consultez également [Comment Azure Security Center peut révéler une cyberattaque](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) pour avoir un exemple de la façon dont Security Center utilise la détection d’activités SQL malveillantes pour détecter une attaque.

## Stockage Azure <a name="azure-storage"></a>

>[!NOTE]
> Advanced Threat Protection pour le stockage est actuellement disponible pour le stockage Blob uniquement.

Advanced Threat Protection pour le stockage offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité, mais aussi de gérer des systèmes de supervision de la sécurité.

Security Center analyse les journaux de diagnostic des requêtes de lecture, écriture et suppression liées au stockage d’objets blob pour détecter les menaces, et déclenche des alertes en cas d'anomalies. Pour plus d’informations, consultez [Configurer la journalisation Storage Analytics](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Accès à partir d’un emplacement inhabituel dans un compte de stockage**|Indique un changement dans le modèle d’accès à un compte Stockage Azure. Quelqu’un a accédé à ce compte à partir d’une adresse IP considérée comme peu familière par rapport à l’activité récente. Soit un attaquant a obtenu l’accès au compte, soit un utilisateur légitime s’est connecté à partir d’un emplacement géographique nouveau ou inhabituel. Dans le deuxième cas, il peut s’agir, par exemple, d’une opération de maintenance à distance effectuée par une nouvelle application ou un nouveau développeur.|
|**Une application inhabituelle a accédé à un compte de stockage**|Indique qu’une application inhabituelle a accédé à ce compte de stockage. Un attaquant a peut-être accédé à votre compte de stockage à l’aide d’une nouvelle application.|
|**Accès anonyme à un compte de stockage**|Indique un changement dans le modèle d’accès à un compte de stockage. Par exemple, le compte a fait l’objet d’un accès anonyme (sans authentification), ce qui est inattendu comparé au modèle d’accès récent à ce compte. Un attaquant a peut-être exploité un accès en lecture public à un conteneur de stockage d’objets blob.|
|**Accès à partir d’un nœud de sortie TDR dans un compte de stockage**|Indique que ce compte a fait l’objet d’un accès à partir d’une adresse IP connue comme étant un nœud de sortie actif de Tor (proxy d’anonymisation). La gravité de cette alerte considère le type d’authentification utilisé (le cas échéant) et le fait qu’il s’agit ou non du premier cas de ce type d’accès. Un attaquant ou un utilisateur légitime a peut-être accédé à votre compte de stockage à l’aide de Tor.|
|**Quantité inhabituelle de données extraites d’un compte de stockage**|Indique qu’une quantité anormalement élevée de données a été extraite par rapport à l’activité récente sur ce conteneur de stockage. Un attaquant a peut-être extrait une grande quantité de données à partir d’un conteneur de stockage d’objets blob.|
|**Suppression inhabituelle dans un compte de stockage**|Indique qu’une ou plusieurs opérations de suppression inattendues se sont produites dans un compte de stockage, par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant ait supprimé des données à partir de votre compte de stockage.|
|**Chargement inhabituel de fichier .cspkg dans un compte de stockage**|Indique qu’un package Azure Cloud Services (fichier .cspkg) a été chargé dans un compte de stockage de façon inhabituelle par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant se prépare à déployer un code malveillant à partir de votre compte de stockage vers un service cloud Azure.|
|**Modification inhabituelle des autorisations d’accès dans un compte de stockage**|Indique que les autorisations d’accès de ce conteneur de stockage ont été modifiées de façon inhabituelle. Un attaquant a peut-être changé les autorisations du conteneur pour affaiblir son état de sécurité ou y accéder de façon persistante.|
|**Inspection d’accès inhabituelle dans un compte de stockage**|Indique que les autorisations d’accès d’un compte de stockage ont été inspectées de façon inhabituelle, par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant ait effectué une reconnaissance en vue d’une attaque future.|
|**Exploration de données inhabituelle dans un compte de stockage**|Indique que les objets blob ou les conteneurs dans un compte de stockage ont été énumérés de manière inhabituelle, par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant ait effectué une reconnaissance en vue d’une attaque future.|
|**PRÉVERSION - Programme potentiellement malveillant chargé vers un compte de stockage**|Indique qu’un objet blob contenant des programmes malveillants potentiels a été chargé dans un compte de stockage. Les causes potentielles peuvent inclure le chargement d’un programme malveillant intentionnel par une personne malveillante ou le chargement non intentionnel d’un objet blob potentiellement malveillant par un utilisateur légitime.|

>[!NOTE]
>Advanced Threat Protection pour le stockage n’est actuellement pas disponible dans les régions de cloud souverain et Azure Government.

Pour plus d’informations sur les alertes de stockage, consultez [Advanced Threat Protection pour le stockage Azure](../storage/common/storage-advanced-threat-protection.md). Examinez tout particulièrement la section relative aux alertes de protection.

## Azure Cosmos DB<a name="cosmos-db"></a>

Les alertes suivantes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos DB :

|Alerte|Description|
|---|---|
|**Accès à partir d’un emplacement inhabituel dans un compte Cosmos DB**|Indique un changement dans le modèle d’accès à un compte Azure Cosmos DB. Quelqu’un a accédé à ce compte à partir d’une adresse IP inconnue, par rapport à l’activité récente. Soit un attaquant a accédé au compte, soit un utilisateur légitime a accédé au compte à partir d’un emplacement géographique nouveau et inhabituel. Dans le deuxième cas, il peut s’agir, par exemple, d’une opération de maintenance à distance effectuée par une nouvelle application ou un nouveau développeur.|
|**Quantité inhabituelle de données extraites d’un compte Cosmos DB**|Indique un changement dans le modèle d’extraction des données à partir d’un compte Azure Cosmos DB. Une personne a extrait une quantité inhabituelle de données par rapport à l’activité récente. Il est possible qu’un attaquant ait extrait une grande quantité de données à partir d’une base de données Azure Cosmos DB (par exemple, une exfiltration ou fuite de données, ou un transfert de données non autorisé). Il est possible aussi qu’un utilisateur ou une application légitime ait extrait une quantité inhabituelle de données à partir d’un conteneur (par exemple, pour l’activité de sauvegarde de maintenance).|

Pour plus d’informations, consultez [Advanced Threat Protection pour Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).
