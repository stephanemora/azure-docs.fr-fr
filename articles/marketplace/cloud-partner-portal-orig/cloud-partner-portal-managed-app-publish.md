---
title: Publier une application gérée Azure sur la Place de marché Microsoft Azure
description: Publier une application gérée Azure sur la Place de marché Microsoft Azure
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6e5bcd6d9923b4051d44d51ff1a2534bc5e02f41
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445658"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Publier une application gérée Azure sur la Place de marché Microsoft Azure 
========================================================

Cet article énonce les diverses étapes de la publication d’une offre Application gérée sur la Place de marché Microsoft Azure.

<a name="pre-requisites-for-publishing-a-managed-application"></a>Conditions préalables à la publication d’une application gérée 
---------------------------------------------------

Conditions préalables à la publication sur la Place de marché Microsoft Azure

1.  Techniques

    -   [Création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Modèles de démarrage rapide Microsoft Azure :

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   Créer des définitions d’interface utilisateur

        -   [Créer un fichier de définition d’interface utilisateur](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  Non techniques (critères de l’entreprise)

    -   Votre entreprise (ou sa filiale) doit être située dans un pays d’origine de la vente pris en charge par la Place de marché Azure.

    -   Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché Microsoft Azure.

    -   Il vous incombe de fournir un support technique à vos clients : gratuit, payant ou via une communauté.

    -   Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances sur les logiciels tiers.

    -   Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché Microsoft Azure et sur le portail de gestion Microsoft Azure.

    -   Vous devez accepter les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de marché Microsoft Azure.

    -   Vous devez vous engager à respecter les Conditions d’utilisation, la Déclaration de confidentialité Microsoft et le Contrat relatif au programme Microsoft Azure Certified.

<a name="how-to-create-a-new-azure-application-offer"></a>Comment créer une offre d’application Azure 
-------------------------------------------

Une fois les conditions préalables remplies, vous êtes prêt à démarrer la création de votre offre d’application gérée. Avant de commencer, voici une brève présentation de ce que sont une offre et une référence (SKU)

**Offer**

Une offre Azure Application correspond à une classe d’offre de produit d’un éditeur. Si vous avez une nouvelle solution/application à publier dans Azure Marketplace, une nouvelle offre peut être la marche à suivre. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché Microsoft Azure.

**Référence (SKU)**

Une référence (SKU) est la plus petite unité achetable d’une offre. À l’intérieur d’une même classe de produit (offre), les références (SKU) vous permettent de faire la distinction entre les fonctionnalités, la nature gérée ou non gérée de l’offre et les modèles de facturation.

Ajoutez plusieurs références (SKU) si vous souhaitez prendre en charge différents modèles de facturation, tels que BYOL (apportez votre propre licence), PAYG (payez au fur et à mesure), etc. 

Ajoutez plusieurs références (SKU) lorsque chacune d’elles prend en charge un ensemble différent et elle est tarifée différemment.

Une référence (SKU) apparaît sous l’offre parente sur la Place de marché Azure, tandis qu’elle apparaît en tant qu’entité achetable en tant que telle sur azure.com.

1.  Connectez-vous au [portail Cloud Partner](http://cloudpartner.azure.com).

2.  Dans la barre de navigation de gauche, cliquez sur \"+ New offer\" (+ Nouvelle offre), puis sélectionnez \"Azure Applications\" (Applications Azure).

    ![Nouvelle offre](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Un affichage \"Éditeur\" de nouvelle offre s’affiche pour vous permettre de commencer à créer.

4.  Les \"formulaires\" à compléter apparaissent sur la gauche en mode \"Editor\" (Éditeur). Chaque \"formulaire\" se compose d’un ensemble de champs à compléter. Les champs obligatoires sont indiqués par un astérisque rouge (\*).

    > Il existe quatre formulaires principaux pour la création d’une application gérée

    -   Paramètres de l’offre

    -   Références (SKU)

    -   Marketplace

    -   Support

<a name="how-to-fill-out-the-offer-settings-form"></a>Comment remplir le formulaire Paramètres de l’offre 
---------------------------------------

Le formulaire Paramètres de l’offre est un formulaire de base.
Les différents champs sont décrits ci-dessous.

**ID de l’offre**

Identificateur unique pour l’offre au sein d’un profil d’éditeur.
Il est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Seuls les caractères alphanumériques en minuscules ou les tirets (-) sont autorisés. Il ne peut pas se terminer par un tiret et ne peut pas dépasser 50 caractères. Ce champ est verrouillé une fois l’offre publiée.

**ID de l’éditeur**

Cette liste déroulante vous permet de choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.

**Nom**

Nom d’affichage de votre offre. C’est le nom qui apparaît sur la Place de marché Azure et sur le portail Azure. Il ne peut pas comprendre plus de 50 caractères. Il est conseillé ici d’inclure un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre entreprise, sauf si c’est le nom sous lequel l’offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.

Cliquez sur \"Enregistrer\" pour enregistrer votre progression. Étape suivante consiste à ajouter des références (SKU) pour votre offre.

<a name="how-to-create-skus"></a>Comment créer des références (SKU) 
------------------

Cliquez sur le formulaire \"Références\". Vous pouvez voir ici l’option \"Ajouter une référence (SKU)\" sur laquelle vous pouvez cliquer pour entrer un \"ID de SKU\".

![SKU de nouvelle offre](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

Le \"SKU ID\" (ID de référence) est un identificateur unique pour la référence (SKU) au sein d’une offre. Cet ID est visible dans les URL de produit, les modèles ARM et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-).
Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée. Vous pouvez avoir plusieurs références (SKU) au sein d’une même offre. Vous avez besoin d’une référence (SKU) pour chaque image que vous prévoyez de publier.

Après l’ajout d’une référence (SKU), celle-ci apparaît dans la liste des références (SKU) dans le formulaire \"Références\". Pour obtenir des détails sur une référence (SKU), cliquez sur son nom. Voici quelques détails pour certains champs.

Après avoir cliqué sur l’option \"New SKU\" (Nouvelle référence), vous devez remplir le formulaire suivant :

![Nouvelle offre : nouvelle référence SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Comment remplir la section des informations sur la référence (SKU) 

**Title** (Titre) : Indiquez un titre pour cette référence (SKU). C’est le titre qui s’affichera dans la galerie pour cet élément.

**Summary** (Résumé) : entrez un bref résumé décrivant cette référence (SKU). Ce texte s’affichera juste en dessous du titre.

**Description** : fournissez une description détaillée de la référence (SKU).

**Sku Type** (Type de référence) : les valeurs autorisées sont \"Managed Application\" (Application gérée) et \"Solution Templates\" (Modèles de solution). Dans le cas présent, vous sélectionnerez \"Managed Application\" (Application gérée).

### <a name="how-to-fill-package-details-section"></a>Comment remplir la section des informations sur le package 

Dans la section sur le package, les champs suivants doivent être remplis :

![Nouvelle offre : nouveau package SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**Current Version** (Version actuelle) : indique la version du package à charger.
Elle doit respecter le format.

**Package file** (Fichier de package) : les fichiers suivants sont compressés dans un fichier .zip.

applianceMainTemplate.json : le fichier de modèle de déploiement utilisé pour déployer la solution/l’application et créer les ressources qu’il contient. Vous trouverez plus d’informations sur la création de fichiers de modèle de déploiement ici : <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json : ce fichier est utilisé par le portail Azure à l’adresse azure.com pour générer l’interface utilisateur pour l’approvisionnement de cette solution/application. Des informations supplémentaires sur la création de ce fichier sont disponibles ici : <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json : le fichier de modèle qui contient uniquement la ressource Microsoft.Solution/appliances. Les propriétés principales de cette ressource sont les suivantes :

-   \"kind\" : la valeur doit être \"Marketplace\" dans le cas d’un scénario d’application gérée par la Place de marché
-   \"ManagedResourceGroupId\" : ID du groupe de ressources de l\'abonnement du client dans lequel toutes les ressources définies dans le fichier applianceMainTemplate.json sont déployées.
-   \"PublisherPackageId\" : la chaîne qui identifie de façon unique le package. 

La valeur doit être construite comme suit : il s’agit une concaténation de \[publisherId\].\[OfferId\]-preview\[SKUID\].\[PackageVersion\].
Chacune de ces valeurs peut être obtenue comme indiqué ci-dessous.

Ce package doit contenir tous les autres modèles imbriqués ou scripts nécessaires à l’approvisionnement correct de cette application. Les fichiers mainTemplate.json, applianceMainTemplate.json et applianceCreateUIDefinition.json doivent se trouver dans le dossier racine.

**Authorizations** : cette propriété définit le niveau d’accès des utilisateurs aux ressources des abonnements clients. Cela permet à l’éditeur de gérer l’application pour le compte du client.

-   PrincipalId : l’identificateur Azure Active Directory d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auquel ou à laquelle certaines autorisations seront accordées (comme indiqué par la définition de rôle) sur les ressources de l’abonnement du client.

-   Role Definition : liste de tous les rôles RBAC intégrés fournis par Azure AD. Vous pouvez sélectionner le rôle le mieux adapté, qui vous permettra de gérer les ressources pour le compte du client.

Plusieurs autorisations peuvent être ajoutées. Toutefois, il est recommandé de créer un groupe d’utilisateurs Active Directory et de spécifier son ID au niveau de la propriété \"PrincipalId\..  Cela permettra d’ajouter davantage d’utilisateurs au groupe d’utilisateurs sans avoir à mettre à jour la référence SKU.

Des informations supplémentaires sur le contrôle d’accès en fonction du rôle (RBAC) sont disponibles ici : <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Formulaire de la Place de marché
----------------

Le formulaire marketplace à l’intérieur d’une offre Azure Application comprend des champs qui doivent apparaître sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/). Voici quelques détails pour certains champs.

#### <a name="preview-subscription-ids"></a>ID d’abonnement pour préversion

Entrez ici la liste des ID d’abonnement Azure dont vous voulez qu’ils aient accès à l’offre dès sa publication. Ces abonnements autorisés vous permettent de tester l’offre préliminaire avant sa publication. Le portail Cloud Partner vous permet d’autoriser jusqu’à 100 abonnements.

#### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste fournie jusqu’à cinq catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

Voici certains des emplacements où apparaissent les données que vous entrez dans ce formulaire.

##### <a name="azure-marketplace"></a>Place de marché Azure

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>Portail sur azure.com

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>Instructions concernant le logo

Tous les logos chargés sur le portail Microsoft Cloud Partner doivent respecter les instructions ci-dessous :

-   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.

-   Les couleurs de thème du portail sur azure.com sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour vos logos. Utilisez des couleurs qui mettent vos logos en évidence dans le portail sur azure.com.
    Nous vous recommandons d’utiliser des couleurs primaires simples. **Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont pas blancs, noirs ou bleus.**

-   N'utilisez pas d’arrière-plan dégradé sur le logo.

-   Évitez de placer du texte, même s’il s’agit du nom votre société ou de votre marque, sur le logo.
    L’apparence de votre logo doit être \'plate\' et éviter les dégradés.

-   Évitez d’étirer le logo.

##### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. En revanche, une fois la bannière téléchargée, il n’est plus possible de la supprimer. Dans ce cas, le partenaire doit suivre les instructions d’Azure Marketplace pour les icônes.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Recommandations concernant l’icône Bannière

-   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en couleur de police blanche. Évitez de conserver toute couleur claire dans l’arrière-plan de l’icône de bannière. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes de bannière.

-   Quand l’offre est publiée, le nom d’affichage de l’éditeur, le titre du plan, le résumé long de l’offre et le bouton Créer sont incorporés par programmation à l’intérieur de la bannière. Vous n’avez pas besoin de saisir de texte lorsque vous concevez la bannière. Conservez des espaces vides à droite du nom d’affichage de l’éditeur, du titre du plan et du résumé long de l’offre, etc. Ils sont inclus par programmation.
    Les espaces vides pour le texte doivent être de 415 x 100 sur la droite, décalés de 370 px à partir de la gauche.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>Formulaire de prise en charge
------------

Le formulaire suivant à remplir est celui relatif à la prise en charge. Ce formulaire vous demande de spécifier les contacts de votre entreprise.  Par exemple, les contacts du support technique et du support client.

<a name="how-to-publish-an-offer"></a>Comment publier une offre
-----------------------

À présent que votre offre est élaborée, l’étape suivante consiste à la publier sur la Place de marché Azure. Cliquez sur le bouton \"Publier\" pour démarrer le traitement de mise en ligne de votre offre.
