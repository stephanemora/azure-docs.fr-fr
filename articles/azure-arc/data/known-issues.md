---
title: Problèmes connus des services de données avec Azure Arc
description: Problèmes connus les plus récents
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: d4667e8fa3a5624dddc3cb0dd792fc73ea812332
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692857"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Problèmes connus – Services de données avec Azure Arc (préversion)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Février 2021


- Le mode de cluster connecté est désactivé
- L’hyperscale PostgreSQL avec Azure Arc activé retourne un message d’erreur incorrect lorsqu’il ne peut pas effectuer une restauration jusqu’au point relatif dans le temps que vous indiquez. Par exemple, si vous avez spécifié un instant dans le passé pour la restauration qui est antérieur aux éléments que contiennent vos sauvegardes, la restauration échoue avec un message d’erreur tel que : `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`. Dans ce cas, redémarrez la commande après avoir donné un point dans le temps qui se situe dans la plage de dates pour lesquelles vous avez des sauvegardes. Pour déterminer cette plage, répertoriez vos sauvegardes et examinez les dates auxquelles elles ont été effectuées.
- Un ID de sauvegarde est requis lors d’une restauration complète. Par défaut, si vous n’indiquez pas d’ID de sauvegarde, la sauvegarde la plus récente sera utilisée. Cela ne fonctionne pas dans cette version.

## <a name="introduced-prior-to-february-2021"></a>Introduit avant février 2021

### <a name="data-controller"></a>Contrôleur de données

- Sur Azure Kubernetes service (AKS), la version 1.19. x de Kubernetes n’est pas prise en charge.
- Sur Kubernetes 1.19, `containerd` n’est pas pris en charge.
- La ressource de contrôleur de données dans Azure est actuellement une ressource Azure. Toutes les mises à jour telles que les suppressions ne sont pas propagées vers le cluster kubernetes.
- Les noms d’instance ne peuvent pas dépasser 13 caractères
- Aucune mise à niveau sur place du contrôleur de données ou des instances de base de données Azure Arc.
- Les images conteneurs de services de données activés par Arc ne sont pas signées.  Il se peut que vous deviez configurer vos nœuds Kubernetes pour permettre l’extraction d’images de conteneur non signées.  Par exemple, si vous utilisez Docker en tant que runtime de conteneurs, vous pouvez définir la variable d’environnement DOCKER_CONTENT_TRUST=0 et redémarrer.  D’autres runtimes de conteneurs offrent des options similaires, par exemple dans [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Impossible de créer des instances gérées SQL activées par Azure Arc, ou des groupes de serveurs PostgreSQL Hyperscale à partir du portail Azure.
- Pour le moment, si vous utilisez NFS, vous devez définir `allowRunAsRoot` sur `true` dans votre fichier de profil de déploiement avant de créer le contrôleur de données Azure Arc.
- Authentification de connexion SQL et PostgreSQL uniquement.  Aucune prise en charge d’Azure Active Directory ou d’Active Directory.
- La création d’un contrôleur de données sur OpenShift impose des contraintes de sécurité peu exigeantes.  Pour plus d'informations, reportez-vous à la documentation.
- Si vous utilisez Azure Kubernetes Service (AKS) sur Azure Stack Hub avec un contrôleur de données Azure Arc et des instances de base de données, la mise à niveau vers une version plus récente de Kubernetes n’est pas prise en charge. Avant de mettre à niveau le cluster Kubernetes, désinstallez le contrôleur de données Azure Arc et toutes les instances de base de données.
- Pour les clusters AKS qui s'étendent sur [plusieurs zones de disponibilité](../../aks/availability-zones.md), les services de données avec Azure Arc ne sont actuellement pas pris en charge. Pour contourner ce problème, lorsque vous créez le cluster AKS sur le portail Azure, si vous sélectionnez une région dans laquelle des zones sont disponibles, effacez toutes les zones du contrôle de sélection. Consultez le graphique suivant :

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Décochez les cases des différentes zones pour n'en spécifier aucune.":::

### <a name="postgresql"></a>PostgreSQL

- L’hyperscale PostgreSQL avec Azure Arc activé retourne un message d’erreur incorrect lorsqu’il ne peut pas effectuer une restauration jusqu’au point relatif dans le temps que vous indiquez. Par exemple, si vous avez spécifié un instant dans le passé pour la restauration qui est antérieur aux éléments que contiennent vos sauvegardes, la restauration échoue avec un message d’erreur tel que : `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
Dans ce cas, redémarrez la commande après avoir donné un point dans le temps qui se situe dans la plage de dates pour lesquelles vous avez des sauvegardes. Vous allez déterminer cette plage en répertoriant vos sauvegardes et en observant les dates auxquelles elles ont été faites.
- La limite de restauration dans le temps n’est prise en charge que sur les groupes de serveurs. Le serveur cible d’une opération de restauration à un point dans le temps ne peut pas être le serveur à partir duquel vous avez effectué la sauvegarde. Il doit s’agir d’un groupe de serveurs différent. Toutefois, la restauration complète est prise en charge pour le même groupe de serveurs.
- Un ID de sauvegarde est requis lors d’une restauration complète. Par défaut, si vous n’indiquez pas d’ID de sauvegarde, la sauvegarde la plus récente sera utilisée. Cela ne fonctionne pas dans cette version.

## <a name="next-steps"></a>Étapes suivantes

> **Vous voulez juste essayer ?**  
> Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure.

- [Installer les outils clients](install-client-tools.md)
- [Créer le contrôleur de données Azure Arc](create-data-controller.md) (nécessite l'installation préalable des outils clients)
- [Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)
- [Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)
- [Fournisseurs de ressources pour les services Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Notes de publication – Services de données activés par Azure Arc (préversion)](release-notes.md)
