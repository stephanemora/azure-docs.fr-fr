---
title: Transformer des données XML avec des mappages XSLT
description: Ajouter des mappages XSLT pour transformer des données XML dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 86e7c07ba3ade77ec3913178a8dc24bb135c0a54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524513"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformer des données XML à l’aide de mappages dans Azure Logic Apps avec Enterprise Integration Pack

Pour convertir des données XML dans un autre format pour des scénarii d’intégration entreprise dans Azure Logic Apps, votre application logique peut utiliser des mappages, notamment des mappages XSLT (Extensible Stylesheet Language Transformations). Un mappage est un document XML qui décrit comment convertir les données d’un document XML dans un autre format.

Par exemple, imaginons que vous receviez régulièrement des commandes ou des factures B2B de la part d’un client qui utilise le format de date AAAMMJJ. Votre organisation, quant à elle, utilise le format de date MMJJAAA. Vous pouvez définir et utiliser un mappage qui transforme le format de date AAAMMJJ au format MMJJAAA avant d’enregistrer les détails de la commande ou de la facture dans votre base de données clients.

> [!NOTE]
> Le service Azure Logic Apps alloue de la mémoire finie pour le traitement des transformations XML. Si vous créez des applications logiques basées sur le type de ressource **Application logique (consommation)** , et que vos transformations de mappage ou de charge utile ont une consommation de mémoire élevée, ces transformations peuvent échouer, provoquant des erreurs de mémoire insuffisante. Pour éviter ce scénario, envisagez les options suivantes :
>
> * Modifiez vos cartes ou charges utiles pour réduire la consommation de mémoire.
>
> * Créez vos applications logiques à l’aide du type de ressource **Application logique (Standard)** à la place.
>
>   Ces flux de travail s’exécutent dans les instances Azure Logic Apps monolocataires, qui offrent des options dédiées et flexibles pour les ressources de calcul et de mémoire. Pour plus d’informations, consultez la documentation suivante :
>
>   * [En quoi consiste Azure Logic Apps - Type de ressource et environnements d’hôte ?](logic-apps-overview.md#resource-type-and-host-environment-differences)
>   * [Créer un flux de travail d’intégration avec les applications Azure Logic Apps monolocataires (standard)](create-single-tenant-workflows-azure-portal.md)
>   * [Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps](single-tenant-overview-compare.md)
>   * [Modèles de contrôle de l’utilisation, de facturation et de tarifs pour Azure Logic Apps](logic-apps-pricing.md)

Pour connaître les limites associées aux comptes d’intégration et aux artefacts tels que les mappages, consultez [Limites et informations de configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Un [Compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) où vous stockez vos mappages et autres artefacts pour les solutions d’intégration d’entreprise et les solutions B2B.

* Si votre mappage fait référence à un assemblage externe, vous avez besoin d’un assemblage de 64 bits. Le service de transformation exécute un processus 64 bits, de sorte que les assemblages 32 bits ne sont pas pris en charge. Si vous disposez du code source pour un assemblage 32 bits, recompilez le code en un assemblage 64 bits. Si vous n’avez pas le code source, mais que vous avez obtenu le fichier binaire d’un fournisseur tiers, obtenez la version 64 bits de ce fournisseur. Par exemple, certains vendeurs fournissent des assemblages dans des packages qui ont à la fois des versions 32 bits et 64 bits. Si vous avez l’option, utilisez la version 64 bits à la place.

* Si votre mappage référence un assembly externe, vous devez charger à la fois l’*assembly et le mappage* dans votre compte d’intégration. Vous devez [*d’abord charger votre assembly*](#add-assembly), puis charger le mappage qui le référence.

  Si la taille de votre assembly est *inférieure ou égale à 2 Mo*, vous pouvez ajouter votre assembly à votre compte d’intégration directement dans le portail Azure. Toutefois, si la taille de votre assembly ou de votre mappage est supérieure à 2 Mo, mais ne dépasse pas la [taille limite des assemblys ou des mappages](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), vous disposez des options suivantes :

  * Pour les assemblys, vous avez besoin d’un conteneur d’objets blob Azure dans lequel vous pouvez charger votre assembly ainsi que l’emplacement de ce conteneur. De cette façon, vous pourrez fournir cet emplacement lorsque vous ajouterez l’assembly à votre compte d’intégration. Pour cette tâche, vous avez besoin des éléments suivants :

    | Élément | Description |
    |------|-------------|
    | [Compte Azure Storage](../storage/common/storage-account-overview.md) | Dans ce compte, créez un conteneur d’objets blob Azure pour votre assembly. Découvrez comment [créer un compte de stockage](../storage/common/storage-account-create.md). |
    | Conteneur d’objets blob | Dans ce conteneur, vous pouvez charger votre assembly. Vous aurez également besoin de l’emplacement de ce conteneur lorsque vous ajouterez l’assembly à votre compte d’intégration. Découvrez comment [créer un conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Cet outil facilite la gestion des comptes de stockage et des conteneurs d’objets blob. Pour utiliser l’Explorateur Stockage, [téléchargez et installez l’Explorateur Stockage Azure](https://www.storageexplorer.com/). Ensuite, connectez l’Explorateur Stockage à votre compte de stockage en suivant les étapes décrites dans [Prise en main de l’Explorateur Stockage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Pour en savoir plus, consultez [Démarrage rapide : Créer un objet blob dans le stockage d’objets avec l’Explorateur Stockage Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Dans le Portail Azure, sélectionnez votre compte de stockage. Dans le menu de votre compte de stockage, sélectionnez **Explorateur Stockage**. |
    |||

  * Pour ajouter des mappages plus grands, vous pouvez utiliser l’[API REST Azure Logic Apps - Maps](/rest/api/logic/maps/createorupdate).

Vous n’avez pas besoin d’une application logique pour créer et ajouter des mappages. Toutefois, pour utiliser un mappage, votre application logique doit être associée au compte d’intégration dans lequel vous stockez ce mappage. Découvrez comment [lier des applications logiques à des comptes d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Si vous n’avez pas encore d’application logique, découvrez [comment créer des applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Ajouter des assemblys référencés

1. Dans le [portail Azure](https://portal.azure.com) connectez-vous avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter votre assembly, par exemple :

1. Dans le menu de votre compte d’intégration, sélectionnez **Vue d’ensemble**. Sous **Paramètres**, sélectionnez **Assemblages**.

1. Dans la barre d’outils du volet **Assemblages**, sélectionnez **Ajouter**.

En fonction de la taille de votre assembly, suivez les étapes de chargement pour les assemblys de taille [inférieure ou égale à 2 Mo](#smaller-assembly) ou pour les assemblys dont la taille est comprise entre [2 Mo et 8 Mo](#larger-assembly). Pour connaître le nombre limite d’assemblages que peut comprendre un compte d’intégration, consultez [Limites et configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Si vous changez votre assembly, vous devez également mettre à jour votre mappage, qu’il comporte, ou non, des changements.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Ajouter des assemblys de taille inférieure ou égale à 2 Mo

1. Sous **Ajouter un assembly**, entrez un nom pour votre assembly. L’option **Fichier de petite taille** doit rester cochée. À côté de la zone **Assemblage**, choisissez l’icône de dossier. Recherchez puis sélectionnez l’assemblage en chargement.

   Une fois l’assemblage sélectionné, la propriété **Nom de l’assemblage** affiche automatiquement le nom de fichier de l’assemblage.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Une fois que le chargement de votre fichier d’assembly est terminé, l’assembly s’affiche dans la liste **Assemblys**. Dans le volet **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre assemblage téléchargé.

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Ajouter des assemblys de taille supérieure à 2 Mo

Pour ajouter des assemblys plus volumineux, vous pouvez charger votre assembly dans un conteneur d’objets blob Azure de votre compte de stockage Azure. Les étapes pour ajouter des assemblys varient selon que votre conteneur d’objets blob dispose, ou non, d’un accès en lecture public. Effectuez donc les étapes suivantes pour vérifier si votre conteneur d’objets blob dispose d’un accès en lecture public : [Définir le niveau d’accès public pour le conteneur d’objets blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Vérifier le niveau d’accès du conteneur

1. Ouvrez l’Explorateur de stockage Azure. Dans la fenêtre de l’Explorateur, développez votre abonnement Azure, si ce n’est déjà fait.

1. Développez **Comptes de stockage** > {*votre compte de stockage*} > **Conteneurs d’objets blob**. Sélectionnez votre conteneur blob.

1. Dans le menu contextuel de votre conteneur d’objets blob, sélectionnez **Définir le niveau d’accès public**.

   * Si votre conteneur d’objets blob dispose au minimum d’un accès public, sélectionnez **Annuler**, puis suivez les étapes fournies plus loin sur cette page : [Charger vers des conteneurs avec un accès public](#public-access-assemblies)

     ![Accès public](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Si votre conteneur d’objets blob ne dispose pas d’un accès public, sélectionnez **Annuler**, puis suivez les étapes fournies plus loin sur cette page : [Charger vers des conteneurs avec un accès public](#no-public-access-assemblies)

     ![Aucun accès public](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Charger dans les conteneurs avec accès public

1. Chargez l’assembly dans votre compte de stockage. Dans la fenêtre de droite, sélectionnez **Télécharger**.

1. Une fois son chargement terminé, sélectionnez votre assembly. Dans la barre d’outils, sélectionnez **Copier l’URL** pour copier l’URL de l’assemblage.

1. Retournez au portail Azure où le volet **Ajouter un assembly** est ouvert. Entrez un nom pour votre assembly. Sélectionnez **Fichier volumineux (supérieur à 2 Mo)** .

   La zone **URI de contenu** s’affiche à la place de la zone **Assembly**.

1. Dans la zone **URI de contenu**, collez l’URL de votre assembly. Terminez l’ajout de votre assembly.

   Une fois que le chargement de votre assembly est terminé, celui-ci s’affiche dans la liste **Assemblys**. Dans le volet **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre assemblage téléchargé.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Charger dans les conteneurs sans accès public

1. Chargez l’assembly dans votre compte de stockage. Dans la fenêtre de droite, sélectionnez **Télécharger**.

1. Une fois le chargement terminé, générez une signature d’accès partagé (SAP) pour votre assembly. Dans le menu contextuel de l’assembly, sélectionnez **Obtenir une signature d’accès partagé**.

1. Dans le volet **Signature d’accès partagé**, sélectionnez **Generate container-level shared access signature URI (Générer un URI de signature d’accès partagé au niveau du conteneur)**  > **Créer**. Une fois que vous avez généré l’URL de signature d’accès partagé, à côté de la zone **URL**, sélectionnez **Copier**.

1. Retournez au portail Azure où le volet **Ajouter un assembly** est ouvert. Entrez un nom pour votre assembly. Sélectionnez **Fichier volumineux (supérieur à 2 Mo)** .

   La zone **URI de contenu** s’affiche à la place de la zone **Assembly**.

1. Dans la zone **URI de contenu**, collez l’URI de signature d’accès partagé généré précédemment. Terminez l’ajout de votre assembly.

Une fois que le chargement de votre assembly est terminé, celui-ci s’affiche dans la liste **Assemblys**. Dans le volet **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre assemblage téléchargé.

## <a name="create-maps"></a>Créer des mappages

Pour créer un document XSLT (Extensible Stylesheet Language Transformation) que vous pouvez utiliser comme carte, vous pouvez utiliser Visual Studio 2015 ou 2019 pour créer un projet d’intégration à l’aide de [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). Dans ce projet, vous pouvez créer un fichier de mappage d’intégration qui vous permet de représenter graphiquement les éléments de deux fichiers de schéma XML. Une fois le projet créé, vous obtenez un document XSLT. Pour connaître le nombre limite de mappages que peut comprendre un compte d’intégration, consultez [Limites et configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

## <a name="add-maps"></a>Ajouter des mappages

Une fois que vous avez chargé les assemblys que votre mappage référence, vous pouvez charger votre mappage.

1. Si vous n’êtes pas déjà connecté, connectez-vous au [portail Azure](https://portal.azure.com) à l’aide des informations d’identification de votre compte Azure.

1. Si votre compte d’intégration n’est pas déjà ouvert, dans la zone de recherche principale d’Azure, entrez `integration accounts`, puis sélectionnez **Comptes d’intégration**.

1. Sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter votre mappage.

1. Dans le menu de votre compte d’intégration, sélectionnez **Vue d’ensemble**. Sous **Paramètres**, sélectionnez **Mappages**.

1. Dans la barre d’outils du volet **Mappages**, sélectionnez **Ajouter**.

1. Continuez à ajouter soit un mappage [jusqu’à 2 MB](#smaller-map) ou [supérieur à 2 MB](#larger-map).

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Ajouter des mappages de taille inférieure ou égale à 2 Mo

1. Sous **Ajouter un mappage**, entrez un nom unique pour votre mappage.

1. Sous **Type de mappage**, sélectionnez un type, par exemple : **Liquid**, **XSLT**, **XSLT 2.0** ou **XSLT 3.0**.

1. À côté de la zone **Mappage**, choisissez l’icône de dossier. Recherchez puis sélectionnez le mappage à charger, par exemple :

   Si vous avez laissé la propriété **Nom** vide, le nom de fichier du mappage s’affiche automatiquement dans cette propriété après avoir sélectionné le fichier du mappage.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Une fois que le chargement de votre fichier de mappage est terminé, le mappage s’affiche dans la liste **Mappages**.

   Sur la page **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre mappage téléchargé.

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Ajouter des mappages de taille inférieure ou égale à 2 Mo

Pour ajouter des mappages plus volumineux, utilisez l’[API REST Azure Logic Apps - Maps](/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Modifier des mappages

Pour mettre à jour un mappage existant, vous devez charger un nouveau fichier de mappage qui comporte les modifications souhaitées. Cependant, vous pouvez d’abord télécharger le mappage existant pour le modifier.

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Mappages**.

1. Une fois la page **Mappages** ouverte, sélectionnez votre mappage. Pour d’abord télécharger et modifier le mappage, dans la barre d’outils du volet **Mappages**, sélectionnez **Télécharger**, puis enregistrez le mappage.

1. Lorsque vous êtes prêt à charger le mappage mis à jour, dans le volet **Mappages**, sélectionnez le mappage que vous souhaitez mettre à jour. Dans la barre d’outils du volet **Mappages**, sélectionnez **Mettre à jour**.

1. Recherchez puis sélectionnez le mappage mis à jour que vous souhaitez charger.

   Une fois que le chargement de votre fichier de mappage est terminé, le mappage mis à jour s’affiche dans la liste **Mappages**.

## <a name="delete-maps"></a>Supprimer des mappages

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Mappages**.

1. Une fois le volet **Mappages** ouvert, sélectionnez votre mappage, puis sélectionnez **Supprimer**.

1. Pour confirmer que vous souhaitez supprimer le mappage, choisissez **Oui**.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [En savoir plus sur les schémas](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [En savoir plus sur les transformations](../logic-apps/logic-apps-enterprise-integration-transform.md)
