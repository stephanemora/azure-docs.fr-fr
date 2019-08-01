---
title: Avis de migration de passerelle pour Azure SQL Database de la génération 2 vers la génération 3 | Microsoft Docs
description: Cet article informe les utilisateurs de la migration des adresses IP des passerelles Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568109"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migration du trafic des bases de données Azure SQL Database vers des passerelles plus récentes

Comme l’infrastructure Azure évolue, Microsoft actualise régulièrement le matériel, afin de proposer la meilleure expérience client possible. Dans les prochains mois, nous prévoyons d’ajouter des passerelles basées sur des générations de matériel plus récentes et de mettre hors service les passerelles basées sur du matériel plus ancien dans certaines régions.  

Les clients seront informés très tôt de toute modification apportée aux passerelles disponibles dans chaque région, par e-mail et via le portail Microsoft Azure. Les informations les plus récentes seront conservées dans la table [Adresses IP des passerelles Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses).

## <a name="impact-of-this-change"></a>Portée de ce changement

La première série de désaffectations de passerelles est prévue pour le 1er septembre 2019 dans les régions suivantes :

- USA Ouest
- Europe Ouest
- East US
- USA Centre
- Asie Sud-Est
- USA Centre Sud
- Europe Nord
- USA Centre Nord
- Japon Ouest
- Japon Est
- USA Est 2
- Asie Est

L’adresse IP désaffectée cessera d’accepter le trafic, et les nouvelles tentatives de connexion seront acheminées vers l’une des passerelles de la région.

Utilisateurs qui ne sont pas affectés par cette modification :

- Les clients qui utilisent la redirection dans le cadre de leur stratégie de connexion ne verront aucun changement.
- Les connexions à la base de données SQL à partir de Microsoft Azure et l’utilisation des balises de service ne seront pas affectées.
- Il en va de même pour les connexions effectuées à l’aide des versions prises en charge de Microsoft JDBC Driver pour SQL Server. Pour les versions prises en charge du pilote JDBC, voir [Télécharger Microsoft JDBC Driver pour SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Que faire si vous êtes concerné

Nous vous recommandons d’autoriser le trafic sortant vers les adresses IP pour l’ensemble des [adresses IP de passerelles Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) dans la région sur le port TCP 1433, et sur la plage de ports 11 000-11 999 pour votre pare-feu. Pour en savoir plus sur les plages de ports, voir [Stratégie de connexion](sql-database-connectivity-architecture.md#connection-policy).

La validation des certificats risque d’échouer pour les connexions établies à partir d’applications à l’aide d’une version de Microsoft JDBC Driver antérieure à 4.0. Les versions inférieures de Microsoft JDBC s’appuient sur un nom courant (CN) dans le champ d’objet du certificat. L’atténuation consiste à s’assurer que la propriété hostNameInCertificate est définie sur *.database.windows.net. Pour savoir comment définir la propriété hostNameInCertificate, voir [Connexion avec chiffrement SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Si l’atténuation ci-dessus ne fonctionne pas, envoyez une demande de support concernant SQL Database, à l’aide de l’URL suivante : https://aka.ms/getazuresupport

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’architecture de la connectivité Azure SQL](sql-database-connectivity-architecture.md)