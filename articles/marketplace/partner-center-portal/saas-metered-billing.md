---
title: Facturation à la consommation avec le service de mesure de la consommation de la Place de marché | Place de marché Azure
description: Cette documentation est un guide pour les éditeurs de logiciels indépendants publiant des offres SaaS avec des modèles de facturation flexibles.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f1b60831e08383ee455cd3afbfbc21ed27445ffc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934183"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Facturation à la consommation avec le service de mesure de la consommation de la Place de marché

Avec le service de mesure de la consommation de la Place de marché, vous pouvez créer des offres SaaS dans le programme commercial de la Place de marché qui sont facturées en fonction d’unités non standard.  Avant de publier cette offre, vous définissez les dimensions de la facturation, comme la bande passante, ou le nombre de tickets ou d’e-mails traités.  Les clients paient ensuite en fonction de leur consommation de ces dimensions, votre système informant Microsoft des événements facturables quand ils se produisent, via l’API du service de mesure de la consommation de la Place de marché.  

## <a name="prerequisites-for-metered-billing"></a>Prérequis pour la facturation à la consommation

Pour qu’une offre SaaS utilise la facturation à la consommation, elle doit :

* Respecter toutes les exigences d’une offre pour une [vente via l’offre de Microsoft](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft), comme indiqué dans [Créer une offre SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* S’intégrer aux [API de traitement SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) pour que les clients provisionnent votre offre et s’y connectent.  
* Être configuré pour le modèle de tarification **forfaitaire** pour la facturation de votre service aux clients.  Les dimensions sont une extension facultative du modèle de tarification forfaitaire. 
* S’intégrer aux [API du service de mesure de la consommation de la Place de marché](./marketplace-metering-service-apis.md) pour informer Microsoft des événements facturables.

>[!Note]
>Le service de mesure de la consommation de la Place de marché est disponible seulement pour le modèle de facturation forfaitaire et ne s’applique pas au modèle de facturation par utilisateur.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Comment la facturation à la consommation s’adapte à la tarification

Quand il s’agit de définir l’offre avec ses modèles de tarification, il est important de comprendre la hiérarchie des offres.

* Chaque offre SaaS est configurée pour être vendue via Microsoft ou non.  Ce paramètre ne peut pas être changé après la publication d’une offre.
* Chaque offre SaaS, configurée pour être vendue via Microsoft, peut avoir un ou plusieurs plans. Un utilisateur s’abonne à l’offre SaaS, mais elle est achetée auprès de Microsoft dans le contexte d’un plan.
* Chaque plan a un modèle de tarification associé : **forfaitaire** ou **par utilisateur**. Tous les plans d’une offre doivent être associés au même modèle de tarification. Par exemple, il ne peut pas y avoir une offre où un des plans est un modèle de tarification forfaitaire et où un autre plan est un modèle de tarification par utilisateur.
* Dans chaque plan configuré pour un modèle de facturation forfaitaire, au moins un coût récurrent (qui peut être de 0 dollar) est inclus :
    * Coût récurrent **mensuel** : coût récurrent mensuel forfaitaire qui est prépayé tous les mois quand l’utilisateur achète le plan.
    * Coût récurrent **annuel** : coût récurrent annuel forfaitaire qui est prépayé tous les ans quand l’utilisateur achète le plan.
* Outre les coûts récurrents, le plan peut également inclure des dimensions facultatives utilisées pour facturer les clients pour l’utilisation non comprise dans le forfait.   Chaque dimension représente une unité facturable que votre service communiquera à Microsoft en utilisant l’[API du service de mesure de la consommation de la Place de marché](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Exemple d’offre

Par exemple, Contoso est un éditeur avec un service SaaS appelé Contoso Notification Services (CNS). CNS permet aux clients d’envoyer des notifications par e-mail ou par SMS. Contoso est inscrit en tant qu’éditeur dans l’Espace partenaires pour le programme commercial de la Place de marché afin de publier des offres destinées aux clients Azure.  Il existe deux plans associés à CNS, présentés ci-dessous :

* Plan de base
    * Envoyer 10 000 e-mails et 1 000 SMS pour 0 $/mois
    * Au-delà des 10 000 e-mails, payer 1 $ tous les 100 e-mails
    * Au-delà des 1 000 SMS, payer 0,02 $ pour chaque SMS
* Plan Premium
    * Envoyer 50 000 e-mails et 10 000 SMS pour 350 $/mois
    * Au-delà des 50 000 e-mails, payer 0,5 $ tous les 100 e-mails
    * Au-delà des 10 000 SMS, payer 0,01 $ pour chaque SMS

Un client Azure s’abonnant au service CNS peut envoyer chaque mois la quantité incluse de SMS et d’e-mails en fonction du plan sélectionné.  Contoso mesure l’utilisation jusqu’à la quantité incluse sans envoyer d’événements d’utilisation à Microsoft.  Quand les clients consomment plus que la quantité incluse, ils n’ont pas à changer de plan ou à procéder différemment.  Contoso mesure le dépassement au-delà de la quantité incluse et commence à émettre des événements d’utilisation à destination de Microsoft pour l’utilisation supplémentaire avec l’[API du service de mesure de la consommation de la Place de marché](./marketplace-metering-service-apis.md).  Microsoft facture à son tour le client pour l’utilisation supplémentaire comme spécifié par l’éditeur.

## <a name="billing-dimensions"></a>Dimensions de facturation

Les dimensions de facturation sont utilisées pour communiquer au client comment il sera facturé pour l’utilisation du logiciel, et aussi pour communiquer les événements d’utilisation à Microsoft. Elles sont définies comme suit :

* **Identificateur de dimension** : identificateur immuable référencé lors de l’émission d’événements d’utilisation.
* **Nom de la dimension** : nom d’affichage associé à la dimension, par exemple « messages SMS envoyés ».
* **Unité de mesure** : description de l’unité de facturation, par exemple « par SMS » ou « par 100 e-mails ».
* **Prix unitaire** : prix pour une unité de la dimension.  
* **Quantité incluse pour l’échéance mensuelle** : quantité de la dimension incluse par mois pour les clients qui paient les coûts mensuels récurrents ; il doit s’agir d’un entier.
* **Quantité incluse pour l’échéance annuelle** : quantité de la dimension incluse par mois pour les clients qui paient les coûts annuels récurrents ; il doit s’agir d’un entier.

Les dimensions de facturation sont partagées entre tous les plans d’une offre.  Certains attributs s’appliquent à la dimension dans tous les plans, et les autres attributs sont spécifiques à un plan.

Les attributs qui définissent la dimension proprement dite sont partagés entre tous les plans d’une offre.  Avant la publication de l’offre, une modification apportée à ces attributs pour le contexte d’un plan affectera la définition de la dimension dans tous les plans.  Une fois que vous avez publié l’offre, ces attributs ne sont plus modifiables.  Ces attributs sont :

* Identificateur
* Name
* Unité de mesure

Les autres attributs d’une dimension sont spécifiques à chaque plan et peuvent avoir des valeurs différentes d’un plan à l’autre.  Avant de publier le plan, vous pouvez modifier ces valeurs : seul ce plan sera affecté.  Une fois que vous avez publié le plan, ces attributs ne sont plus modifiables.  Ces attributs sont :

* Prix unitaire
* Quantité incluse pour les clients avec facture mensuelle 
* Quantité incluse pour les clients avec facture annuelle 

Les dimensions ont également deux concepts spéciaux, « Activé » et « Infini » :

* **Activé** indique que ce plan participe à cette dimension.  Vous pouvez ne pas activer cette option si vous créez un plan qui n’envoie pas d’événements d’utilisation basés sur cette dimension.  En outre, toutes les nouvelles dimensions ajoutées après la publication initiale d’un plan apparaissent comme « Non activées » sur le plan déjà publié.  Une dimension désactivée ne s’affiche dans aucune des listes de dimensions pour un plan visualisé par des clients.
* **Infini**, représentée par le symbole de l’infini « ∞ », indique que ce plan participe à cette dimension, mais ne mesure pas l’utilisation pour cette dimension.  Si vous voulez indiquer à vos clients que les fonctionnalités représentées par cette dimension sont incluses dans le plan, mais sans limite d’utilisation.  Une dimension dont l’utilisation est infinie apparaît dans les listes de dimensions d’un plan montrées aux clients, avec une indication établissant qu’elle n’engendrera jamais de coûts pour ce plan.

>[!Note] 
>Les scénarios suivants sont explicitement pris en charge : <br> - Vous pouvez ajouter une nouvelle dimension à un nouveau plan.  La nouvelle dimension ne sera pas activée pour les plans déjà publiés. <br> - Vous pouvez publier un plan **forfaitaire** sans aucune dimension, puis ajouter un nouveau plan et configurer une nouvelle dimension pour ce plan. La nouvelle dimension ne sera pas activée pour les plans déjà publiés.

## <a name="constraints"></a>Contraintes

### <a name="trial-behavior"></a>Comportement des évaluations

La facturation à la consommation avec le service de mesure de la consommation de la Place de marché n’est pas compatible avec une offre d’évaluation gratuite.  Il n’est pas possible de configurer un plan pour utiliser à la fois la facturation à la consommation et une évaluation gratuite.

### <a name="locking-behavior"></a>Comportement du verrouillage

Une dimension utilisée avec le service de mesure de la consommation de la Place de marché représentant une indication de la manière dont un client paye pour le service, tous les détails d’une dimension ne sont plus modifiables une fois que vous l’avez publiée.  Il est important que vos dimensions soient entièrement définies pour un plan avant la publication.
  
Une fois qu’une offre est publiée avec une dimension, les détails au niveau de l’offre pour cette dimension ne peuvent plus être modifiés :

* Identificateur
* Name
* Unité de mesure

Une fois qu’un plan est publié, les détails au niveau du plan ne peuvent plus être modifiés :

* Prix unitaire
* Quantité incluse pour l’échéance mensuelle
* Quantité incluse pour l’échéance annuelle
* Indique si la dimension est activée pour le plan

### <a name="upper-limits"></a>Limites supérieures

Le nombre maximal de dimensions pouvant être configurées pour une même offre est de 18 dimensions uniques.

## <a name="get-support"></a>Obtenir de l’aide

Si vous êtes dans un des cas suivants, vous pouvez ouvrir un ticket de support.

* Problèmes techniques liés à l’API du service de mesure de la consommation de la Place de marché.
* Un problème qui doit être remonté en raison d’une erreur ou d’un bogue de votre côté (par exemple un événement d’utilisation incorrect).
* Tout autre problème lié à la facturation à la consommation. 

Suivez les étapes ci-dessous pour envoyer votre ticket de support :

1. Accédez à la [page de support](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Les premiers menus déroulants sont automatiquement remplis pour vous. Pour le support de la Place de marché, identifiez la famille de produits comme étant **Services cloud et en ligne** et le produit comme étant **Éditeur Marketplace**.  Ne changez pas les sélections préremplies des menus déroulants.
2. Sous « Sélectionner la version du produit », sélectionnez **Gestion des offres en direct**.
3. Sous « Sélectionnez une catégorie qui décrit le mieux le problème », choisissez **Applications Saas**.
4. Sous « Sélectionnez un problème décrivant le mieux le vôtre », sélectionnez **Facturation à la consommation**.
5. Après avoir sélectionné le bouton **Suivant**, vous êtes redirigé vers la page **Détails du problème**, où vous pouvez entrer plus de détails sur votre problème.

Pour plus d’options de support pour les éditeurs, consultez [Support technique pour le programme commercial de la Place de marché dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [API du service de mesure de la consommation de la Place de marché](./marketplace-metering-service-apis.md).
