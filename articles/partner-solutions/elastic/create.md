---
title: Créer une application Élastique - Solutions de partenaires Azure
description: Cet article explique l’utilisation du portail Azure pour créer une instance Élastique.
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 456decb74534cfd3ca5bfbf966c57b4182814225
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952609"
---
# <a name="quickstart-get-started-with-elastic"></a>Démarrage rapide : démarrage avec Élastique

Dans ce guide de démarrage rapide, le portail Azure permet d’intégrer une instance Élastique à vos solutions Azure.

## <a name="prerequisites"></a>Prérequis

- Propriétaire de l’abonnement : l’intégration d’Élastique à Azure ne peut être créée que par les utilisateurs disposant d’un accès _Propriétaire_ sur l’abonnement Azure. [Vérifiez que vous disposez de l’accès approprié](../../role-based-access-control/check-access.md) avant de commencer la configuration.
- Application à authentification unique : la navigation automatique entre le portail Azure et le cloud Élastique n’est possible que via l’authentification unique (SSO). Cette option est automatiquement activée pour tous les utilisateurs Azure. 

## <a name="find-offer"></a>Rechercher une offre

Le portail Azure permet de rechercher l’application Élastique.

1. Dans un navigateur web, accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte.

1. Si vous avez visité la **Place de marché** dans une session récente, sélectionnez l’icône parmi les options disponibles. Dans le cas contraire, recherchez _Place de marché_.

    :::image type="content" source="media/create/marketplace.png" alt-text="Icône de la Place de marché.":::

1. Recherchez _Élastique_ et sélectionnez **Elasticsearch (cloud Élastique)** parmi les offres disponibles.

1. Sélectionnez **Configurer + s’abonner**.

   :::image type="content" source="media/create/set-up.png" alt-text="sélectionnez l’offre.":::

## <a name="create-resource"></a>Créer une ressource

Une fois l’offre pour Élastique sélectionnée, vous êtes prêt à configurer l’application.

1. Sur la page des notions de base **Créer une ressource Élastique**, indiquez les valeurs suivantes.

    :::image type="content" source="media/create/create-resource.png" alt-text="Formulaire de configuration de la ressource Élastique.":::

    | Propriété | Description |
    | ---- | ---- |
    | **Abonnement** | Dans la liste déroulante, sélectionnez un abonnement Azure pour lequel vous disposez d’un accès propriétaire. |
    | **Groupe de ressources** | Indiquez si vous souhaitez créer un groupe de ressources ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../../azure-resource-manager/management/overview.md). |
    | **Nom du compte Élastique** | Entrez le nom pour le compte Élastique que vous souhaitez créer |
    | **Région** | Sélectionnez **USA Ouest 2** ou **Royaume-Uni Sud**. Dans la préversion, seules ces régions sont prises en charge par Élastique. |
    | **Plan tarifaire** | **Paiement à l’utilisation**. |
    | **Price** | Spécifié en fonction du plan Élastique sélectionné. |

   Une fois que vous avez terminé, sélectionnez **Suivant : Métriques et journaux d’activité**.

1. Dans **Métriques et journaux d’activité**, spécifiez les journaux à envoyer à Élastique.

    :::image type="content" source="media/create/configure-logs.png" alt-text="Sélectionnez les journaux à envoyer.":::

   Il existe deux types de journaux qui peuvent être émis à partir d’Azure vers Élastique.

   Les **journaux d’abonnement** fournissent des insights sur les opérations effectuées sur chaque ressource Azure de l’abonnement à partir du [plan de gestion](../../azure-resource-manager/management/control-plane-and-data-plane.md). Les journaux fournissent également des mises à jour relatives aux événements Service Health. Le journal d’activité vous permet de déterminer le contenu, l’auteur et le moment de toute opération d’écriture (PUT, POST, DELETE) effectuée sur les ressources de votre abonnement. Il n’y qu’un seul journal d’activité par abonnement Azure.

   Les **journaux de ressources Azure** fournissent des insights sur les opérations qui se produisent dans le [plan de données](../../azure-resource-manager/management/control-plane-and-data-plane.md). Par exemple, l’obtention d’un secret à partir d’un coffre de clés ou l’exécution d’une requête sur une base de données sont des activités de plan de données. Le contenu des journaux de ressources varie en fonction du service Azure et du type de ressource. Les types de journaux de ressources Azure sont répertoriés dans [Catégories de journaux de ressources Azure Monitor](../../azure-monitor/essentials/resource-logs-categories.md).

   Pour filtrer les ressources Azure qui envoient des journaux à Élastique, utilisez des étiquettes de ressource. Les règles d’étiquette pour l’envoi de journaux sont les suivantes :

   * Par défaut, les journaux sont collectés pour toutes les ressources. 
   * Les ressources possédant des étiquettes *Inclure* envoient des journaux à Élastique. 
   * Les ressources possédant des étiquettes *Exclure* n’envoient pas de journaux à Élastique. 
   * En cas de conflit entre les règles d’inclusion et d’exclusion, l’exclusion a la priorité.
 
   Sélectionnez **Suivant : Étiquettes** pour définir des étiquettes pour la nouvelle ressource Élastique.

1. Dans **Étiquettes**, ajoutez des étiquettes personnalisées pour la nouvelle ressource Élastique. Chaque étiquette se compose d’un nom et d’une valeur. Une fois les étiquettes ajoutées, sélectionnez **Suivant : évaluer + créer** afin d’accéder à l’étape finale pour la création de ressources. 

   :::image type="content" source="media/create/add-tags.png" alt-text="Ajout d’étiquettes à une ressource Élastique":::

1. À l’étape **Vérifier + créer**, votre configuration est validée. Vous pouvez vérifier les sélections que vous avez effectuées dans les formulaires précédents. Vous pouvez également consulter les conditions de cette offre.

   :::image type="content" source="media/create/review-validation.png" alt-text="Vérification et validation des sélections":::

   Une fois la validation réussie et les conditions générales vérifiées, sélectionnez **Créer**.

1. Azure démarre le déploiement.

   :::image type="content" source="media/create/deployment-in-progress.png" alt-text="État du déploiement":::

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource** pour afficher la ressource déployée.

    :::image type="content" source="media/create/deployment-complete.png" alt-text="Afficher l’état du déploiement":::


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer la ressource Élastique](manage.md)