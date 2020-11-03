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
ms.openlocfilehash: 3c20bbd3ab02cd1eccd00e2d36c14eebf2f63205
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360301"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notes de publication – Services de données activés par Azure Arc (préversion)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Septembre 2020

Les services de données activés par Azure Arc sont mis en production pour la préversion publique. Les services de données activés par Arc permettent de gérer les services de données en tout lieu.

- Instance managée SQL
- PostgreSQL Hyperscale

Pour obtenir des instructions, consultez [Présentation des services de données avec Azure Arc](overview.md)

### <a name="known-issues"></a>Problèmes connus

Les règles s’appliquant à cette version sont les suivantes :

* **Suppression du groupe de serveurs Hyperscale PostgreSQL**  : Si vous avez modifié la configuration de votre groupe de serveurs ou de votre instance, attendez la fin de l’opération de modification avant de supprimer un groupe de serveurs Hyperscale PostgreSQL.

* **`azdata notebook run` peut échouer**  : Pour résoudre ce problème, exécutez `azdata notebook run` dans un environnement virtuel Python. Ce problème se manifeste également dans un échec de tentative de création d’une instance gérée SQL ou d’un groupe de serveurs Hyperscale PostgreSQL à l’aide de l’Assistant Déploiement d’Azure Data Studio. Dans ce cas, vous pouvez ouvrir le bloc-notes et cliquer sur le bouton **Exécuter tout** en haut du bloc-notes.

## <a name="next-steps"></a>Étapes suivantes

> **Vous voulez juste essayer ?**  
> Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure.

[Installer les outils clients](install-client-tools.md)

[Créer le contrôleur de données Azure Arc](create-data-controller.md) (nécessite l’installation préalable des outils clients)

[Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)

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
- Pour les clusters Azure Kubernetes Service (AKS) qui s'étendent sur [plusieurs zones de disponibilité](../../aks/availability-zones.md), les services de données avec Azure Arc ne sont actuellement pas pris en charge. Pour contourner ce problème, lorsque vous créez le cluster AKS sur le portail Azure, si vous sélectionnez une région dans laquelle des zones sont disponibles, effacez toutes les zones du contrôle de sélection. Consultez le graphique suivant :

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Décochez les cases des différentes zones pour n'en spécifier aucune.":::

  
