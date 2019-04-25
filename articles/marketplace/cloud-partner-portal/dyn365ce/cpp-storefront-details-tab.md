---
title: Onglet Détails de la vitrine d’une offre Dynamics 365 for Customer Engagement - Place de marché Azure | Microsoft Docs
description: Utilisez l’onglet Détails de la vitrine afin de spécifier les informations de ventes et de marketing pour une offre Dynamics 365 for Customer Engagement.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: pbutlerm
ms.openlocfilehash: f9cbf5033d0ffb84959de94db052626dd1d131c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311271"
---
# <a name="dynamics-365-for-customer-engagement-storefront-details-tab"></a>Onglet Détails de la vitrine Dynamics 365 for Customer Engagement

Utilisez l’onglet **Détails de la vitrine** pour fournir des artefacts et des informations de ventes et de marketing.  Cet onglet contient les six sections suivantes : **Détails de l’offre**, **Détails de l’annonce**, **Artefacts marketing**, **Informations légales**, **Service clientèle**, **Gestion des prospects**.  Une étiquette comportant un astérisque (*) indique qu’elle est obligatoire.


## <a name="offer-details-section"></a>Section Détails de l’offre

Vous fournirez le résumé et la description de votre offre dans cette section.  Ces informations sont affichées en priorité dans la Place de marché.
 
![Section Détails de l’offre de l’onglet Détails de la vitrine](./media/storefront-tab-offer-details.png)

Le tableau suivant décrit les champs de cet onglet : 

|    **Champ**          |  **Description**               |
|  ---------------      |  ---------------               |
|  Résumé de l’offre        | Résumé de la proposition de valeur de votre offre. Il figurera sur la page de recherche de votre offre. Le format est du texte brut avec un maximum de 100 caractères. |
|  Description de l’offre    | Description qui figurera dans la page des détails de votre application. Le format est HTML simple (y compris les balises p, em, ul, li, ol et header) avec un maximum de 1 300 caractères.  |
|  |  | 


## <a name="listing-details-section"></a>Section Détails de l’annonce

Vous fournirez des détails sur l’annonce de la Place de marché dans cette section.

![Section Détails de l’annonce de l’onglet Détails de la vitrine](./media/storefront-tab-listing-details.png)

Le tableau suivant décrit les champs de cet onglet : 

|    **Champ**          |  **Description**               |
|  ---------------      |  ---------------               |
|  Secteurs d’activité           | Sélectionnez les secteurs d’activité (deux maximum) qui correspondent à l’application. Si aucune ne convient, vous pouvez ne sélectionner aucune option.  |
|  Categories           | Sélectionnez les catégories qui se rapportent à l’application. Choisissez entre une et trois options.  |
|  Type d’application             | Sélectionnez le type d’essai activé pour l’application sur AppSource. **Gratuit** indique que votre application peut être utilisée sans frais, **Essai** indique que les clients peuvent essayer votre application pendant une courte période sans frais, et **Me contacter** indique que les clients peuvent demander un essai de l’application sur AppSource.  |
| Lien d’aide pour votre application | URL de la documentation en ligne ou d’autres ressources d’aide pour l’application.  |
| Pays/régions pris en charge | Une boîte de dialogue contextuelle permet de sélectionner les régions géographiques où l’application est disponible.  La distribution dans le monde entier est la valeur par défaut. |
| Langues prises en charge   | Sélectionnez les langues prises en charge par votre application. Actuellement, seules quelques options de langue sont prises en charge.  (Si votre application prend en charge des langues supplémentaires qui ne sont pas dans cette liste, continuer à publier votre offre et envoyer par courrier électronique à ces informations pour : appsource\@microsoft.com.)  |
| Version de l’application           | Numéro de version de l’application    |
| Date de publication de l’application      | Date de publication de l’application |
| Produits utilisés par votre application | Produits ou services avec lesquels votre application interagit. Vous pouvez indiquer au maximum trois produits. Pour lister un produit, cliquez sur **+ Nouveau** et entrez le nom d’un produit dans la zone de texte affichée.  |
| Mots clés de recherche       | Ensemble de mots clés associés à l’application, avec un maximum de trois mots clés.  AppSource permet au client de rechercher par mots clés.  Choisissez des mots généraux et courants que les utilisateurs sont susceptibles d’utiliser pour rechercher votre application.  |
|  Clé de masquage             | Clé secrète combinée à l’URL de l’offre pour vous permettre d’afficher un aperçu de l’offre avant sa mise en ligne.  Cette clé n’est *pas* un mot de passe.  Elle peut contenir uniquement des caractères alphanumériques.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Section Artefacts marketing

Dans cette section, vous allez fournir des supports marketing : images de logo, vidéos promotionnelles ou d’instructions, documentation de produit et captures d’écran de l’interface utilisateur.

![Section Artefacts marketing de l’onglet Détails de la vitrine](./media/storefront-tab-market-artifacts.png)

Le tableau suivant décrit les champs de cet onglet : 

|      Champ            |    Description                 |
|  ---------------      |  ---------------               |
| Logo de l’offre (format png, 48 x 48) | Chargez une image qui sera affichée dans la page de recherche de votre application. Elle doit être au format PNG et avoir une taille de 48 x 48 pixels. |
| Logo de l’offre (format png, 216 x 216) | Chargez une image qui sera affichée dans la page de détails de votre application. Elle doit être au format PNG et avoir une taille de 216 x 216 pixels. |
| Videos                | Chargez des vidéos promotionnelles ou d’instructions pour l’application (maximum quatre).  Pour chaque vidéo, renseignez le nom de la vidéo, l’URL (YouTube ou Vimeo uniquement) et une miniature associée au format PNG et de dimension 1 280 x 720 pixels.  | 
| Documents             | Chargez des documents promotionnels ou d’instructions de produit au format PDF (maximum trois).  Spécifiez un nom pour chaque document.  |
| Captures d’écran.           | Chargez un maximum de cinq images d’interface utilisateur de l’application au format PNG et de dimension 1 280 x 720 pixels.  Ces captures d’écran apparaîtront dans la page détaillée AppSource de votre application. |
|  |  |


## <a name="legal-section"></a>Section Informations légales

Vous allez fournir la *politique de confidentialité* et les *conditions d’utilisation* de votre application dans cette section.

![Section Informations légales de l’onglet Détails de la vitrine](./media/storefront-tab-legal.png)

Le tableau suivant décrit les champs de cet onglet : 

|      Champ            |    Description                 |
|  ---------------      |  ---------------               |
| URL de la politique de confidentialité    | URL de la politique de confidentialité en ligne pour votre application.  |
| Conditions d’utilisation          | Conditions d’utilisation de votre application au format texte brut.  Les clients AppSource doivent accepter ces conditions avant de pouvoir essayer de votre application. |
|  |  |


## <a name="customer-support-section"></a>Section Service clientèle

Dans cette section, vous fournirez l’**URL du support technique** pour votre application.


## <a name="lead-management-section"></a>Section Gestion des prospects

Dans cette section, vous allez fournir le mécanisme permettant de gérer les prospects générés par votre annonce de l’offre.  Les prospects sont généralement stockés dans des systèmes de Gestion de la relation client (CRM).  Les destinations suivantes sont prises en charge : **Aucune**, **Table Azure**, **Dynamics CRM Online**, **Point de terminaison HTTPS**, **Marketo** et **Salesforce** .  Pour plus d’informations, consultez [Obtenir des prospects](../../cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads.md).


## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous allez fournir des informations de support technique et client sous l’[onglet Contacts](./cpp-contacts-tab.md). 
