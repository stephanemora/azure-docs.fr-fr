---
title: Publier un modèle de solution | Microsoft Docs
description: Publier un modèle de solution sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 5320b8d5ca7456a6f1b0fdd1372c9f39ac1edfb0
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219407"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Publier un modèle de solution sur la Place de marché Microsoft Azure

Cet article décrit les étapes de la publication d’une offre de modèle de solution sur la Place de marché Microsoft Azure.

## <a name="prerequisites"></a>Prérequis

Les prérequis techniques et non techniques suivants s’appliquent à la publication d’un modèle de solution sur la Place de marché Microsoft Azure.

### <a name="technical"></a>Techniques

- [Comprendre les modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Modèles de démarrage rapide Microsoft Azure :

    - [Documentation sur les modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/)

    - [Documentation de démarrage rapide Azure sur GitHub](https://github.com/azure/azure-quickstart-templates)

 - [Créer le fichier d’interface utilisateur du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>Non techniques (critères de l’entreprise)

-   Votre entreprise (ou sa filiale) doit être située dans un pays expéditeur pris en charge par la Place de marché Microsoft Azure.

-   Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché Microsoft Azure.

-   Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique gratuit, payant ou accessible via une communauté.

-   Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.

-   Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché Microsoft Azure et sur le portail de gestion Microsoft Azure.

-   Acceptez les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de marché Microsoft Azure.

-   Engagez-vous à respecter les Conditions d’utilisation, la Déclaration de confidentialité Microsoft et le Contrat relatif au programme Microsoft Azure Certified.

## <a name="before-you-begin"></a>Avant de commencer

Dès que vous répondez à tous les prérequis, vous pouvez commencer à créer votre offre de modèle de solution. Avant de commencer, examinez les informations ci-dessous concernant l’offre et la référence SKU.

**Offer**

Une offre Azure Application correspond à une classe d’offre de produit d’un éditeur. Si vous voulez mettre à disposition un nouveau type de solution/d’application sur la Place de marché Microsoft Azure, la meilleure approche consiste à créer une nouvelle offre. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché Microsoft Azure.

**Référence (SKU)**

Une référence (SKU) est la plus petite unité achetable d’une offre. À l’intérieur d’une même classe de produit (offre), les références SKU vous permettent de faire la distinction entre les différentes fonctionnalités prises en charge. Par exemple, l’offre est managée ou non managée et différents modèles de facturation sont pris en charge.

Ajoutez plusieurs références SKU dans les scénarios suivants :
- Vous pouvez prendre en charge différents modèles de facturation, tels que BYOL (apportez votre propre licence) ou PAYG (payez au fur et à mesure).
- Chaque référence SKU prend en charge un ensemble différent de fonctionnalités et chaque ensemble de fonctionnalités n’affiche pas le même tarif.

Une référence SKU apparaît sous l’offre parente sur la Place de marché Microsoft Azure, tandis qu’elle apparaît en tant qu’entité achetable en tant que telle sur le portail Azure.

## <a name="to-create-a-new-offer"></a>Pour créer une offre

1.  Connectez-vous au [portail Cloud Partner](http://cloudpartner.azure.com/).

2.  Dans la barre de navigation de gauche, sélectionnez **+Nouvelle offre**, puis **Applications Azure**.

    ![Créer une offre](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Sous **Nouvelle offre**, sélectionnez **Éditeur**.

    ![Éditeur de nouvelle offre](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  Sous **Éditeur**, vous allez fournir des informations dans les vues suivantes :
    - Paramètres de l’offre
    - Références (SKU)
    - Marketplace
    - Support

Chaque vue contient un ensemble de champs que vous devez remplir. Les champs obligatoires sont indiqués par un astérisque rouge (\*).

## <a name="to-configure-offer-settings"></a>Pour configurer les paramètres de l’offre

1. Configurez les champs **Identité de l’offre** ci-dessous dans les paramètres de l’offre.

    **ID de l’offre**

     Identificateur unique de l’offre au sein du profil d’éditeur. Cet ID est visible dans les URL de produit, les modèles ARM et les états de facturation. Vous ne pouvez utiliser que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret et ne peut pas comporter plus de 50 caractères. 
    >[!Note]
    >Ce champ est verrouillé une fois l’offre publiée.

    **ID de l’éditeur**

    Liste déroulante pour le profil d’éditeur. Choisissez le profil sous lequel vous voulez publier l’offre. 
    >[!Note]
    >Ce champ est verrouillé une fois que l’offre est en ligne.

    **Nom**

    Nom d’affichage de votre offre. Ce nom apparaît sur la Place de marché Microsoft Azure et sur le portail Azure. Il ne peut pas comprendre plus de 50 caractères. Suivez les conseils ci-dessous pour le nom de l’offre :
    -  Incluez un nom de marque reconnaissable pour votre produit. 
    - N’incluez pas ici le nom de votre entreprise, sauf si l’offre est commercialisée de cette façon.
    - Si vous commercialisez cette offre sur votre propre site web, veillez à ce que le nom soit identique à celui qui apparaît sur votre site web.

2. Sélectionnez **Enregistrer** pour finaliser les paramètres de l’offre.
pour votre offre.

## <a name="to-create-skus"></a>Créer des références SKU
------------------

1. Sélectionnez **Références SKU**. 

    ![Nouvelle référence SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    Le SKU ID (ID de référence SKU) est un identificateur unique de la référence SKU au sein d’une offre. Cet ID est visible dans les URL de produit, les modèles ARM et les états de facturation. L’ID de référence SKU :
    - Ne peut pas comporter plus de 50 caractères.
    - Ne peut être constitué que de caractères alphanumériques en minuscules ou de tirets (-).
    - L’ID ne peut pas se terminer par un tiret.

    >[!Note]
    >Une fois la référence SKU ajoutée, elle figure dans la liste des références SKU dans la vue Références. Pour voir les détails d’une référence SKU, sélectionnez son nom. 

2. Sélectionnez **Nouvelle référence SKU** pour fournir les informations présentées dans la capture d’écran suivante. 

    ![Détails de la référence SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>Paramètres de la référence SKU

Indiquez les paramètres de référence SKU suivants.

- **Titre** : entrez le titre de la référence SKU. Ce titre s’affiche dans la galerie pour cet élément.

- **Résumé** : courte description de la référence SKU. (La longueur maximale est de 100 caractères.)

- **Description** : description détaillée de la référence SKU.

- **Type de référence SKU** : liste déroulante avec ces valeurs : « Managed Application (préversion) » et « Modèle de Solution ». Pour ce scénario, sélectionnez **Modèle de solution**.

- **Disponibilité cloud** : emplacement de la référence SKU. La valeur par défaut est Public Azure.

### <a name="package-details"></a>Détails du package

Une fois que vous avez terminé de renseigner les paramètres de la référence SKU, fournissez les informations suivantes sur le package.

![Détails du package](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **Version actuelle** : version du package que vous allez charger. Elle doit respecter le format - ..

- **Fichier de package** : ce package contient les fichiers suivants, compressés dans un fichier .zip.

    -   MainTemplate.json : fichier de modèle de déploiement utilisé pour déployer la solution/l’application et créer les ressources définies par la solution. Pour plus d’informations, consultez [Comment créer des fichiers de modèle de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).

    -   appliancecreateUIDefinition.json : ce fichier est utilisé par le portail Azure pour générer l’interface utilisateur pour l’approvisionnement de cette solution/application. Pour plus d’informations, consultez [Créer une interface utilisateur de portail Azure pour votre application managée](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

    >[!IMPORTANT]
    >Ce package doit contenir tous les autres modèles ou scripts imbriqués nécessaires à l’approvisionnement de cette application. Les fichiers mainTemplate.json et createUiDefinition.json doivent se trouver dans le dossier racine.

## <a name="to-configure-the-marketplace"></a>Pour configurer la Place de marché

Utilisez la vue Place de marché pour configurer les champs qui s’affichent pour l’offre sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID d’abonnement pour version préliminaire

Il s’agit de la liste des ID d’abonnement Azure dont vous voulez qu’ils aient accès à l’offre dès qu’elle est publiée. Ces abonnements autorisés vous permettent de tester l’offre préliminaire avant sa publication. Le portail Cloud Partner vous permet d’autoriser jusqu’à 100 abonnements.

### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste fournie jusqu’à 5 catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

Les exemples suivants présentent les informations de place de marché qui s’affichent sur la Place de marché Microsoft Azure et le portail Azure.

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Portail Azure**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>Instructions concernant le logo

Suivez ces instructions pour les logos chargés sur le portail Cloud Partner :

-   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.

-   Les couleurs de thème du portail Azure sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan de vos logos. Utilisez des couleurs qui mettent en valeur vos logos sur le portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples.

    >[!Note] 
    >Si vous utilisez un arrière-plan transparent, assurez-vous que le logo/texte n’est pas blanc, noir ou bleu.

-   N'utilisez pas d’arrière-plan dégradé sur le logo.

-   Évitez de placer du texte sur le logo. Cela vaut aussi pour le nom de votre entreprise ou de votre marque. L’apparence de votre logo doit être *plate* et éviter les dégradés.

-   Le logo ne doit pas être étiré.

#### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. En revanche, une fois que le logo est chargé, il ne peut plus être supprimé. Le partenaire doit suivre les instructions de la Place de marché Microsoft Azure pour les icônes Bannière.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Recommandations concernant l’icône Bannière

-   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en utilisant une police de couleur blanche. Évitez d’utiliser une couleur claire dans l’arrière-plan. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes Bannière.

-   Le nom d’affichage de l’éditeur, le titre du plan, le résumé long de l’offre et le bouton Créer sont incorporés par programmation à l’intérieur de la bannière quand l’offre est publiée. N’entrez pas de texte au moment de concevoir la bannière. Laissez un espace vide à droite du logo. Cet espace doit faire 415 x 100 pixels et être décalé de 370 px à partir de la gauche.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>Pour configurer le support

Utilisez la vue Support pour fournir les informations suivantes :

- Coordonnées du support de votre entreprise, par exemple le service ingénierie.
- Coordonnées du service clientèle.

## <a name="to-publish-the-offer"></a>Pour publier l’offre

L’étape finale consiste à publier l’offre. Sélectionnez **Publier**.
