---
title: Emplacements de données pour Azure Virtual Desktop - Azure
description: Brève vue d’ensemble des emplacements dans lesquels sont stockées des données et des métadonnées Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 06/08/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ef72214cf0a5a5d0d65cc13dba88a4776b4240ad
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745292"
---
# <a name="data-locations-for-azure-virtual-desktop"></a>Emplacements de données pour Azure Virtual Desktop

Azure Virtual Desktop est actuellement disponible pour tous les emplacements géographiques. Les administrateurs peuvent choisir l’emplacement de stockage des données utilisateur au moment de la création des machines virtuelles du pool d’hôtes et des services associés, par exemple les serveurs de fichiers. Découvrez plus en détail les zones géographiques Azure sur la [carte des centres de données Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft ne contrôle ni ne limite les régions où vos utilisateurs et vous pouvez accéder aux données utilisateur et aux données spécifiques de vos utilisateurs et applications.

>[!IMPORTANT]
>Azure Virtual Desktop stocke divers types d’information, tels que les noms de pool d’hôtes, de groupes d’applications, d’espace de travail et d’utilisateur principal dans un centre de données. Lors de la création d’un objet de service, le client doit entrer l’emplacement où l’objet doit être créé. L’emplacement de cet objet détermine où les informations relatives à l’objet sont stockées. Le client choisit une région Azure et les informations connexes sont stockées dans la zone géographique associée. Pour obtenir une liste de toutes les régions Azure et des zones géographiques associées, consultez [https://azure.microsoft.com/global-infrastructure/geographies/](https://azure.microsoft.com/global-infrastructure/geographies/).

Cet article décrit les informations stockées dans le service Azure Virtual Desktop. Pour en savoir plus sur les définitions de données client, consultez [Comment Microsoft classe les données pour les services en ligne](https://www.microsoft.com/trust-center/privacy/customer-data-definitions).

## <a name="customer-input"></a>Entrée client

Pour configurer le service Azure Virtual Desktop, le client doit créer des pools d’hôtes, ainsi que d’autres objets de service. Au cours de la configuration, le client doit fournir des informations, telles que les noms du pool d’hôtes, du groupe d’applications, etc. Ces informations sont considérées comme des entrées client. L’entrée client est stockée dans la zone géographique associée à la région dans laquelle l’objet est créé. Les chemins d’accès Azure Resource Manager aux objets sont considérés comme des informations organisationnelles, de sorte que la résidence des données ne s’applique pas à eux. Les données relatives aux chemins d’accès Azure Resource Manager sont stockées en dehors de la zone géographique choisie.

## <a name="customer-data"></a>Données client

Le service ne stocke pas directement les informations relatives aux utilisateurs ou aux applications, mais il stocke les données client, telles que les noms d’application et d’utilisateur principal, car ils font partie du processus de configuration des objets. Ces informations sont stockées dans la zone géographique associée à la région dans laquelle le client a créé l’objet.

## <a name="diagnostic-data"></a>Données de diagnostic

Azure Virtual Desktop recueille les données de diagnostic générées par le service chaque fois que le client ou l’utilisateur interagit avec le service. Ces données ne sont utilisées que pour la résolution de problèmes, la prise en charge et la vérification de l’intégrité du service sous forme agrégée. Par exemple, du côté de l’hôte de session, lorsqu’une machine virtuelle s’inscrit auprès du service, nous générons des informations qui incluent le nom de la machine virtuelle, le pool d’hôtes auquel la machine virtuelle appartient, etc. Ces informations sont stockées dans la zone géographique associée à la région dans laquelle le pool a été créé. En outre, lorsqu’un utilisateur se connecte au service et lance un bureau à distance, nous générons des informations de diagnostic qui incluent le nom d’utilisateur principal, l’emplacement du client, l’adresse IP du client, le pool d’hôtes auquel l’utilisateur se connecte, etc. Ces informations sont envoyées vers deux emplacements différents :

- L’emplacement le plus proche de l’utilisateur où l’infrastructure de service (suivis des clients, suivis utilisateur, données de diagnostic) est présente.
- L’emplacement où se trouve le pool d’hôtes.

## <a name="service-generated-data"></a>Données générées par le service

Pour garantir la fiabilité et l’évolutivité d’Azure Virtual Desktop, nous regroupons les modèles et l’utilisation du trafic afin de vérifier l’intégrité et les performances du plan de contrôle de l’infrastructure. Par exemple, pour comprendre comment accroître la capacité de l’infrastructure régionale à mesure que l’utilisation des services augmente, nous traiterons les données du journal d’utilisation du service. Nous examinons ensuite les journaux pendant les heures de pointe et nous décidons des centres de données à ajouter pour atteindre cette capacité. Nous regroupons ces informations à partir de tous les emplacements où se trouve l’infrastructure de service, puis nous les envoyons aux États-Unis. Les données envoyées aux États-Unis incluent des données modifiées, mais pas de données client.

Nous prenons actuellement en charge le stockage des données susmentionnées dans les emplacements suivants :

- États-Unis (US) (en disponibilité générale)
- Europe (EU) (en disponibilité générale)
- Royaume-Uni (UK) (préversion publique)

D’autres zones géographiques sont ajoutées au fur et à mesure du développement du service. Les informations stockées sont chiffrées au repos et des miroirs géoredondants sont conservés à l’intérieur de la zone géographique. Les données client, telles que les paramètres d’application et les données utilisateur, se trouvent à l’emplacement choisi par le client et ne sont pas gérées par le service.

Les données de contour sont répliquées dans la zone géographique Azure à des fins de récupération d’urgence.
