---
title: Créer une offre Dynamics 365 pour Customer Engagement et Power Apps sur Microsoft AppSource (Place de marché Azure)
description: Créer une offre Dynamics 365 pour Customer Engagement et Power Apps sur Microsoft AppSource (Place de marché Azure)
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 550ae815933fd79e0cf6dc411aaddc8a8f564202
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108314514"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Comment créer une offre Dynamics 365 pour Customer Engagement et Power Apps

Cet article décrit comment créer une offre Dynamics 365 pour Customer Engagement et Power Apps. Toutes les offres pour Dynamics 365 passent par notre processus de certification. Cette expérience d’essai permet aux utilisateurs de déployer votre solution dans un environnement Dynamics 365 en ligne.

Avant de commencer, créez un compte de marketplace commercial dans [Espace partenaires](partner-center-portal/create-account.md) et vérifiez qu’il est inscrit au programme de marketplace commercial.

## <a name="before-you-begin"></a>Avant de commencer

Examinez [Planifier une offre Dynamics 365](marketplace-dynamics-365.md). Cet article explique les exigences techniques de cette offre et répertorie les informations et ressources dont vous aurez besoin pour la créer.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Marketplace commercial** > **Vue d’ensemble**.
3. Sur la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Dynamics 365 pour Customer Engagement et Power Apps**.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Affiche les options de menu du volet gauche et le bouton « Nouvelle offre » avec Customer Engagement sélectionné.":::

> [!IMPORTANT]
> Après publication d’une offre, les modifications que vous y apportez dans Espace partenaires apparaissent sur Microsoft AppSource uniquement après la republication de l’offre. Veillez à toujours republier une offre après l’avoir modifiée.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre et dans les modèles Azure Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. L’ID peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si votre ID d’éditeur est `testpublisherid` et que vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé sur AppSource. Il est différent du nom de l’offre et des autres valeurs présentées aux clients.
- Ce nom ne peut pas être modifié après que vous ayez sélectionné **Créer**.

Sélectionnez **Créer** pour générer l’offre. Espace partenaires ouvre la page **Configuration de l’offre**.

## <a name="alias"></a>Alias

Entrez un nom descriptif que nous utiliserons pour faire référence à cette offre uniquement dans l’Espace partenaires. L’alias de l’offre (pré-rempli avec les informations que vous avez entrées lors de la création de l’offre) n’est pas utilisé dans la Place de marché et est différent du nom de l’offre présenté aux clients. Si vous souhaitez mettre à jour le nom de l’offre par la suite, consultez la page [Annonce de l’offre](dynamics-365-customer-engage-offer-listing.md).

## <a name="setup-details"></a>Détails de la configuration

Pour **Comment voulez-vous qu’interagissent les clients potentiels avec l’offre de listing ?** , sélectionnez l’option que vous souhaitez utiliser pour cette offre :

- **Activer la gestion de licence d’application via Microsoft** : gérez vos licences d’application avec Microsoft. Pour permettre aux clients d’exécuter les fonctionnalités de base de votre application sans licence, ainsi que des fonctionnalités Premium après avoir acheté une licence, sélectionnez la **case Autoriser les clients à installer mon application même si les licences ne sont pas attribuées**. Si vous sélectionnez cette deuxième case, vous devez configurer votre package de solution pour qu’il ne nécessite pas de licence.

    > [!NOTE]
    > Vous ne pouvez pas modifier ce paramètre après la publication de votre offre. Pour en savoir plus sur ce paramètre, consultez [Gestion de licence d’application tierce via Microsoft](third-party-license.md).

- **Obtenez-en une maintenant (gratuit)**  : référencez votre offre gratuitement pour les clients.
- **Essai gratuit (annonce)**  : référencez votre offre pour les clients avec un lien vers un essai gratuit. Les versions d’évaluation gratuites sont créées, gérées et configurées par votre service et n’ont pas d’abonnements gérés par Microsoft.

    > [!NOTE]
    > Les jetons que votre application recevra via votre lien d’évaluation peuvent uniquement être utilisés pour obtenir des informations utilisateur par le biais de Azure Active Directory (Azure AD) pour automatiser la création de comptes dans votre application. Les comptes Microsoft ne sont pas pris en charge pour l’authentification à l’aide de ce jeton.

- **Me contacter** : collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Prospects](#customer-leads).

## <a name="test-drive"></a>Test drive

Une version d’évaluation constitue un excellent moyen de présenter votre offre à des clients potentiels en leur donnant accès à un environnement préconfiguré pendant un nombre fixe d’heures. L’offre d’une version d’évaluation entraîne un taux de conversion accru et génère des prospects hautement qualifiés. Pour plus d’informations, commencez par [Qu’est-ce qu’une version d’évaluation ?](what-is-test-drive.md)

> [!TIP]
> Une version d’évaluation n’est pas un essai gratuit. Vous pouvez proposer une version d’évaluation, un essai gratuit ou les deux. Dans les deux cas, vos clients disposent de votre solution pendant une période fixe. Cependant, une version d’évaluation propose en outre une visite autoguidée concrète des principales fonctionnalités de votre produit, ainsi qu’une démonstration des avantages de celui-ci au travers d’un scénario d’implémentation réel.

Pour activer une version d’évaluation, cochez la case **Activer une version d’évaluation** et sélectionnez le **type de version d’évaluation**. Vous configurerez ultérieurement la version d'évaluation. Avec la version d’évaluation, vous devez également configurer votre offre auprès d’un système CRM pour les prospects (voir la section suivante). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

## <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Pour plus d’informations, consultez [Prospects de votre offre de marketplace commercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de poursuivre vers le prochaine tabulation du menu de navigation gauche **Propriétés**.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les propriétés de l’offre](dynamics-365-customer-engage-properties.md)
- [Bonnes pratiques pour le référencement des offres](gtm-offer-listing-best-practices.md)