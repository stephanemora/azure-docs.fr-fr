---
title: Gestion des licences d'application tierces avec Microsoft - Azure et Microsoft AppSource
description: Découvrez la gestion des licences d’application tierces avec Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.date: 04/30/2021
ms.openlocfilehash: 8535d124596b2ce5bd80df0d95f0fb5af5bb68f9
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759488"
---
# <a name="third-party-app-license-management-through-microsoft"></a>Gestion des licences d’application tierces avec Microsoft

> [!IMPORTANT]
> Cette fonctionnalité est actuellement disponible en préversion publique.

S’applique au type d’offre suivant :

- Dynamics 365 pour Customer Engagement et Power Apps

La _gestion des licences d’application tierces avec Microsoft_ permet aux éditeurs de logiciels indépendants qui créent des solutions avec la suite de produits Dynamics 365 de gérer et d’appliquer des licences pour leurs solutions à l’aide des systèmes fournis par Microsoft. Cette approche offre les avantages suivants :

- Vous pouvez offrir à vos clients la possibilité d’attribuer les licences de votre solution et de supprimer des attributions à l’aide d’outils familiers comme le Centre d’administration Microsoft 365, qu’ils utilisent pour gérer les licences Office et Dynamics.
- Power Platform applique vos licences à l’exécution pour que seuls les utilisateurs titulaires d’une licence puissent accéder à votre solution.
- Vous n’avez pas à créer ni à maintenir votre propre système de gestion et d’application des licences.


> [!NOTE]
> La gestion des licences d’application tierces avec Microsoft n’est accessible qu’aux éditeurs de logiciels indépendants participant au programme ISV Connect. Microsoft n’est pas impliqué dans la vente de licences.

## <a name="prerequisites"></a>Prérequis

Pour gérer vos licences d’application avec Microsoft, vous devez respecter les prérequis suivants.

1. Vous devez disposer d’un [compte Microsoft Partner Network](/partner-center/mpn-create-a-partner-center-account) valide.
1. Vous devez avoir validé votre inscription au programme de la place de marché commerciale. Pour plus d’informations, consultez [Créer un compte de marketplace commercial dans Espace partenaires](create-account.md).
1. Vous devez avoir validé votre inscription au [programme ISV Connect](https://partner.microsoft.com/solutions/business-applications/isv-overview). Pour plus d’informations, consultez [Guide d’intégration au programme Microsoft Business Applications ISV Connect](business-applications-isv-program.md).
1. Votre équipe de développement doit disposer des environnements de développement et des outils nécessaires pour créer des solutions Dataverse. Votre solution Dataverse doit inclure des applications pilotées par modèle (il s’agit actuellement du seul type de composants de solution pris en charge avec la fonctionnalité de gestion des licences).

## <a name="high-level-process"></a>Processus de haut niveau

Ce tableau illustre le processus général de gestion des applications tierces avec Microsoft :

| Étape | Détails |
| ------------ | ------------- |
| Étape 1 : Créer une offre | L’éditeur de logiciels indépendant crée une offre dans l’Espace partenaires et choisit de gérer les licences associées avec Microsoft. Cela comprend la définition d’un ou plusieurs plans de licence pour l’offre. Pour plus d'informations, consultez [Créer une offre Dynamics 365 pour Customer Engagement et Power Apps sur Microsoft AppSource](dynamics-365-customer-engage-offer-setup.md). |
| Étape 2 : Mettre à jour le package | L’éditeur de logiciels indépendant crée un package de solution pour l’offre, qui comprend des informations sur le plan de licence dans ses métadonnées, et le charge dans l’Espace partenaires pour publication sur Microsoft AppSource. Pour plus d’informations, consultez [Ajout de métadonnées de licence à une solution](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution). |
| Étape 3 : Acheter des licences | Les clients découvrent l’offre de l’éditeur dans AppSource ou directement sur le site web de l’éditeur. Les clients achètent des licences pour les plans qu’ils souhaitent directement auprès de l’éditeur (ces offres ne peuvent pas être achetées par le biais d’AppSource pour le moment). |
| Étape 4 : Enregistrer la transaction | L’éditeur enregistre l’achat auprès de Microsoft dans l’Espace partenaires. Dans le cadre de [l’enregistrement de la transaction](/partner-center/csp-commercial-marketplace-licensing#register-isv-connect-deal-in-deal-registration), il spécifie le type et la quantité de chacun des plans de licence achetés par le client. |
| Étape 5 : Gérer les licences | Les plans de licence apparaissent dans le Centre d'administration Microsoft 365 pour permettre au client de les [attribuer aux utilisateurs ou aux groupes](/microsoft-365/commerce/licenses/manage-third-party-app-licenses) de son organisation. Il a également la possibilité d’installer l’application dans son locataire avec le Centre d’administration Power Platform. |
| Étape 6 : Effectuer la vérification de la licence | Lorsqu’un utilisateur de l’organisation du client tente d’exécuter une application, Microsoft vérifie qu’il dispose d’une licence avant de l’autoriser à exécuter l’application. Si ce n’est pas le cas, l’utilisateur voit un message lui indiquant qu’il doit contacter un administrateur pour obtenir une licence. |
| Étape 7 : Afficher les rapports | Les éditeurs peuvent afficher des informations sur les licences provisionnées et attribuées sur une période de temps donnée et selon la zone géographique. |
|||

## <a name="enabling-app-license-management-through-microsoft"></a>Activation de la gestion des licences d’application avec Microsoft

Lors de la création d’une offre, deux cases à cocher dans l’onglet Configuration de l’offre permettent d’activer la gestion des licences d’application sur une offre.

### <a name="enable-app-license-management-through-microsoft-check-box"></a>Case à cocher Activer la gestion des licences d’application avec Microsoft

Fonctionnement :

- Une fois que vous avez coché la case **Activer la gestion des licences d’application avec Microsoft**, vous pouvez définir les plans de licence de votre offre.
- Les clients voient un bouton **Obtenir maintenant** sur la page de description de l’offre dans AppSource. Ils peuvent le sélectionner pour vous contacter dans le but d’acheter des licences pour l’application.

### <a name="allow-customers-to-install-my-app-even-if-licenses-are-not-assigned-check-box"></a>Case à cocher Autoriser les clients à installer mon application même si les licences ne sont pas attribuées

Une fois que vous avez coché la première case, la zone **Autoriser les clients à installer mon application même si les licences ne sont pas attribuées** s’affiche. Cette option est utile si vous utilisez une stratégie de licence « freemium » qui vous permet de proposer gratuitement les fonctionnalités de base de votre solution à tous les utilisateurs et de faire payer les fonctionnalités Premium. Si, à l’inverse, vous souhaitez que seuls les locataires qui possèdent actuellement des licences pour votre produit puissent le télécharger sur AppSource, ne sélectionnez pas cette option.

> [!NOTE]
> Si vous cochez cette deuxième case, vous devez configurer votre package de solution de sorte qu’aucune licence ne soit exigée.

Fonctionnement :

- Tous les utilisateurs d’AppSource voient le bouton **Obtenir maintenant** sur la page de description de l’offre, ainsi que le bouton **Me contacter**. Ils sont autorisés à télécharger et à installer votre offre.
- Si vous ne sélectionnez pas cette option, AppSource vérifie que le locataire de l’utilisateur dispose d’au moins une licence pour votre solution avant d’afficher le bouton **Obtenir maintenant**. S’il n’existe aucune licence dans le locataire de l’utilisateur, seul le bouton **Me contacter** s’affiche.

Pour plus d’informations sur la configuration d’une offre, consultez [Guide pratique pour créer une offre Dynamics 365 pour Customer Engagement et Power Apps](dynamics-365-customer-engage-offer-setup.md).

## <a name="offer-listing-page-on-appsource"></a>Page de description de l’offre sur AppSource

Une fois votre offre publiée, les options que vous avez choisies déterminent quels boutons sont proposés à l’utilisateur. Cette capture d’écran montre une page de description de l’offre sur AppSource avec les boutons **Obtenir maintenant** et **Me contacter**.

:::image type="content" source="./media/third-party-license/f365.png" alt-text="Capture d’écran d’une page de description de l’offre sur AppSource, avec les boutons Télécharger maintenant et Me contacter.":::

***Figure 1 : Page de description de l’offre sur Microsoft AppSource***

## <a name="next-steps"></a>Étapes suivantes

- [Planifier une offre Dynamics 365](marketplace-dynamics-365.md)
- [Guide pratique pour créer une offre Dynamics 365 pour Customer Engagement et Power Apps](dynamics-365-customer-engage-offer-setup.md)
