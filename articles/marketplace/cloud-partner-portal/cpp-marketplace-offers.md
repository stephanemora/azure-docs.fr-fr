---
title: Offres des places de marché Azure et AppSource | Microsoft Docs
description: Création et gestion des offres des places de marché Azure et AppSource
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: ca4979188830fcb53732750a3eaadfc2009c4f9a
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658706"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Offres des places de marché Azure et AppSource

La première partie de cette section présente les opérations générales utilisées pour créer et gérer les offres sur les places de marché Azure et AppSource.  Cette partie fournit les informations de base que vous devez comprendre pour gérer des types d’offre spécifiques, ainsi que des informations techniques qui sont communes à tous les types d’offres.  La majorité de cette section contient des instructions détaillées sur la façon de créer et gérer des types d’offre spécifiques.  

La vidéo suivante présente les différentes fonctionnalités et les différents types d’offre disponibles dans la Place de marché Microsoft Azure ou AppSource.  Elle aborde également les techniques importantes et les aspects commerciaux de la publication d’une application ou service dans ces places de marché.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Génération d’applications et de services pour la Place de marché Microsoft Azure et AppSource - Build 2018**

Pour plus d’informations sur ces places de marché, consultez le [Guide de publication sur AppSource et la Place de marché Azure](../marketplace-publishers-guide.md).


## <a name="azure-marketplace-and-appsource-offer-types"></a>Types d’offre de la Place de marché Microsoft Azure et d’AppSource

Le tableau suivant répertorie les types d’offre actuels pris en charge par le [Portail Microsoft Cloud Partner](https://cloudpartner.azure.com).  Pour chaque type d’offre, il liste la ou les place(s) de marché où l’offre peut être référencée, ainsi qu’une description générale de la technologie de la solution d’offre.

|                Type d’offre                |  Marketplace  |   Description                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Application Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | La solution est composée d’une ou plusieurs machines virtuelles, d’un code Azure personnalisé facultatif, déployés par le biais d’un modèle Azure Resource Manager.  Le déploiement peut être effectué par le client au moyen d’un modèle de solution ou géré par l’éditeur. Ce type procure plus de souplesse que le type d’offre « machine virtuelle ».  |
| [Service de conseil](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Les deux | Les consultants agréés Microsoft peuvent annoncer leurs services spécifiques à un domaine sur la Place de marché Microsoft Azure ou AppSource.  Leur expertise aide les clients à évaluer leurs problèmes et à créer et à déployer des solutions adaptées à leurs objectifs métier.  |
| [Conteneur](./containers/cpp-containers-offer.md)  | Azure | La solution est une image conteneur Docker provisionnée en tant que service Kubernetes ou instances Azure Container. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Package qui étend ce système ERP (Enterprise Resource Planning) et de gestion d’entreprise. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Package qui étend ce système CRM (Customer Resource Management), par le biais de ses modules de ventes, de service, de service de projet et de service sur le terrain.  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Package qui étend ce service ERP (Enterprise Resource Planning) qui gère les aspects avancés de la finance, des opérations, de la fabrication et de la gestion de la chaîne logistique. |
| [Module IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Conteneur compatible Docker qui s’exécute sur un appareil IoT Edge.  Il contient de petits modules de calculs qui utilisent une combinaison de code personnalisé, d’autres services Azure et de services tiers. |
| [Application Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Package utilisant des flux de données pour relier les rapports et tableaux de bord aux données dans l’espace de stockage des données communes. |
| [Application SaaS](./saas-app/cpp-saas-offer.md) | Azure | La solution est un abonnement SaaS, gérée par l’éditeur, à laquelle les utilisateurs se connectent via une interface personnalisée qui tire parti d’Azure Active Directory. |
| [Machine virtuelle](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | La solution est contenue dans une seule machine virtuelle déployée sur l’abonnement du client.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Pour plus d’informations, consultez [Guide de publication par type d’offre](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Étapes suivantes

Vous allez découvrir, dans la rubrique [Gérer les offres](./manage-offers/cpp-manage-offers.md), les opérations générales que vous pouvez effectuer sur les offres de place de marché et leurs ressources et attributs techniques communs.
