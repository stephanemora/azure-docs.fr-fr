---
title: Configurer votre application dans le portail de publication
description: Instructions relatives à la configuration de votre application dans le portail de publication cloud.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280199"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Configurer votre application dans le portail de publication

Vous êtes maintenant prêt à configurer votre application dans le portail de publication.

## <a name="login-and-create-a-new-offer"></a>Se connecter et créer une offre

1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2. Dans la barre de navigation de gauche, cliquez sur « + Nouvelle offre », puis sélectionnez « Dynamics 365 for Customer Engagement ».

![Sélection d’une offre](./media/CRMScreenShot14.png)

1. Un nouvel affichage d’offre « Éditeur » est à présent ouvert, et nous sommes prêts à créer.

![Écran Nouvelle offre](./media/CRMScreenShot15.png)

1. Les « formulaires » à compléter apparaissent sur la gauche en mode « Éditeur ». Chaque « formulaire » se compose d’un ensemble de champs à compléter. Les champs obligatoires sont indiqués par un astérisque rouge (\*).

Il existe quatre formulaires principaux pour la création d’une offre Dynamics 365 for Customer Engagement

* Paramètres de l’offre
* Informations techniques
* Informations sur les vitrines
* Contacts

## <a name="fill-out-the-offer-settings-form"></a>Remplir le formulaire Paramètres de l’offre

Le formulaire Paramètres de l’offre est un formulaire de base. Les différents champs sont décrits ci-dessous.

### <a name="offer-id"></a>ID de l’offre

Il s’agit d’un identificateur unique pour l’offre au sein d’un profil d’éditeur. Cet ID sera visible dans les URL de produit. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée.

Par exemple, si l’éditeur **« contoso »** crée une offre dont l’ID est **« sample-WebApp »** , celle-ci se présente dans AppSource comme suit « https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview »

### <a name="publisher-id"></a>ID de l’éditeur

Cette liste déroulante vous permet de choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.

### <a name="name"></a>Nom

Ceci est le nom d’affichage de votre offre. C’est le nom qui s’affiche dans [AppSource](https://appsource.microsoft.com/). Il ne peut pas comprendre plus de 50 caractères.

Cliquez sur « Enregistrer » pour enregistrer votre progression. L’étape suivante consiste à ajouter des informations techniques pour votre offre.

## <a name="fill-out-the-technical-info-form"></a>Remplir le formulaire d’informations techniques


C’est dans le formulaire d’informations techniques que vous remplissez les informations spécifiques à votre solution Dynamics 365 for Customer Engagement. Survolez-le pour afficher des informations supplémentaires. Reportez-vous à l’exemple ci-dessous.

![Écran Informations techniques](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informations sur l’application

La plupart des éditeurs conservent les valeurs par défaut des champs Utilisateur, Numéro, Numéro et une URL de configuration d’application vide comme dans la capture d’écran ci-dessus.

### <a name="crm-package"></a>Package CRM

![Informations sur le package CRM](./media/CRMScreenShot17.png)

Voici une explication de ces champs :

* Nom de fichier de votre package : nom de fichier que vous avez créé à l’étape précédente lors de la création du fichier zip qui correspond à votre package AppSource CRM. Dans l’exemple ci-dessus, il s’agit de « Microsoft\_SamplePackage.zip ».
* URL de l’emplacement de votre package : il s’agit de l’URL du compte de Stockage Azure contenant le nom de fichier du package spécifié ci-dessus. Il s’agit de l’URL créée à l’étape 9 de la section ci-dessus.
* Un ou plusieurs packages CRM dans votre fichier de package : Sélectionnez Oui **UNIQUEMENT** si vous prenez en charge plusieurs versions de CRM avec différents packages. Pour la plupart des partenaires, ce sera « Non ». Si vous sélectionnez Oui, vous devez créer des packages AppSource pour chaque version de votre solution. _Remarque : On ne vous demande pas si vous disposez de plusieurs fichiers **zip**. Si vous avez plusieurs fichiers solution.zip mais une seule version, vous devez toujours sélectionner « Non ». L’outil de création de package les regroupe automatiquement._

### <a name="crm-package-availability"></a>Disponibilité du package CRM

Dans cette section, sélectionnez les régions de CRM dans lesquelles votre package sera disponible. Pour plus d’informations sur quelles régions de CRM desservent quels pays/régions, consultez le lien : [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Remarque : Le déploiement de « Sovereign and US Gov Cloud » en Allemagne REQUIERT UNE AUTORISATION SPÉCIALE ET UNE VALIDATION LORS DE LA CERTIFICATION

## <a name="storefront-details"></a>Informations sur les vitrines

### <a name="offer-summary"></a>Résumé de l’offre

Résumé de la proposition de valeur de votre offre. Il figurera sur la page de recherche de votre offre. Elle ne doit pas dépasser 100 caractères.

### <a name="offer-description"></a>Description de l’offre

Description qui figurera sur la page des détails de votre application. Le maximum autorisé est de 1 300 caractères

### <a name="industries"></a>Secteurs d’activité

Sélectionnez le secteur d’activité auquel s’adresse essentiellement votre application. Si votre application est apparentée à plusieurs secteurs d’activité, vous pouvez laisser ce champ vide.

### <a name="categories"></a>Catégories

Sélectionnez les catégories qui se rapportent à votre application. Sélectionnez un maximum de 3.

### <a name="app-type"></a>Type d’application

Sélectionnez le type d’essai que votre application autorisera sur AppSource. « Gratuit » signifie que votre application est gratuite. « Essai » signifie que les clients peuvent essayer votre application pendant une courte période sur AppSource. « Demande d’essai » n’est pas pris en charge pour les applications Dynamics 365 for Customer Engagement. Ne sélectionnez pas cette option.

### <a name="help-link-for-your-app"></a>Lien d’aide pour votre application

Entrez l’URL d’une page qui contient des informations d’aide associées pour votre application.

### <a name="supported-countriesregions"></a>Pays/régions pris en charge

Ce champ détermine les pays/régions dans lesquels votre offre pourra être essayée.

### <a name="supported-languages"></a>Langues prises en charge

Sélectionnez les langues prises en charge par votre application. Si votre application prend en charge d’autres langues qui ne figurent pas dans cette liste, poursuivez la publication de votre offre et envoyez-nous un message à [appsource@microsoft.com](mailto:appsource@microsoft.com) pour nous en informer.

### <a name="app-version"></a>Version de l’application

Entrez le numéro de version de votre application

### <a name="app-release-date"></a>Date de publication de l’application

Entrez la date de publication de votre application

### <a name="products-your-app-works-with-max-3"></a>Produits utilisés par votre application (3 max.)

Liste des produits spécifiques avec lesquels votre application fonctionne. Vous pouvez indiquer au maximum trois produits. Pour ajouter un produit à la liste, cliquez sur le signe plus (à côté de nouveau). Dans le champ de texte ouvert qui est créé, entrez le nom du produit avec lequel votre application fonctionne.

### <a name="search-keywords-max-3"></a>Mots clés de recherche (3 max.)

AppSource permet au client de rechercher par mots clés. Vous pouvez entrer l’ensemble des mots clés grâce auquel votre application est présentée aux clients.

Par exemple, si l’application est « Mon service de messagerie électronique », messages électroniques, messagerie, service de courrier, peuvent être des mots clés. Choisissez des mots que les utilisateurs sont susceptibles d’utiliser pour rechercher votre application dans la zone de recherche AppSource.

### <a name="hide-key"></a>Clé de masquage

Il s’agit d’une clé associée à l’URL d’aperçu de l’offre et qui permet de masquer l’affichage public de l’offre. Il ne s’agit pas d’un mot de passe. Vous pouvez entrer ici n’importe quelle chaîne.

### <a name="offer-logo-png-format-48x48"></a>Logo de l’offre (format png, 48 x 48)

Il figurera sur la page de recherche de votre application. **Seul le format png est autorisé.** Chargez une image png d’une résolution de 48 \* 48 px

### <a name="offer-logo-png-format-216x216"></a>Logo de l’offre (format png, 216 x 216)

Il figurera sur la page détaillée de votre application. **Seul le format png est autorisé.** Chargez une image png d’une résolution de 216 \* 216 px

### <a name="videos"></a>Videos

Vous pouvez charger un maximum de quatre vidéos. Pour chaque vidéo que vous souhaitez charger, vous devez indiquer le nom de la vidéo, l’URL (YouTube ou Vimeo uniquement) et la miniature à associer à la vidéo. La miniature doit être au format png et d’une résolution de 1280 \* 720 px. Pour ajouter une ou plusieurs vidéos nouvelles, cliquez sur le signe plus. Les miniatures des vidéos figureront sur la page détaillée de votre application.

### <a name="documents"></a>Documents

Vous pouvez charger un maximum de trois documents au format PDF. Vous devez indiquer le nom de chaque document que vous voulez charger et ensuite charger le document. Le document doit être au format pdf.

Pour ajouter un ou plusieurs documents nouveaux, cliquez sur le signe plus

### <a name="screenshots"></a>Captures d’écran.

Il s’agit des captures d’écran qui figureront sur la page détaillée AppSource de votre application.

### <a name="privacy-policy"></a>Politique de confidentialité

Entrez l’URL de la politique de confidentialité de votre application

### <a name="terms-of-use"></a>Conditions d’utilisation

Entrez les conditions d’utilisation de votre application. Les clients AppSource doivent accepter ces conditions pour pouvoir essayer votre application.

### <a name="support-url"></a>URL du support technique

Entrez l’URL de support de votre application.

### <a name="lead-destination"></a>Destination du prospect

Sélectionnez un système CRM dans lequel vos prospects seront stockés. Sélectionnez « Table Azure » ici si vous disposez d’un des systèmes CRM suivants : Salesforce, Marketo, Microsoft Dynamics CRM. Le système CRM que vous sélectionnez ici est celui dans lequel nous écrirons les détails des utilisateurs finaux qui essaient votre application sur AppSource (prospects). Cliquez ci-dessous sur l’URL correspondant au système CRM que vous sélectionnez pour savoir comment compléter le jeu de champs suivant.

* [Table Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Informations sur les vitrines

Les coordonnées sont utilisées uniquement pour les communications internes entre le partenaire et Microsoft. Remarque : Il est important d’utiliser une adresse e-mail qui est supervisée dans ces champs. Nous allons utiliser cette adresse électronique pour vous communiquer votre progression dans la publication sur AppSource. Seule l’URL de support sera présentée aux clients.
