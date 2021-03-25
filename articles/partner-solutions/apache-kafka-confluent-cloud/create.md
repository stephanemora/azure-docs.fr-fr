---
title: Créer une instance d’Apache Kafka pour Confluent Cloud - Solutions partenaires Azure
description: Cet article explique comment utiliser le portail Azure pour créer une instance d’Apache Kafka pour Confluent Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98253660"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Démarrage rapide : Bien démarrer avec Apache Kafka pour Confluent Cloud

Dans ce guide de démarrage rapide, vous allez utiliser le portail Azure pour créer une instance d’Apache Kafka pour Confluent Cloud.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure. Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free/).
- Vous devez disposer du rôle _Propriétaire_ ou _Contributeur_ pour votre abonnement Azure. L’intégration entre Azure et Confluent ne peut être configurée que par les utilisateurs ayant un accès _Propriétaire_ ou _Contributeur_. Avant de commencer, [veillez à disposer de l’accès approprié](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Rechercher une offre

Utilisez le portail Azure pour rechercher l’application Apache Kafka pour Confluent Cloud.

1. Dans un navigateur web, accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte.

1. Si vous avez visité la **Place de marché** dans une session récente, sélectionnez l’icône parmi les options disponibles. Dans le cas contraire, recherchez _Place de marché_.

    :::image type="content" source="media/marketplace.png" alt-text="Icône de la Place de marché.":::

1. Dans la page **Place de marché**, vous disposez de deux options en fonction du type de plan souhaité. Vous pouvez vous inscrire à un plan de paiement à l’utilisation ou un plan d’engagement. Le paiement à l’utilisation est disponible pour tout le monde. Le plan d’engagement est disponible pour les clients qui ont été approuvés pour une offre privée.

   - Pour les clients inscrits à un plan de **paiement à l’utilisation**, recherchez _Apache Kafka sur Confluent Cloud_. Sélectionnez l’offre pour Apache Kafka sur Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="rechercher parmi les offres de la Place de marché Azure.":::

   - Pour les clients inscrits à un plan d’**engagement**, sélectionnez le lien vers **Voir les offres privées**. L’engagement vous oblige à vous inscrire pour un montant minimal de dépense. Utilisez cette option uniquement lorsque vous savez que vous avez besoin du service pendant une période prolongée.

     :::image type="content" source="media/view-private-offers.png" alt-text="voir les offres privées.":::

     Recherchez _Apache Kafka sur Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="sélectionner une offre privée.":::

## <a name="create-resource"></a>Créer une ressource

Une fois que vous avez sélectionné l’offre pour Apache Kafka sur Confluent Cloud, vous êtes prêt à configurer l’application.

1. Si vous avez sélectionné des offres privées dans la section précédente, vous disposez de deux options pour les types de plans :

    - Confluent Cloud - Paiement à l’utilisation
    - Engagement - Plan d’engagement

   Si vous n’avez pas sélectionné d’offres privées, vous disposez uniquement de l’option de paiement à l’utilisation.

   Choisissez le plan à utiliser, puis sélectionnez **Configurer + s’abonner**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Configurer + s’abonner.":::

1. Dans la page des concepts de base **Create Confluent Cloud Resource** (Créer une ressource Confluent Cloud), indiquez les valeurs suivantes. Quand vous avez terminé, sélectionnez **Suivant : Balises**.

    :::image type="content" source="media/setup-basics.png" alt-text="Formulaire pour configurer la ressource Confluent Cloud.":::

    | Propriété | Description |
    | ---- | ---- |
    | **Abonnement** | Dans le menu déroulant, sélectionnez l’abonnement Azure sur lequel effectuer le déploiement. Vous devez avoir un accès _Propriétaire_ ou _Contributeur_. |
    | **Groupe de ressources** | Indiquez si vous souhaitez créer un groupe de ressources ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../../azure-resource-manager/management/overview.md). |
    | **Nom de l’organisation Confluent** | Nommez la ressource SaaS (Software as a Service). |
    | **Région** | Dans le menu déroulant, sélectionnez l’une des régions suivantes : <br/><br/> Australie Est, Canada Centre, USA Centre, USA Est, USA Est 2, France Centre, Europe Nord, Asie Sud-Est, Royaume-Uni Sud, USA Centre-Ouest, Europe Ouest, USA Ouest 2 |
    | **Planification** | Sélectionnez **Paiement à l’utilisation** ou **Engagement**. |
    | **Fréquence de facturation** | Propriété préremplie en fonction du plan de facturation sélectionné. |
    | **Price** | Propriété préremplie en fonction du plan Confluent sélectionné. |

1. Sur **Étiquettes**, indiquez les paires de **nom** et **valeur** pour les étiquettes que vous souhaitez appliquer à la ressource. Après avoir entré les étiquettes, sélectionnez **Vérifier + créer**.

    :::image type="content" source="media/setup-tags.png" alt-text="Ajouter des étiquettes de projet.":::

1. Passez en revue les paramètres que vous avez fournis. Quand vous êtes prêt, sélectionnez **Créer**.

1. La création de la ressource prend quelques minutes. Vous pouvez consulter l’état du déploiement dans **Notifications**. Une fois le déploiement terminé, sélectionnez la ressource pour afficher la page **Vue d’ensemble**.

    :::image type="content" source="media/deployment-status.png" alt-text="État du déploiement.":::

   Si vous obtenez une erreur, consultez [Résolution des problèmes des solutions Apache Kafka pour Confluent Cloud](troubleshoot.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer la ressource Confluent Cloud](manage.md)
