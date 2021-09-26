---
title: Comment migrer une instance Gestion des API Azure d'une région vers une autre
description: Apprenez à migrer une instance Gestion des API d'une région vers une autre.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/20/2021
ms.author: danlep
ms.custom: subject-moving-resources
ms.openlocfilehash: 1007241c09e22d1fb35f6cc12733c04946af88f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591783"
---
# <a name="how-to-move-azure-api-management-across-regions"></a>Comment migrer une instance Gestion des API Azure d'une région vers une autre

Cet article explique comment déplacer une instance de Gestion Azure vers une autre région Azure. Vous aurez peut être à migrer votre instance vers une autre région pour de multiples raisons. Par exemple :

* Localisez votre instance plus près de vos consommateurs d’API
* Déployer les fonctionnalités disponibles uniquement dans des régions spécifiques
* Respecter les exigences de gouvernance et de stratégie internes

Pour migrer des instances de Gestion des API d'une région Azure vers une autre, vous pouvez utiliser les opérations [de sauvegarde et de restauration](api-management-howto-disaster-recovery-backup-restore.md) du service. Vous pouvez utiliser un autre nom d’instance de Gestion des API ou le nom existant. 

> [!NOTE]
> La Gestion des API prend également en charge le [déploiement multirégions](api-management-howto-deploy-multi-region.md), qui distribue un service de gestion des API Azure unique sur plusieurs régions Azure. Ce déploiement multirégions permet de réduire la latence de la requête telle qu’elle est perçue par les consommateurs distribués de l’API et améliore la disponibilité du service si une région est mise hors connexion.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="considerations"></a>Considérations

* Choisissez le même niveau tarifaire de Gestion des API dans les régions source et cible. 
* La sauvegarde et la restauration ne fonctionneront pas lors de la migration entre différents types de cloud. Pour ce scénario, exportez la ressource [en tant que modèle](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Adaptez ensuite le modèle exporté à la région Azure cible et recréez la ressource. 

## <a name="prerequisites"></a>Prérequis

* Passez en revue les exigences et les limitations des opérations de [sauvegarde et de restauration](api-management-howto-disaster-recovery-backup-restore.md) de la Gestion des API. 
* Consultez les [Éléments non sauvegardés](api-management-howto-disaster-recovery-backup-restore.md#what-is-not-backed-up). Enregistrez les paramètres et les données que vous devrez recréer manuellement après le déplacement de l’instance.
* Créez un [compte de stockage](../storage/common/storage-account-create.md?tabs=azure-portal) situé dans la région source. Vous allez utiliser ce compte pour sauvegarder l’instance source. 

## <a name="prepare-and-move"></a>Préparer et déplacer

### <a name="option-1-use-a-different-api-management-instance-name"></a>Option 1 : Utiliser un nom d'instance Gestion des API différent

1. Dans la région cible, créez une nouvelle instance Gestion des API avec le même niveau tarifaire que l'instance source. Utilisez un nom différent pour la nouvelle instance.
1. [Sauvegardez](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service) l'instance de Gestion des API existante sur le compte de stockage. 
1. [Restaurez](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service) la sauvegarde de l’instance source vers la nouvelle instance de Gestion des API.
1. Si vous disposez d'un domaine personnalisé pointant vers l'instance Gestion des API de la région source, mettez à jour l'enregistrement CNAME du domaine personnalisé pour qu'il pointe vers la nouvelle instance Gestion des API. 

### <a name="option-2-use-the-same-api-management-instance-name"></a>Option n°2 : Utiliser le même nom d'instance Gestion des API

> [!WARNING]
> Cette option supprime l’instance de Gestion des API d’origine et entraîne un temps d’arrêt pendant la migration. Vérifiez que vous disposez d’une sauvegarde valide avant de supprimer l’instance source.

1. [Sauvegardez](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service) l'instance de Gestion des API existante sur le compte de stockage. 
1. Supprimez l'instance de Gestion des API de la région source. 
1. Créez une nouvelle instance Gestion des API dans la région cible en lui attribuant le même nom que celle de la région source.
1. [Restaurez](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service) la sauvegarde de l’instance source vers la nouvelle instance de Gestion des API dans la région cible.  

## <a name="verify"></a>Vérifier

1. Assurez-vous que l’opération de restauration se termine correctement avant d’accéder à votre instance de Gestion des API dans la région cible.
1. Configurez les paramètres qui ne sont pas déplacés automatiquement au cours de l’opération de restauration. Exemples : la configuration du réseau virtuel, les identités managées, le contenu du portail des développeurs et les certificats de l’autorité de certification personnalisée et du domaine personnalisé.
1. Accédez à vos points de terminaison de Gestion des API dans la région cible. Par exemple, testez vos API ou accédez au portail des développeurs.

## <a name="clean-up-source-resources"></a>Nettoyer les ressources sources

Si vous avez déplacé l’instance Gestion des API à l’aide de l’Option 1, vous pouvez supprimer l’instance source après la restauration et la configuration réussies de l’instance cible.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d'informations sur la fonctionnalité de sauvegarde et de restauration, consultez [Implémenter la récupération d'urgence](api-management-howto-disaster-recovery-backup-restore.md).
* Pour plus d'informations sur la migration des ressources Azure, consultez les [instructions sur la migration entre régions Azure](https://github.com/Azure/Azure-Migration-Guidance).
* [Optimiser et réduire les dépenses liées au cloud](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
