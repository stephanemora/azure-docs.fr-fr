---
title: Créer une offre de test
description: Comment créer une offre de développement distincte pour tester votre offre de production dans le cadre du programme Place de marché commerciale de l'Espace partenaires Microsoft ?
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 318eebcca3053be2164a1fc52905f36f26b11a6d
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331121"
---
# <a name="create-a-test-offer"></a>Créer une offre de test

Pour développer dans un environnement distinct de votre offre de production, vous devez créer une offre de test/développement (DEV) et une offre de production (PROD) distinctes. Pour plus d'informations sur les avantages de l'utilisation d'une offre DEV distincte, consultez [Planifier une offre SaaS](plan-saas-offer.md#test-offer).

Pour la plupart des paramètres, la configuration est la même dans les offres DEV et PROD. Par exemple, la langue officielle utilisée pour le marketing et les ressources, telles que les captures d'écran et les logos, doivent être les mêmes. Lorsque la configuration est identique, vous pouvez copier-coller des champs des plans de l'offre DEV vers les plans de l'offre PROD.

Les sections suivantes décrivent les différences de configuration entre les offres DEV et PROD.

## <a name="offer-setup-page"></a>Page de configuration de l’offre

Nous vous recommandons d'utiliser le même alias dans la zone **Alias** des deux offres, mais d'ajouter « _test » à l'alias de l'offre DEV. Par exemple, si l'alias de votre offre PROD est « contososolution », l'alias de l’offre DEV doit être « contososolution_test ». Vous pourrez ainsi facilement distinguer votre offre DEV de votre offre PROD.

Dans la section **Prospects**, utilisez une table Azure ou un environnement CRM de test pour l'offre DEV. Utilisez le système de gestion des prospects prévu pour l'offre PROD.

## <a name="properties-page"></a>Page Propriétés

Configurez cette page de la même façon dans les offres DEV et PROD.

## <a name="offer-listing-page"></a>Page de référencement de l’offre

Configurez cette page de la même façon dans les offres DEV et PROD.

## <a name="preview-audience"></a>Public de la version préliminaire

Dans l'offre DEV, incluez les noms d'utilisateur principal Azure Active Directory (AAD) ou les adresses e-mail des comptes Microsoft (MSA) des développeurs et des testeurs, y compris la vôtre. Le nom d'utilisateur principal d'un utilisateur AAD peut être différent de son adresse e-mail. Par exemple, jane.doe@contoso.com ne fonctionne pas alors que janedoe@contoso.com fonctionne. Les utilisateurs que vous désignerez auront accès à l'offre DEV lorsque vous partagerez le lien **Préversion** pendant la phase de développement et de test.

Dans l'offre PROD, incluez le nom d'utilisateur principal Azure AD ou l'adresse e-mail du compte Microsoft des utilisateurs qui valideront l'offre avant de sélectionner le **bouton Mettre en ligne** pour publier l'offre en direct.

## <a name="technical-configuration-page"></a>Page de configuration technique

Le tableau suivant décrit les différences entre les paramètres des offres DEV et des offres PROD.

***Tableau 1 : Différences de configuration technique***

| Paramètre | Offre DEV | Offre PROD |
| ------------ | ------------- | ------------- |
| URL de la page d'arrivée | Entrez votre point de terminaison de dev/test. | Entrez votre point de terminaison de production. |
| Webhook de connexion | Entrez votre point de terminaison de dev/test. | Entrez votre point de terminaison de production. |
| ID de locataire Azure Active Directory | Entrez l'ID de locataire pour l'inscription de votre application de test (ID du répertoire AAD). | Entrez l'ID de locataire pour l'inscription de votre application de production. |
| ID d’application Azure Active Directory | Entrez l'ID d'application pour l'inscription de votre application de test (ID client). | Entrez l'ID d'application pour l'inscription de votre application de production. |
||||

## <a name="plan-overview-page"></a>Page de présentation du plan

Lors de la création de vos plans, nous vous recommandons d'utiliser le même _ID de plan_ et le même _Nom de plan_ dans les offres DEV et PROD, à l'exception de l'ID de plan de l'offre DEV auquel vous devez ajouter **_test**. Par exemple, si l'ID de plan de l'offre PROD est « entreprise », l'ID de plan de l'offre DEV doit être « enterprise_test ». Vous pourrez ainsi facilement distinguer votre offre DEV de votre offre PROD. Lors de la création des plans de l'offre PROD, vous utiliserez les modèles de tarification et les prix que vous jugerez les mieux adaptés à votre offre.

### <a name="plan-listing"></a>Liste des plans

Sous l'onglet **Présentation des plans** > **Liste des plans**, entrez la même description pour les plans DEV et PROD.

### <a name="pricing-and-availability-page"></a>Page Tarification et disponibilité

Cette section fournit des conseils pour compléter la page **Présentation des plans** > **Tarification et disponibilité**.

#### <a name="markets"></a>Marchés

Sélectionnez les mêmes marchés pour les offres DEV et PROD.

#### <a name="pricing"></a>Tarifs

Utilisez l'offre DEV pour expérimenter des modèles de tarification. Après avoir identifié le ou les modèles de tarification qui conviennent le mieux, vous créerez les plans de l'offre PROD avec les modèles de tarification et les prix souhaités.

L'offre DEV doit comporter des plans dont les prix sont nuls ou bas. L'offre PROD comportera les prix que vous souhaitez facturer aux clients.

> [!IMPORTANT]
> Les achats effectués dans le cadre de la Préversion seront traités pour les offres DEV et PROD. Si le prix d'une offre est de 100 USD/mois, votre entreprise sera facturée 100 USD. Dans ce cas, vous pouvez ouvrir un [ticket de support](support.md) et nous émettrons un paiement correspondant au montant total (sans frais de service de magasin).

#### <a name="pricing-model"></a>Modèle de tarification

Utilisez le même modèle de tarification dans les plans des offres DEV et PROD. Par exemple, si le plan de l'offre PROD est forfaitaire, avec une fréquence de facturation mensuelle, configurez le plan de l'offre DEV avec le même modèle.

Pour réduire les coûts de test des modèles de tarification, y compris des dimensions des compteurs personnalisés de la Place de marché, nous vous recommandons de configurer la section **Tarification** de l'onglet **Tarification et disponibilité** de l'offre DEV avec des prix inférieurs à ceux de l'offre PROD. Voici quelques consignes que vous pouvez suivre lors de la définition des tarifs des plans de l'offre DEV.

***Tableau 2 : Consignes relatives à la tarification***

| Price | Commentaire |
| ------------ | ------------- |
| $0.00 | Définissez un coût total de transaction égal à zéro pour n'avoir aucun impact financier. Utilisez ce prix lorsque vous passez des appels aux API de comptage, ou pour tester les plans d'achat de votre offre pendant le développement de votre solution. |
| 0,01 - 49,99 USD | Utilisez cette fourchette de prix pour tester l'analyse, la création de rapports et le processus d'achat. |
| 50,00 à 100,00 USD | Utilisez cette fourchette de prix pour tester le paiement. Pour plus d'informations sur notre calendrier de paiement, consultez [Calendriers et processus de paiement](/partner-center/payout-policy-details). |
|||

> [!IMPORTANT]
>  Pour éviter que des frais Store Service ne soient facturés lors de votre test, ouvrez un [ticket de support](support.md) dans les sept jours suivant l’achat du test.

#### <a name="free-trial"></a>Essai gratuit

N'activez pas d'essai gratuit pour l'offre DEV.

## <a name="co-sell-with-microsoft-page"></a>Page Co-vendre avec Microsoft

Ne configurez pas l'onglet **Co-vendre avec Microsoft** de l'offre DEV.

## <a name="resell-through-csps"></a>Revendre via des fournisseurs de solutions cloud

Ne configurez pas l'onglet **Revendre via des fournisseurs de solutions cloud** de l'offre DEV.

## <a name="next-steps"></a>Étapes suivantes

- [Tester et publier une offre SaaS](test-publish-saas-offer.md)
