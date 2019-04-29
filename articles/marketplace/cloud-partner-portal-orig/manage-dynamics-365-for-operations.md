---
title: Comment créer une offre Dynamics 365 for Operations via le portail Cloud Partner | Microsoft Docs
description: Comment créer une offre Dynamics 365 for Operations via le portail Cloud Partner
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 93c70e038589667ae97acb86663a6179dcc81637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776724"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Comment créer une offre Dynamics 365 for Operations via le portail Cloud Partner

Le portail de publication fournit un accès basé sur les rôles, ce qui permet à plusieurs personnes de collaborer à la publication d’une offre. Consultez [Gérer les utilisateurs du portail Cloud](./cloud-partner-portal-manage-users.md) pour plus d’informations.

Avant de pouvoir publier une offre pour le compte d’un compte de publication, une des personnes disposant du rôle \"propriétaire\" doit s’engager à respecter les [conditions d’utilisation](https://azure.microsoft.com/support/legal/website-terms-of-use/), la [déclaration de confidentialité de Microsoft](https://www.microsoft.com/privacystatement/default.aspx) et le [contrat du programme Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Comment créer une nouvelle offre Dynamics 365 for Operations

Une fois toutes les conditions préalables réunies, vous pouvez commencer à créer votre offre Dynamics 365 for Operations.

1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2. Dans la barre de navigation de gauche, cliquez sur \"+Nouvelle offre\", puis sélectionnez \"Dynamics 365 for Operations\".
3. Un nouvel affichage d’offre \"Éditeur\" est à présent ouvert, et nous sommes prêts à créer.
4. Les \"formulaires\" à compléter apparaissent sur la gauche en mode \"Editor\". Chaque \"formulaire\" se compose d’un ensemble de champs à compléter. Les champs obligatoires sont indiqués par un astérisque rouge (\*).

Il existe quatre formulaires principaux pour la création d’une offre Dynamics 365 for Operations :

- Paramètres de l’offre
- Informations techniques
- Informations sur les vitrines
- Contacts

## <a name="how-to-fill-out-the-offer-settings-form"></a>Comment remplir le formulaire Paramètres de l’offre

Le formulaire Paramètres de l’offre est un formulaire de base. Les différents champs sont décrits ci-dessous.

### <a name="offer-id"></a>ID de l’offre

Il s’agit d’un identificateur unique pour l’offre au sein d’un profil d’éditeur. Cet ID sera visible dans les URL de produit. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée.

par exemple, si un serveur de publication de contoso de serveur de publication crée une offre avec ID de l’offre *exemple-Dynamics 365 pour les opérations*, il s’affichera dans AppSource en tant que `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\`.

### <a name="publisher-id"></a>ID de l’éditeur

Cette liste déroulante vous permet de choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.

Nom

Ceci est le nom d’affichage de votre offre. Nom qui s’affichera dans [AppSource](https://appsource.microsoft.com). Il ne peut pas comprendre plus de 50 caractères.

Cliquez sur \"Enregistrer\" pour enregistrer votre progression. L’étape suivante consiste à remplir les informations techniques de votre offre.

## <a name="how-to-fill-out-the-technical-info-form"></a>Comment remplir le formulaire d’informations techniques

Le formulaire d’informations techniques contient les informations qui seront affichées dans la page de votre offre. Les instructions des différents champs sont décrites ci-dessous.

![Écran Nouvelle offre](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Identificateur de solution

Tout d’abord, l’identificateur de votre solution.

1. Pour trouver cet identificateur, accédez à Lifecycle Services, puis sélectionnez Gestion des solutions.
2. Après avoir sélectionné la solution appropriée, vous verrez l’identificateur de la solution dans la vue d’ensemble du package. \*\*Si l’identificateur est vierge, sélectionnez Modifier et republiez votre package pour que l’identificateur de la solution apparaisse.

### <a name="validation-assets"></a>Ressources de validation

Chargez ici vos rapports d’analyse de personnalisation.

### <a name="does-solution-enable-translations"></a>La solution permet-elle les traductions ?

Sélectionnez \'Oui\' ou \'Non\'.

### <a name="does-solution-include-localizations"></a>La solution comprend-elle une localisation ?

Sélectionnez \'Oui\' ou \'Non\'.

### <a name="product-version"></a>Version du produit

Sélectionnez Nouveau AX. Puis, cliquez sur Enregistrer.

## <a name="how-to-fill-out-storefront-details-form"></a>Comment remplir le formulaire Détails de la vitrine.

D’abord les détails de l’offre.

1. **Résumé de l’offre**

    \- Entrez un bref résumé de votre solution (au maximum 100 caractères).

2. **Description de l’offre**

    \- Entrez une courte description de votre solution. Votre description doit présenter le fonctionnement de votre solution et être alignée sur votre bibliothèque BPM. Par exemple, si vous dites que vous disposez des fonctionnalités x, y, z dans votre contenu marketing, au cours de la vérification finale, nous nous assurerons que ces résultats figurent dans la bibliothèque de gestion des processus métiers dans LCS.

![écran des détails de la vitrine](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Détails de l’annonce

![Écran des détails de la vitrine](./media/publish_d365_new_offer/storefront_details.png)

1. **Secteurs d’activité** : sélectionnez un maximum de deux secteurs d’activité dans les options proposées.
2. **Catégories** : sélectionnez un maximum de trois catégories dans les options proposées.
3. **Type d’application** : sélectionnez parmi les options proposées.
4. **Lien d’aide pour votre application** : entrez le lien d’aide de votre solution.
5. **Pays/régions pris en charge** : sélectionnez parmi les options proposées.
6. **Langues prises en charge** : sélectionnez parmi les options proposées.
7. **Version de l’application** : entrez la version de votre solution qui est publiée. (par exemple 1.0.0.0)
8. **Date de publication de l’application** : entrez la date de publication de votre solution (mm/jj/aaaa).
9. **Produits utilisés par votre application** : liste des produits avec lesquels fonctionne votre application. Vous pouvez indiquer au maximum trois produits. Pour ajouter un produit à la liste, cliquez sur le signe plus (à côté de nouveau). Dans le champ de texte ouvert qui est créé, entrez le nom du produit avec lequel votre application fonctionne.
10. **Mots clés de recherche** : entrez les termes courants que les utilisateurs peuvent utiliser pour trouver votre solution. \*\*Ces mots clés ne seront pas affichés sur la place de marché.
11. **Masquer la clé :** il s’agit d’une clé associée à l’URL de préversion de l’offre et qui permet de masquer l’affichage public de l’offre. Il ne s’agit pas d’un mot de passe. Vous pouvez entrer ici n’importe quelle chaîne.

### <a name="marketing-artifacts"></a>Artefacts marketing

1. Chargez ensuite votre **logo** et des **captures d’écran**. \*\*Veuillez noter les tailles pour chaque chargement. Toutes les images doivent être au format PNG.
2. **Vidéos de démonstration** : cliquez sur \"+Nouveau\". Chargez une vidéo de démonstration de votre solution (liens YouTube ou Vimeo uniquement).\*\*. Noter que vous devez charger une miniature de la taille spécifiée pour que votre vidéo s’affiche dans l’environnement intermédiaire.
3. **Documents** : tous les documents liés à votre solution. Veillez à préciser le nom de chaque document.

### <a name="legal"></a>Informations juridiques

Ces informations renverront à votre politique de confidentialité et aux conditions d’utilisation. Entrez l’URL de la politique de confidentialité de la solution et de vos conditions d’utilisation. \*\*Le client pourra consulter ces documents sur le portail.

### <a name="customer-support"></a>Service client

L’URL de support ne sera présentée qu’à vos utilisateurs sur le portail.

### <a name="leads-management"></a>Gestion des prospects

Sélectionnez un système CRM dans lequel vos prospects seront stockés. Sélectionnez \"Table Azure\" ici si vous avez un des systèmes CRM suivants : Salesforce, Marketo, Microsoft Dynamics CRM. Le système CRM que vous sélectionnez ici est celui dans lequel nous écrirons les détails des utilisateurs finaux qui essaient votre application sur AppSource (prospects). Cliquez ci-dessous sur l’URL correspondant au système CRM que vous sélectionnez pour savoir comment compléter le jeu de champs suivant.

![Détails de la gestion des prospects](./media/publish_d365_new_offer/leads.png)

1. Pour Table Azure, reportez-vous [ici](https://aka.ms/leadsettingforazuretable).
2. Pour Dynamics CRM online, reportez-vous [ici](https://aka.ms/leadsettingfordynamicscrm)
3. Pour Marketo, reportez-vous [ici](https://aka.ms/leadsettingformarketo)
4. Pour Salesforce, reportez-vous [ici](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Remplir le formulaire Contacts.

Ces informations seront utilisées par Microsoft et support client. Entrez les coordonnées techniques et celles du support de votre entreprise, ainsi que l’URL de support de votre solution. Ces informations seront utilisées comme point de contact unique en cas de question de Microsoft sur votre solution, mais aussi pour le support technique.

![Écran des contacts de l’offre](./media/publish_d365_new_offer/Contacts.png)
