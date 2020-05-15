---
title: Régions prenant en charge les Zones de disponibilité dans Azure
description: Pour créer des applications hautement disponibles et résilientes dans Azure, les zones de disponibilité fournissent des emplacements physiquement distincts que vous pouvez utiliser pour exécuter vos ressources.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: b181316ac2c6998676a17589fff246cf88ff50c2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123800"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Régions prenant en charge les Zones de disponibilité dans Azure

Les Zones de disponibilité constituent une offre à haute disponibilité qui protège vos applications et données contre les pannes des centres de données. Pour plus d’informations sur les Zones de disponibilité, consultez [Régions et Zones de disponibilité dans Azure](az-overview.md).

## <a name="services-support-by-region"></a>Prise en charge des services par région

Cette section répertorie les régions et services Azure qui prennent en charge les Zones de disponibilité.

Pour voir les services disponibles dans chaque région, ainsi que la feuille de route à venir pour la disponibilité, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

|                                 |Amérique |              |           |           | Europe |              |          |              | Asie-Pacifique |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |USA Centre|USA Est|USA Est 2|USA Ouest 2|France Centre|Europe Nord|Sud du Royaume-Uni|Europe Ouest|Japon Est|Asie Sud-Est|Australie Est|
| **Calcul**                         |            |              |           |           |                |              |          |             |            |                |                |
| Machines virtuelles Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Machines virtuelles Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| ILB Azure App Service Environments | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Stockage**   |            |              |           |           |                |              |          |             |            |                |                |
| Disques managés                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Stockage redondant interzone          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Mise en réseau**                     |            |              |           |           |                |              |          |             |            |                |                |
| Adresse IP standard        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Passerelle VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Passerelle ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Application Gateway (V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Pare-feu Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Bases de données**                     |            |              |           |           |                |              |          |             |            |                |                |
| Explorateur de données Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;(Préversion) | &#10003;       | &#10003;(Préversion)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Cache Azure pour Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB      | &#10003;   |  &#10003;  |  &#10003; | &#10003; |  &#10003;  | &#10003;    | &#10003; |  &#10003;   |     &#10003;       | &#10003;    | &#10003;    |
| **Analyse**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Intégration**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (Niveau Premium uniquement) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identité**                     |            |              |           |           |                |              |          |             |            |                |                |
| Services de domaine Azure AD | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |


## <a name="next-steps"></a>Étapes suivantes

- [Régions et Zones de disponibilité dans Azure](az-overview.md)
