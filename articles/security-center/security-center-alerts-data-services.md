---
title: Détection des menaces pour les services de données dans Azure Security Center | Microsoft Docs
description: Cette rubrique présente les alertes des services de données disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: f33b69ac443a1bb8f6b7d6e1b19f2f077bf38f58
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501481"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Détection des menaces pour les services de données dans Azure Security Center

 Security Center analyse les journaux des services de stockage de données et déclenche des alertes lorsqu’il détecte une menace pour vos ressources de données. Cette rubrique répertorie les alertes générées par Security Center pour les services suivants :

* [Azure SQL Database et SQL Data Warehouse](#data-sql)
* [Stockage Azure](#azure-storage)
* [Cosmos DB](#cosmos-db)

## Azure SQL Database et SQL Data Warehouse <a name="data-sql"></a>

SQL Threat Detection détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à utiliser des bases de données ou à exploiter leurs failles de sécurité. Security Center analyse les journaux d’audit SQL et s’exécute en mode natif dans le moteur SQL.

|Alerte|Description|
|---|---|
|**Vulnérabilité par injection de code SQL**|Une application a généré une instruction SQL défectueuse dans la base de données. Cela peut indiquer une vulnérabilité aux attaques par injection de code SQL. Deux raisons possibles expliquent la génération d’une instruction défectueuse : Un défaut dans le code d’application qui crée l’instruction SQL défectueuse. Le code de l’application ou des procédures stockées qui n'ont pas assaini l’entrée utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par les injections SQL.|
|**Injection potentielle de code SQL**|Une attaque active a eu lieu contre une application identifiée vulnérable aux injections SQL. Cela signifie qu'un attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.|
|**Accès à partir d’un emplacement inhabituel**|Le modèle d’accès au serveur SQL a changé car quelqu'un s’est connecté au serveur SQL à partir d’un emplacement géographique inhabituel. Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).|
|**Accès à partir d’une entité de sécurité inconnue**|Le modèle d’accès au serveur SQL a changé. Quelqu'un s’est connecté au serveur SQL à l'aide d'un principal inhabituel (utilisateur SQL). Dans certains cas, l’alerte détecte une action légitime (nouvelle application, opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).|
|**Accès à partir d’une application potentiellement dangereuse**|Une application potentiellement dangereuse a été utilisée pour accéder à la base de données. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque à l’aide d’outils d’attaque courants.|
|**Informations d’identification SQL par force brute**|Un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes a eu lieu. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque par force brute.|

Pour plus d’informations sur les alertes SQL Threat Detection, consultez [Détection des menaces sur Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview), et plus particulièrement la section relative aux alertes de détection des menaces. Consultez également [Comment Azure Security Center peut révéler une cyberattaque](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) pour découvrir comment Security Center peut utiliser la détection d'activités SQL malveillantes pour détecter une attaque.

## Stockage Azure<a name="azure-storage"></a>

>[!NOTE]
> Advanced Threat Protection pour Stockage Azure n’est actuellement disponible que pour le Stockage Blob uniquement.

Advanced Threat Protection pour Stockage Azure offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni devoir gérer des systèmes de supervision de la sécurité.

Security Center analyse les journaux de diagnostic des requêtes de lecture, écriture et suppression liées au stockage d’objets blob pour détecter les menaces, et déclenche des alertes en cas d'anomalies. Pour plus d’informations, consultez [Configurer la journalisation Storage Analytics](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Anomalies d'accès inhabituel à l'emplacement**|L’analyse d’un échantillon du trafic réseau a détecté une communication RDP (Remote Desktop Protocol) sortante anormale à partir d’une ressource dans votre déploiement. Cette activité est considérée comme anormale pour cet environnement et peut indiquer que votre ressource a été compromise et qu’elle est désormais utilisée pour mener une attaque par force brute sur le point de terminaison RDP externe. Notez que ce type d’activité peut entraîner le marquage de votre adresse IP par des entités externes comme étant malveillante.|
|**Anomalie d'accès aux applications**|Indique qu’une application inhabituelle a accédé à ce compte de stockage. Il est possible qu'un attaquant ait accédé à votre compte de stockage à l’aide d’une nouvelle application.|
|**Anomalie d'accès anonyme**|Indique un changement dans le modèle d’accès à un compte de stockage. Par exemple, le compte a fait l’objet d’un accès anonyme (sans authentification), ce qui est inattendu comparé au modèle d’accès récent à ce compte. Il est possible qu'un attaquant ait exploité un accès en lecture public pour un conteneur contenant des objets blob.|
|**Anomalie d'exfiltration de données**|Indique qu’une quantité anormalement élevée de données a été extraite par rapport à l’activité récente sur ce conteneur de stockage. Il est possible qu'un attaquant ait extrait une grande quantité de données à partir d’un conteneur contenant des objets blob.|
|**Anomalie de suppression inattendue**|Indique qu’une ou plusieurs opérations de suppression inattendues se sont produites dans un compte de stockage, par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant ait supprimé des données à partir de votre compte de stockage.|
|**Chargement de package de service cloud Azure**|Indique qu’un package de service cloud Azure (fichier .cspkg) a été chargé dans un compte de stockage de façon inhabituelle, par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant se prépare à déployer un code malveillant à partir de votre compte de stockage vers un service cloud Azure.|
|**Anomalie d'autorisation d'accès**|Indique que les autorisations d’accès de ce conteneur de stockage ont été modifiées de façon inhabituelle. Il est possible qu'un attaquant ait modifié les autorisations de conteneur pour affaiblir son état de sécurité ou s'y installer.|
|**Anomalie d'accès à l'inspection**|Indique que les autorisations d’accès d’un compte de stockage ont été inspectées de façon inhabituelle, par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant ait effectué une reconnaissance en vue d’une attaque future.|
|**Anomalie d'exploration des données**|Indique que les objets blob ou les conteneurs dans un compte de stockage ont été énumérés de manière inhabituelle, par rapport à l’activité récente sur ce compte. Il est possible qu'un attaquant ait effectué une reconnaissance en vue d’une attaque future.|

>[!NOTE]
>Advanced Threat Protection pour Stockage Azure n’est actuellement pas disponible dans les régions de cloud souverain et Azure Government.

Pour plus d’informations sur les alertes relatives au stockage, consultez l'article [Advanced Threat Protection pour le stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection), et plus particulièrement la section Alertes de protection.

## Cosmos DB<a name="cosmos-db"></a>

Les alertes suivantes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos DB :

|Alerte|Description|
|---|---|
|**Accès à partir d’un emplacement inhabituel**|Indique un changement dans le modèle d’accès à un compte Cosmos DB. Quelqu’un a accédé à ce compte à partir d’une adresse IP inconnue, par rapport à l’activité récente. Soit une personne malveillante a accédé à un compte Cosmos DB, soit un utilisateur légitime a accédé au compte Cosmos DB à partir d’un emplacement géographique nouveau et inhabituel. Par exemple : une nouvelle maintenance d’application ou de développeur à distance.|
|**Exfiltration de données inhabituelle**|Indique un changement dans le modèle d’extraction des données d’un compte Cosmos DB. Une personne a extrait une quantité inhabituelle de données par rapport à l’activité récente. Ou une personne malveillante a extrait une grande quantité de données d’une base de données Cosmos DB. Par exemple : exfiltration/fuite de données, transfert de données non autorisé. Ou un utilisateur ou une application légitime a extrait une quantité inhabituelle de données d’un conteneur. Par exemple : activité de sauvegarde de maintenance.|

Pour plus d’informations, consultez [Advanced Threat Protection pour Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).