---
title: Intégration d’éléments de travail – Application Insights
description: Découvrez comment créer des éléments de travail dans GitHub ou Azure DevOps avec des données Application Insights incorporées.
ms.topic: conceptual
ms.date: 06/27/2021
ms.openlocfilehash: 02fbb15f417d01d1f9c5b572fdb5553ad1127037
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989131"
---
# <a name="work-item-integration"></a>Intégration d’éléments de travail 

La fonctionnalité d’intégration d’éléments de travail vous permet de créer facilement des éléments de travail dans GitHub ou Azure DevOps, contenant des données Application Insights incorporées.


La nouvelle intégration d’éléments de travail offre les fonctionnalités suivantes par rapport à la version [classique](#classic-work-item-integration) :
- Champs avancés tels que destinataire, projets ou jalons.
- Icônes du référentiel pour vous permettre de faire la différence entre les classeurs GitHub et Azure DevOps.
- Configurations multiples pour un nombre quelconque de référentiels ou d’éléments de travail.
- Déploiement par le biais de modèles Azure Resource Manager.
- Requêtes KQL (Keyword Query Language) pré-générées et personnalisables pour ajouter des données Application Insights à vos éléments de travail.
- Modèles de classeur personnalisables.


## <a name="create-and-configure-a-work-item-template"></a>Créer et configurer un modèle d’élément de travail

1. Pour créer un modèle d’élément de travail, accédez à votre ressource Application Insights, à gauche sous *Configurer*, sélectionnez **Éléments de travail**, puis en haut, choisissez **Créer un modèle**

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Capture d’écran de l’onglet Éléments de travail avec l’option Créer un modèle sélectionnée." lightbox="./media/work-item-integration/create-work-item-template.png":::

    Vous pouvez également créer un modèle d’élément de travail à partir de l’onglet Détails de la transaction de bout en bout, si aucun modèle n’existe actuellement. Sélectionnez un événement et, à droite, choisissez **Créer un élément de travail**, puis **Démarrer avec un modèle de classeur**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Capture d’écran de l’onglet Détails de la transaction de bout en bout, avec l’option Créer un élément de travail, Démarrer avec un modèle de classeur sélectionnée." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Après avoir sélectionné **Créer un modèle**, vous pouvez choisir vos systèmes de suivi, nommer votre classeur, créer un lien vers votre système de suivi sélectionné, puis choisir une région pour le stockage du modèle (la valeur par défaut est la région dans laquelle se trouve votre ressource Application Insights). Les paramètres d’URL sont l’URL par défaut de votre référentiel, par exemple, `https://github.com/myusername/reponame` ou `https://mydevops.visualstudio.com/myproject`.

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Capture d’écran de la création d’un modèle de classeur.":::

    Vous pouvez définir des propriétés spécifiques de l’élément de travail directement à partir du modèle lui-même. Cela comprend le destinataire, le chemin d’itération, les projets et plus encore, en fonction de votre fournisseur de contrôle de version.

## <a name="create-a-work-item"></a>Créer un élément de travail

 Vous pouvez accéder à votre nouveau modèle à partir de l’onglet Détails de la transaction de bout en bout, auquel vous pouvez accéder à partir des onglets Performances, Échecs, Disponibilité, entre autres.

1. Pour créer un élément de travail, accédez à l’onglet Détails de la transaction de bout en bout, sélectionnez un événement, choisissez **Créer un élément de travail**, puis sélectionnez votre modèle d’élément de travail.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Capture d’écran de l’onglet Détails de la transaction de bout en bout avec l’option Créer un élément de travail sélectionnée." lightbox="./media/work-item-integration/create-work-item.png":::

1. Votre système de suivi s’ouvre dans un nouvel onglet de votre navigateur. Dans Azure DevOps, vous pouvez créer un bogue ou une tâche et, dans GitHub, vous pouvez créer un problème dans votre référentiel. Un nouvel élément de travail est automatiquement créé avec des informations contextuelles fournies par Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Capture d’écran du problème GitHub automatiquement créé" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Capture d’écran de la création automatique d’un bogue dans Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Modifier un modèle

Pour modifier votre modèle, accédez à l’onglet **Éléments de travail** sous *Configurer*, puis sélectionnez l’icône en forme de crayon en regard du classeur que vous souhaitez mettre à jour.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Capture d’écran de l’onglet d’un élément de travail avec l’icône de modification en forme de crayon sélectionnée.":::

Sélectionnez Modifier ![icône de modification](./media/work-item-integration/edit-icon.png) en haut pour commencer à modifier le modèle. Les modèles d’élément de travail sont basés sur des [classeurs Azure Monitor](../visualize/workbooks-overview.md). Les informations sur l’élément de travail sont générées à l’aide du langage de requête de mot clé. Vous pouvez modifier les requêtes pour ajouter davantage de contexte essentiel à votre équipe. Lorsque vous avez terminé la modification, enregistrez le classeur en sélectionnant Enregistrer ![icône d’enregistrement](./media/work-item-integration/save-icon.png) dans la barre d’outils supérieure.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Capture d’écran du modèle de classeur d’élément de travail en mode d’édition." lightbox="./media/work-item-integration/edit-workbook.png":::

Vous pouvez créer plusieurs configurations d’élément de travail et disposer d’un classeur personnalisé pour répondre à chaque scénario. Les classeurs peuvent également être déployés par Azure Resource Manager afin de garantir des implémentations standard dans tous vos environnements.

## <a name="classic-work-item-integration"></a>Intégration des éléments de travail classiques 

1. Dans votre ressource Application Insights, sous *Configurer*, sélectionnez **Éléments de travail**.
1. Sélectionnez **Basculer vers classique**, renseignez les champs avec vos informations et autorisez. 

    :::image type="content" source="./media/work-item-integration/classic.png" alt-text=" Capture d’écran de la configuration des éléments de travail classiques." lightbox="./media/work-item-integration/classic.png":::

1. Créer un élément de travail en accédant aux détails de la transaction de bout en bout, sélectionnez un événement, choisissez **Créer un élément de travail (classique)** . 


### <a name="migrate-to-new-work-item-integration"></a>Migrer vers une nouvelle intégration d’éléments de travail

Pour migrer, supprimez votre configuration d’éléments de travail classique, puis [créez et configurez un modèle d’élément de travail](#create-and-configure-a-work-item-template) pour recréer votre intégration.

Pour supprimer, accédez à votre ressource Application Insights sous *Configurer* sélectionnez **Éléments de travail** puis sélectionnez **Basculer vers classique** et **Supprimer* en haut.


## <a name="next-steps"></a>Étapes suivantes
[Test de disponibilité](availability-overview.md)

