---
title: Services de données activés par Azure Arc – Notes de publication
description: Dernières notes de publication
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 5908083be4e6ed389b606754ffef41a4a371c3e3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930104"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notes de publication – Services de données activés par Azure Arc (préversion)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Septembre 2020

Les services de données activés par Azure Arc sont mis en production pour la préversion publique. Les services de données activés par Arc permettent de gérer les services de données en tout lieu.

- Instance managée SQL
- PostgreSQL Hyperscale

Pour obtenir des instructions, consultez [Présentation des services de données avec Azure Arc](overview.md)

## <a name="next-steps"></a>Étapes suivantes

> **Vous voulez juste essayer ?**  
> Démarrez rapidement avec [Démarrage rapide d'Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure.

[Installer les outils clients](install-client-tools.md)

[Créer le contrôleur de données Azure Arc](create-data-controller.md) (nécessite l'installation préalable des outils clients)

[Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md) (nécessite la création préalable d'un contrôleur de données Azure Arc)

[Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) (nécessite la création préalable d'un contrôleur de données Azure Arc)

## <a name="known-limitations-and-issues"></a>Limites et problèmes connus

- Les noms d’instance gérée SQL ne peuvent pas dépasser 13 caractères
- Aucune mise à niveau sur place du contrôleur de données ou des instances de base de données Azure Arc.
- Les images conteneurs de services de données activés par Arc ne sont pas signées.  Il se peut que vous deviez configurer vos nœuds Kubernetes pour permettre l’extraction d’images de conteneur non signées.  Par exemple, si vous utilisez Docker en tant que runtime de conteneurs, vous pouvez définir la variable d’environnement DOCKER_CONTENT_TRUST=0 et redémarrer.  D’autres runtimes de conteneurs offrent des options similaires, par exemple dans [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Impossible de créer des instances gérées SQL activées par Azure Arc, ou des groupes de serveurs PostgreSQL Hyperscale à partir du portail Azure.
- Pour le moment, si vous utilisez NFS, vous devez définir allowRunAsRoot sur true dans votre fichier de profil de déploiement avant de créer le contrôleur de données Azure Arc.
- Authentification de connexion SQL et PostgreSQL uniquement.  Aucune prise en charge d’Azure Active Directory ou d’Active Directory.
- La création d’un contrôleur de données sur OpenShift impose des contraintes de sécurité peu exigeantes.  Pour plus d'informations, reportez-vous à la documentation.
- La _réduction d’échelle_ du nombre de nœuds Worker Postgres Hyperscale n’est pas prise en charge.
- Si vous utilisez un moteur Azure Kubernetes Service (moteur AKS) sur Azure Stack Hub avec un contrôleur de données Azure Arc et des instances de base de données, la mise à niveau vers une version plus récente de Kubernetes n’est pas prise en charge. Avant de mettre à niveau le cluster Kubernetes, désinstallez le contrôleur de données Azure Arc et toutes les instances de base de données.
- La préversion ne prend pas en charge la sauvegarde/restauration pour la version 11 du moteur Postgres. Elle prend uniquement en charge la sauvegarde/restauration pour la version 12 de Postgres.
