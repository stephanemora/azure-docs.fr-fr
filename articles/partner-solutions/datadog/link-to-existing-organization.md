---
title: Lien vers les solutions existantes des partenaires Datadog - Azure
description: Cet article explique comment utiliser le portail Azure pour créer un lien vers une instance existante de Datadog.
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: b142b7d6e34905fe02de2a7546f9265289016808
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656897"
---
# <a name="quickstart-link-to-existing-datadog-organization"></a>Démarrage rapide : lier à une organisation Datadog existante

Dans ce démarrage rapide, vous allez créer un lien vers une organisation existante de Datadog. Vous pouvez soit [créer une organisation Datadog](create.md), soit établir un lien vers une organisation Datadog existante.

## <a name="prerequisites"></a>Prérequis

Avant de créer votre première instance de Datadog dans Azure, [configurez votre environnement](prerequisites.md). Vous devez effectuer ces étapes avant de passer aux étapes suivantes de ce démarrage rapide.

## <a name="find-offer"></a>Rechercher une offre

Utilisez le portail Azure pour rechercher Datadog.

1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous.

1. Si vous avez visité la **Place de marché** dans une session récente, sélectionnez l’icône parmi les options disponibles. Dans le cas contraire, recherchez _Place de marché_.

    :::image type="content" source="media/link-to-existing-organization/marketplace.png" alt-text="Icône de la Place de marché.":::

1. Dans le marketplace, recherchez **Datadog**.

1. Dans l’écran de vue d’ensemble du plan, sélectionnez **Configurer + s’abonner**.

   :::image type="content" source="media/link-to-existing-organization/datadog-app-2.png" alt-text="Application Datadog dans Place de marché Azure.":::

## <a name="link-to-existing-datadog-organization"></a>Lien vers une organisation Datadog existante

Le portail affiche une sélection vous demandant si vous souhaitez créer une organisation Datadog ou lier un abonnement Azure à une organisation Datadog existante.

Si vous établissez un lien vers une organisation Datadog existante, sélectionnez **Créer** sous **Lier un abonnement Azure à une organisation Datadog existante**

:::image type="content" source="media/link-to-existing-organization/datadog-create-link-selection.png" alt-text="Créer ou lier à une organisation Datadog" border="true":::

Vous pouvez lier votre nouvelle ressource Datadog dans Azure à une organisation Datadog existante dans **US3**.

Le portail affiche un formulaire pour la création de la ressource Datadog.

:::image type="content" source="media/link-to-existing-organization/link-to-existing.png" alt-text="Lier à une organisation Datadog existante." border="true":::

Renseignez les valeurs suivantes.

|Propriété | Description
|:-----------|:-------- |
| Abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour créer la ressource Datadog. Vous devez disposer d’un accès propriétaire. |
| Resource group | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un [groupe de ressources](../../azure-resource-manager/management/overview.md#resource-groups) est un conteneur regroupant les ressources associées pour une solution Azure. |
| Nom de la ressource | Spécifiez un nom pour la ressource Datadog. Ce nom sera celui de la nouvelle organisation Datadog, lors de la création d’une nouvelle organisation Datadog. |
| Emplacement | Sélectionnez USA Ouest 2. À l’heure actuelle, USA Ouest 2 est la seule région prise en charge. |

Sélectionnez **Lier à une organisation Datadog**. Le lien ouvre une fenêtre d’authentification Datadog. Connectez-vous à Datadog.

Par défaut, Azure lie votre organisation Datadog actuelle à votre ressource Datadog. Si vous souhaitez créer un lien vers une autre organisation, sélectionnez l’organisation appropriée dans la fenêtre d’authentification, comme indiqué ci-dessous.

:::image type="content" source="media/link-to-existing-organization/select-datadog-organization.png" alt-text="Sélectionner l’organisation Datadog appropriée à lier" border="true":::

Sélectionnez **Suivant : Métriques et journaux** pour configurer les métriques et les journaux.

## <a name="configure-metrics-and-logs"></a>Configurer les métriques et les journaux

Utilisez des étiquettes de ressources Azure pour configurer les métriques et les journaux qui sont envoyés à Datadog. Vous pouvez inclure ou exclure des métriques et des journaux pour des ressources spécifiques.

Les règles d’étiquette pour l’envoi de **métriques** sont les suivantes :

- Par défaut, les métriques sont collectées pour toutes les ressources, à l’exception des machines virtuelles, des groupes de machines virtuelles identiques et des plans App Service.
- Les machines virtuelles, les groupes de machines virtuelles identiques et les plans App Service ayant des étiquettes *Inclure* envoient des métriques à Datadog.
- Les machines virtuelles, les groupes de machines virtuelles identiques et les plans App Service ayant des étiquettes *Exclure* n’envoient pas de métriques à Datadog.
- En cas de conflit entre les règles d’inclusion et d’exclusion, l’exclusion a la priorité.

Les règles d’étiquette pour l’envoi des **journaux** sont les suivantes :

- Par défaut, les journaux sont collectés pour toutes les ressources.
- Les ressources Azure ayant des étiquettes *Inclure* envoient des journaux à Datadog.
- Les ressources Azure ayant des étiquettes *Exclure* n’envoient pas de journaux à Datadog.
- En cas de conflit entre les règles d’inclusion et d’exclusion, l’exclusion a la priorité.

Par exemple, la capture d’écran ci-dessous montre une règle d’étiquette où seuls les machines virtuelles, les groupes de machines virtuelles identiques et les plans App Service étiquetés *Datadog = True* envoient des métriques à Datadog.

:::image type="content" source="media/link-to-existing-organization/config-metrics-logs.png" alt-text="Configurer les journaux et les métriques." border="true":::

Il existe deux types de journaux qui peuvent être émis d’Azure vers Datadog.

* **Journaux de niveau abonnement** : fournissent des informations sur les opérations de vos ressources au niveau du [plan de contrôle](../../azure-resource-manager/management/control-plane-and-data-plane.md). Les mises à jour sur les événements d’intégrité du service sont également incluses. Utilisez le journal d’activité pour répondre aux questions quoi, qui et quand de toute opération d’écriture (PUT, POST, DELETE). Il n’y qu’un seul journal d’activité par abonnement Azure.

* **Journaux des ressources Azure** : fournissent des informations sur les opérations qui ont été effectuées sur une ressource Azure au niveau du [plan de données](../../azure-resource-manager/management/control-plane-and-data-plane.md). Par exemple, l’obtention d’un secret à partir d’un coffre de clés est une opération de plan de données. Ou encore, l’envoi d’une requête à une base de données est aussi une opération de plan de données. Le contenu des journaux de ressources varie en fonction du service Azure et du type de ressource.

Pour envoyer les journaux de niveau abonnement à Datadog, sélectionnez **Envoyer les journaux d’activité de l’abonnement**. Si cette option n’est pas cochée, aucun des journaux de niveau abonnement n’est envoyé à Datadog.

Pour envoyer les journaux des ressources Azure à Datadog, sélectionnez **Envoyer les journaux des ressources Azure pour toutes les ressources définies**. Les types de journaux de ressources Azure sont répertoriés dans [Catégories de journaux de ressources Azure Monitor](../../azure-monitor/essentials/resource-logs-categories.md).  Pour filtrer l’ensemble des ressources Azure qui envoient des journaux à Datadog, utilisez des étiquettes de ressources Azure.

Les journaux envoyés à Datadog sont facturés par Azure. Pour plus d’informations, consultez la [tarification des journaux de plateforme](https://azure.microsoft.com/pricing/details/monitor/) envoyés aux partenaires de Place de marché Azure.

Une fois que vous avez terminé la configuration des métriques et des journaux, sélectionnez **Suivant : Authentification unique**.

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Si vous liez la ressource Datadog à une organisation Datadog existante, vous ne pouvez pas configurer l’authentification unique à cette étape. Vous devez plutôt configurer l’authentification unique après avoir créé la ressource Datadog. Pour plus d’informations, consultez [Reconfigurer l’authentification unique](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/link-to-existing-organization/linking-sso.png" alt-text="Authentification unique pour la liaison à une organisation Datadog existante." border="true":::

Sélectionnez **Suivant : Balises**.

## <a name="add-custom-tags"></a>Ajouter des étiquettes personnalisées

Vous pouvez spécifier des étiquettes personnalisées pour la nouvelle ressource Datadog. Fournissez des paires nom-valeur pour les étiquettes à appliquer à la ressource Datadog.

:::image type="content" source="media/link-to-existing-organization/tags.png" alt-text="Ajouter des étiquettes personnalisées pour la ressource Datadog." border="true":::

Lorsque vous avez terminé d’ajouter des étiquettes, sélectionnez **Suivant : Vérifier + créer**.

## <a name="review--create-datadog-resource"></a>Vérifier + créer la ressource Datadog

Vérifiez vos sélections et les Conditions d’utilisation. Une fois la validation terminée, sélectionnez **Créer**.

:::image type="content" source="media/link-to-existing-organization/review-create.png" alt-text="Vérifier et créer la ressource Datadog." border="true":::

Azure déploie la ressource Datadog.

Une fois le processus terminé, sélectionnez **Accéder à la ressource** pour afficher la ressource Datadog.

:::image type="content" source="media/link-to-existing-organization/go-to-resource.png" alt-text="Déploiement de la ressource Datadog." border="true":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer la ressource Datadog](manage.md)
