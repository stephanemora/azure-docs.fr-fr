---
title: Consulter et publier une offre sur le Marketplace Microsoft
description: Utilisez l’Espace Partenaires pour soumettre votre offre en préversion, afficher un aperçu de votre offre, puis la publier sur le Marketplace Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 07/05/2020
ms.openlocfilehash: 34e56e5d92526cbf46408c670127e87781e342cd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119733"
---
# <a name="review-and-publish-an-offer-to-commercial-marketplace"></a>Consulter et publier une offre sur le Marketplace

Cet article vous montre comment utiliser l’Espace Partenaires pour soumettre votre offre en préversion, afficher un aperçu de votre offre, puis la publier sur le Marketplace Microsoft. Nous aborderons également la vérification de l’état de publication au fur et à mesure de la procédure de publication. Vous devez avoir déjà créé une offre que vous souhaitez publier.

## <a name="go-to-your-offer-in-commercial-marketplace"></a>Accédez à votre offre dans le Marketplace

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
1. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
1. Dans l’onglet **Vue d’ensemble**, sous **Offres**, l’un des indicateurs d’état suivants s’affiche dans la colonne **État** pour chaque offre.
 
    | Statut | Description |
    | ------------ | ------------- |
    | Brouillon | L’offre a été créée, mais elle n’est actuellement pas publiée. |
    | Publication en cours | Le processus de publication de l’offre est en cours. |
    | Attention requise | Nous avons découvert un problème critique au cours de la certification ou au cours d’une autre phase de publication. |
    | PRÉVERSION | L’offre a été certifiée par Microsoft et doit maintenant faire l’objet d’une vérification finale par l’éditeur. Sélectionnez **Mise en ligne** pour proposer l’offre en direct. |
    | En direct | L’offre est également disponible en direct dans la Place de marché. Des clients peuvent la voir et l’acheter. |
    | Arrêt de la vente en attente | L’éditeur a sélectionné « Arrêter la vente » sur l’offre ou le plan, mais l’action n’est pas encore terminée. |
    | Non disponible dans la Place de marché | Une offre publiée sur la place de marché a été supprimée. |
    |||

4. Dans la colonne **Alias d’offre**, sélectionnez l’offre que vous souhaitez prévisualiser et publier.

## <a name="submit-your-offer-to-preview"></a>Soumettre l’offre en préversion

1. Pour soumettre votre offre en préversion, sélectionnez **Vérifier et publier** dans le coin supérieur droit du portail. La page **Vérifier et publier** s’ouvre.
1. Assurez-vous que la colonne **État** de chaque page indique **Complète**. Les états possibles sont les suivants :
   - **Non démarrée** : la page n’a pas encore été modifiée et doit être complétée.
   - **Incomplète** : des informations requises sont manquantes dans la page ou des erreurs doivent être corrigées. Vous devez revenir à la page et la mettre à jour.
   - **Complète** : la page est complète. Toutes les données requises ont été fournies et il n’existe aucune erreur.
1. Si l’un des pages présente un État autre que **Complète**, dans la colonne **Page**, sélectionnez le nom de la page, corrigez le problème, enregistrez la page, puis sélectionnez **Vérifier et publier** à nouveau pour revenir à cette page.
1. Après avoir compléter toutes les pages, dans la boîte **Notes pour la certification**, fournissez des instructions de test à l’équipe de certification pour vous assurer que votre application est correctement testée. Fournissez toutes les notes supplémentaires utiles pour comprendre votre demande.
1. Pour envoyer l’offre à des fins de publication, sélectionnez **Publier**. La page **Vue d’ensemble** s’affiche et indique l’état de publication.

## <a name="validation-and-publishing-steps"></a>Étapes de validation et de publication

Une fois que vous avez sélectionné **Publier**, les processus de validation et de publication sont exécutés dans l’ordre. Le processus de publication le plus courant est illustré dans le tableau suivant :

| Phase | Ce qui se produit | 
| ------------ | ------------- | ------------- |
| Validation automatique | Nous effectuons un ensemble de validations automatisées. | 
| Certification | Nous effectuons des validations manuelles. | 
| Création de la préversion | La page de liste de votre offre en préversion est disponible pour toute personne disposant d’un lien d’aperçu. Si votre offre est vendue par le biais de Microsoft (négociable), seul le public que vous avez spécifié sur la page **Preview audience** (Public de la préversion) de votre offre peut acheter et accéder à l’offre pour test. | 
| Validation de l’éditeur | Nous vous envoyons un e-mail vous demandant de regarder votre offre et de l’approuver. | 
| Publish | Nous exécutons une série d’étapes pour vérifier que l’offre en préversion est publiée en direct sur la place de marché commerciale. | 
|||

## <a name="automated-validation-phase"></a>Phase de validation automatique

La première étape du processus de publication est un ensemble de validations automatiques. Chaque étape de validation correspond à une fonctionnalité que vous avez choisi d’activer lors de la création de votre offre. Chaque contrôle de validation doit être effectué avant que l’offre puisse passer à l’étape suivante du processus de publication.

- **Configuration du flux d’achat de l’offre (< 10 min)**

   Dans cette étape, nous faisons en sorte que votre offre puisse être traitée quand elle est achetée par des clients à l’aide du portail Azure. Cette étape est applicable uniquement aux offres vendues par le biais de Microsoft.
- **Validation des données de la version d’évaluation (environ 5 min)**

   Dans cette étape, nous validons les données que vous avez fournies dans la page Configuration technique de l’offre. Les fonctionnalités de la version d’évaluation sont testées et approuvées. Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.

-   **Provisionnement de la version d’évaluation (environ 30 min)**

    Dans cette étape, après avoir validé les données et les fonctionnalités de votre version d’évaluation à l’étape précédente, nous déployons et répliquons des instances de votre version d’évaluation afin qu’elles soient prêtes à être utilisées par les clients. Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.

-   **Validation et enregistrement de la gestion des prospects (< 15 min)**

    Dans cette étape, nous vérifions que votre système de gestion des prospects peut recevoir des prospects en fonction des détails fournis dans la page **Configuration de l’offre**. Cette étape est applicable uniquement pour les offres avec la gestion des prospects activée.

## <a name="certification-phase"></a>Phase de certification

Avant d’être publiées, les offres soumises à la place de marché commerciale doivent être certifiées. Les offres soumises subissent des tests rigoureux, parfois automatisés et parfois manuels. Consultez les [politiques de certification de la place de marché commerciale](https://aka.ms/commercial-marketplace-certification-policies) pour en savoir plus.

### <a name="types-of-validation-that-take-place-during-certification"></a>Types de validation qui se produisent lors de la certification
Le processus de certification comprend trois niveaux de validation pour chaque offre soumise.
-   Éligibilité de l’entreprise de l’éditeur
-   Validation du contenu
-   Validation technique

#### <a name="publisher-business-eligibility"></a>Éligibilité de l’entreprise de l’éditeur
Chaque type d’offre vérifie un ensemble de critères d’éligibilité de base que l’éditeur doit satisfaire. Les critères d’éligibilité peuvent inclure l’état MPN de l’éditeur, les compétences détenues, les niveaux de compétence, etc.

#### <a name="content-validation"></a>Validation du contenu

La qualité et la pertinence des informations entrées lors de la création de votre offre sont vérifiées. Ces vérifications passent en revue vos entrées pour les détails de référencement dans la Place de marché, le tarif, la disponibilité, les plans associés, etc. Pour répondre aux critères de référencement dans la Place de marché Azure et/ou AppSource, nous vérifions que votre offre inclut les éléments suivants :
-   Un titre qui décrit précisément l’offre
-   Des descriptions bien écrites qui fournissent une vue d’ensemble détaillée et une proposition de valeur
-   Des captures d’écran et vidéos de qualité
-   Une description de la façon dont l’offre utilise les outils et des plateformes Microsoft.

Apprenez-en plus sur les critères de validation du contenu en lisant les [stratégies de référencement générales](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Validation technique
Pendant la validation technique, l’offre (package ou binaire) fait l’objet des vérifications suivantes.
-   Analyse pour détecter les programmes malveillants
-   Supervision des appels réseau
-   Analyse du package
-   Une analyse complète des fonctionnalités réelles de l’offre

L’offre est testée sur différentes plateformes et différentes versions afin de garantir qu’elle est fiable.

### <a name="certification-failure-report"></a>Rapport d’échec de la certification

Si votre offre ne passe pas l’une des vérifications de référencement, techniques ou de politique, ou si vous n’êtes pas qualifié pour soumettre une offre de ce type, un rapport d’échec de certification est généré et vous est envoyé par e-mail.

Ce rapport contient des descriptions de toutes les stratégies ayant échoué, ainsi que des remarques sur les vérifications. Passez en revue ce rapport par e-mail, résolvez tous les problèmes, en mettant à jour votre offre si nécessaire, puis soumettez de nouveau l’offre à l’aide du [portail de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) dans l’Espace partenaires. (Vous pouvez soumettre l’offre autant de fois que nécessaire jusqu’à ce que la certification réussisse).

## <a name="preview-creation-phase"></a>Phase de création de la préversion

Lors de l’étape de création de la préversion, nous créons une version de votre offre accessible uniquement au public que vous avez spécifié dans la section **Public de la préversion** (Public de la préversion) de votre offre. La préversion de votre offre sera indisponible pour toute personne n’étant pas spécifiée dans le public de la préversion tant que vous n’avez pas publié l’offre en direct.

> [!NOTE]
> N'utilisez pas ce public de préversion pour permettre aux personnes extérieures à votre organisation de visualiser une offre. Utilisez plutôt l'option Private Offer (Offre privée). À ce stade, votre offre n'a pas encore été entièrement testée et validée, et elle n'est pas prête à être distribuée à l'extérieur. 

## <a name="publisher-signoff-phase"></a>Phase de validation de l’éditeur

Une fois que l’offre est prête à être revue et approuvée, nous vous enverrons un e-mail pour vous demander de consulter et d’approuver la version préliminaire de votre offre. Vous pouvez également actualiser la page **Vue d’ensemble de l’offre** depuis votre navigateur pour voir si votre offre a atteint la phase d’approbation de publication. Si c’est le cas, le bouton **Go Live** et les liens d’aperçu seront disponibles.

La capture d’écran suivante montre la page **Vue d’ensemble de l’offre** pour une offre SaaS. Les étapes de validation que vous verrez sur cette page varient en fonction du type d’offre et des sélections que vous avez apportées lors de la création de l’offre.

![Illustre la page Vue d’ensemble de l’offre pour une offre dans l’Espace partenaires. Le bouton Go Live et les liens d’aperçu s’affichent.](./partner-center-portal/media/publish-status-publisher-signoff.png)

**Pour afficher un aperçu de votre offre et approuver**
1. Sur la page **Vue d’ensemble de l’offre**, pour afficher un aperçu de votre offre, sélectionnez le lien sous le bouton **Go Live**.
   > [!NOTE]
   > Il y aura un lien soit pour l’aperçu AppSource, soit pour l’aperçu de la Place de marché Azure, ou les deux, selon les options que vous avez choisies lors de la création de votre offre. Si vous avez choisi de vendre votre offre par le biais de Microsoft, quiconque a été ajouté au public de la préversion peut tester son acquisition et son déploiement pour vérifier qu’elle répond à vos exigences au cours de cette phase.

1. Si vous voulez apporter des changements à l’offre après l’avoir visualisée, vous pouvez la modifier et la soumettre de nouveau pour publier une nouvelle préversion. Pour plus d’informations, consultez [Mettre à jour une offre existante dans la Place de marché commerciale](./partner-center-portal/update-existing-offer.md).

1. Une fois que vous avez approuvé votre préversion, pour publier votre offre en direct sur la place de marché commerciale, sélectionnez **Go Live**.
   > [!TIP]
   > Si votre offre est déjà publiée et disponible pour public dans la Place de marché, toutes les mises à jour que vous apportez ne seront pas mises en service tant que vous ne sélectionnerez pas **Go live**.

## <a name="publish-phase"></a>Phase de publication

Maintenant que vous avez choisi de mettre en service votre offre, en la rendant disponible dans la Place de marché, il y a une série de vérifications finales qui sera effectuée pour garantir que l’offre est configurée exactement comme la préversion.

-   **Configuration du flux d’achat de l’offre (> 10 min)**

    Dans cette étape, nous faisons en sorte que votre offre puisse être traitée quand elle est achetée par des clients à l’aide du portail Azure. Cette étape est applicable uniquement aux offres vendues par le biais de Microsoft.
-   **Validation des données de la version d’évaluation (environ 5 min)**

    Dans cette étape, nous validons les données que vous avez fournies dans la page Configuration technique de l’offre. Les fonctionnalités de la version d’évaluation sont testées et approuvées. Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.

-   **Provisionnement de la version d’évaluation (environ 30 min)**

      Dans cette étape, nous déployons et répliquons des instances de votre version d’évaluation afin qu’elles soient prêtes à être utilisées par les clients. Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.
-   **Validation et enregistrement de la gestion des prospects (> 15 min)**

    Dans cette étape, nous vérifions que votre système de gestion des prospects peut recevoir des prospects en fonction des détails fournis dans la page **Configuration de l’offre**. Cette étape est applicable uniquement pour les offres avec la gestion des prospects activée.

-   **Dernière publication (> 30 minutes)**

    Dans cette étape, nous vérifions que votre offre est publiquement disponible dans la Place de marché.

Une fois ces vérifications terminées, votre offre est publiée sur la place de marché.

## <a name="next-step"></a>Étape suivante

[Accéder aux rapports analytiques de la Place de marché commerciale dans l’Espace partenaires](./partner-center-portal/analytics.md)