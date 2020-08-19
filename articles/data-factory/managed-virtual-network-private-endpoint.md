---
title: Réseau virtuel managé et points de terminaison privés managés
description: Découvrez le réseau virtuel managé et les points de terminaison privés managés dans Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: c0f23c864430b6cb2f49f924d5aaa8bde296037c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135961"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Réseau virtuel managé Azure Data Factory (préversion)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit le réseau virtuel managé et les points de terminaison privés managés dans Azure Data Factory.


## <a name="managed-virtual-network"></a>Réseau virtuel managé

Quand vous créez un runtime Azure Integration Runtime au sein d’un réseau virtuel managé Azure Data Factory, ce runtime est provisionné avec le réseau virtuel managé et il tire parti des points de terminaison privés pour se connecter en toute sécurité aux magasins de données pris en charge. 

La création d’un runtime Azure Integration Runtime au sein d’un réseau virtuel managé garantit l’isolation et la sécurisation du processus d’intégration des données. 

Avantages de l’utilisation du réseau virtuel managé :

- Avec un réseau virtuel managé, vous pouvez déplacer la charge liée à la gestion du réseau virtuel vers Azure Data Factory. Vous n’avez pas besoin de créer un sous-réseau pour Azure Integration Runtime qui pourrait finalement utiliser de nombreuses adresses IP privées à partir de votre réseau virtuel et nécessiter une planification préalable de l’infrastructure réseau. 
- Il ne demande pas de connaissances approfondies en réseau Azure pour effectuer des intégrations de données de façon sécurisée. Au contraire, le démarrage avec l’opération ETL sécurisée est très simplifié pour les ingénieurs de données. 
- Le réseau virtuel managé ainsi que les points de terminaison privés managés assurent une protection contre l’exfiltration des données. 

> [!IMPORTANT]
>Actuellement, le réseau virtuel managé est uniquement pris en charge dans la même région qu’Azure Data Factory.
 

![Architecture de réseau virtuel managé ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Points de terminaison privés managés

Les points de terminaison privés managés sont des points de terminaison privés créés sur le réseau virtuel managé Azure Data Factory qui établissent une liaison privée vers des ressources Azure. Azure Data Factory gère ces points de terminaison privés à votre place. 

![Nouveau point de terminaison privé managé](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory prend en charge les liaisons privées. Une liaison privée vous permet d’accéder aux services Azure (PaaS), tels que le Stockage Azure, Azure Cosmos DB, Azure Synapse Analytics (anciennement Azure SQL Data Warehouse).

Quand vous utilisez une liaison privée, le trafic entre vos magasins de données et le réseau virtuel managé transite intégralement par le réseau principal de Microsoft. Une liaison privée assure une protection contre les risques liés à l’exfiltration des données. Vous établissez une liaison privée vers une ressource en créant un point de terminaison privé.

Le point de terminaison privé utilise une adresse IP privée sur le réseau virtuel managé pour y placer de fait le service. Les points de terminaison privés sont mappés à une ressource spécifique dans Azure, et non à l’ensemble du service. Les clients peuvent limiter la connectivité à une ressource spécifique approuvée par leur organisation. Apprenez-en davantage sur [les liaisons privées et les points de terminaison privés](https://docs.microsoft.com/azure/private-link/).

> [!NOTE]
> Nous vous recommandons de créer des points de terminaison privés managés pour vous connecter à toutes vos sources de données Azure. 
 
> [!WARNING]
> Si un magasin de données PaaS (objets blob, ADLS Gen2, SQL DW) a un point de terminaison privé déjà créé, et même s’il autorise l’accès à partir de tous les réseaux, ADF peut uniquement y accéder à l’aide d’un point de terminaison privé managé. Veillez à créer un point de terminaison privé dans de tels scénarios. 

Une connexion de point de terminaison privé est créée dans un état « en attente » quand vous créez un point de terminaison privé managé dans Azure Data Factory. Un workflow d’approbation est lancé. Le propriétaire de la ressource de liaison privée est responsable de l’approbation ou du refus de la connexion.

![Point de terminaison privé managé](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Si le propriétaire approuve la connexion, la liaison privée est établie. S’il la refuse, la liaison privée n’est pas établie. Dans les deux cas, le point de terminaison privé managé est mis à jour avec l’état de la connexion.

![Approuver le point de terminaison privé managé](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Seule une instance de point de terminaison privé managé dans un état approuvé peut envoyer du trafic vers une ressource de liaison privée donnée.

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus
### <a name="supported-data-sources"></a>Sources de données prises en charge
Les sources de données suivantes sont prises en charge pour établir une connexion via une liaison privée à partir d’un réseau virtuel managé ADF.
- Stockage Blob Azure
- Stockage Table Azure
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (sans Azure SQL Managed Instance)
- Azure Synapse Analytics (anciennement Azure SQL Data Warehouse)
- SQL Azure Cosmos DB
- Azure Key Vault
- Azure Private Link

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Communications sortantes via un point de terminaison public à partir du réseau virtuel managé ADF
- Seul le port 443 est ouvert pour les communications sortantes.
- Le Stockage Azure et Azure Data Lake Gen2 ne sont pas pris en charge pour une connexion via un point de terminaison public à partir du réseau virtuel managé ADF.

### <a name="other-known-issues"></a>Autres problèmes connus
L’exécution du débogage pour la connectivité Cosmos DB ne fonctionne pas, notamment le débogage de dataflow et le débogage du pipeline.


## <a name="next-steps"></a>Étapes suivantes

- Tutoriel : [Générer un pipeline de copie à l’aide d’un réseau virtuel managé et de points de terminaison privés](tutorial-copy-data-portal-private.md) 
- Tutoriel : [Générer un pipeline de dataflow de mappage à l’aide d’un réseau virtuel managé et de points de terminaison privés](tutorial-data-flow-private.md)
