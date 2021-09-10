---
title: Ajouter des schémas pour valider du code XML
description: Ajouter des schémas pour valider les documents XML dans les Applications logiques Azure avec le Pack d’intégration d’entreprise
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: dc55e70e9ceaa9546890b2ed7dd5df0d705b1a92
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099988"
---
# <a name="add-schemas-to-validate-xml-in-azure-logic-apps"></a>Ajouter des schémas pour valider du code XML dans les Applications logiques Azure

Pour vérifier que les documents utilisent du code XML valide et qu’ils contiennent les données attendues au format prédéfini pour les scénarios d’intégration entreprise dans Azure Logic Apps, votre application logique peut utiliser des schémas. Un schéma peut également valider des messages que les applications logiques échangent dans les scénarios B2B.

Si vous débutez avec les applications logiques, consultez la documentation suivante :

* [En quoi consistent les Applications Azure Logic - Type de ressources et environnements d’hôte ?](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [Créer un flux de travail d’intégration avec des Applications logiques Azure monolocataires (Standard)](create-single-tenant-workflows-azure-portal.md)

* [Créer des flux de travail d’applications logiques monolocataires](create-single-tenant-workflows-azure-portal.md)

* [Modèles de mesure de l’utilisation, de facturation et de tarification pour les Applications logiques Azure](logic-apps-pricing.md)

## <a name="limits"></a>Limites

* Pour les ressources d’applications logiques **Standard**, il n’existe aucune limite concernant les tailles de fichier de schéma.

* Pour les ressources d’applications logiques de **Consommation**, il existe des limites pour les comptes et artefacts d’intégration comme les schémas. Pour plus d’informations, consultez [Informations relatives aux limites et à la configuration pour les Applications logiques Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

  En règle générale, lorsque vous utilisez un compte d’intégration avec votre workflow et que vous souhaitez valider le code XML, vous ajoutez ou chargez le schéma dans ce compte. Si vous référencez ou importez un schéma absent de votre compte d’intégration, vous pouvez recevoir l’erreur suivante lorsque vous utilisez l’élément `xsd:redefine` :

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  Pour y remédier, vous devez utiliser l’élément `xsd:import` ou `xsd:include` plutôt que `xsd:redefine`, ou utiliser un URI.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Si vous utilisez le type de ressource **Application logique (standard)** , vous n’avez pas besoin de compte d’intégration. Au lieu de cela, vous pouvez ajouter des schémas directement à votre ressource d’application logique dans le portail Azure ou Visual Studio Code. Vous pouvez ensuite utiliser ces schémas sur plusieurs flux de travail au sein de la *même ressource d’application logique*.

* Si vous utilisez le type de ressource **Application logique (Consommation)** , vous devez posséder une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) où vous pouvez stocker vos schémas et autres artefacts à utiliser dans les solutions d’intégration d’entreprise et les solutions B2B. Cette ressource doit remplir les conditions suivantes :

  * Est associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que la ressource d’application logique dans laquelle vous envisagez d’utiliser l’action de validation XML.

  * Est [liée](logic-apps-enterprise-integration-create-integration-account.md#link-account) à votre ressource d’application logique lorsque vous souhaitez utiliser des schémas.

    Pour créer et ajouter des schémas à utiliser dans les flux de travail d’application logique de consommation, vous n’avez pas encore besoin d’une ressource d’application logique. Toutefois, lorsque vous êtes prêt à utiliser ces schémas dans vos flux de travail, votre ressource d’application logique requiert un compte d’intégration lié qui stocke ces schémas.

* Si la taille de votre schéma est [inférieure ou égale à 2 Mo](#smaller-schema), vous pouvez ajouter votre schéma à votre compte d’intégration *directement* à partir du portail Azure. Toutefois, si la taille de votre schéma est supérieure à 2 Mo, mais qu’elle ne dépasse pas la [taille limite des schémas](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), vous pouvez charger votre schéma vers un compte de stockage Azure. Pour ajouter ce schéma à votre compte d’intégration, vous pouvez associer votre compte de stockage à votre compte d’intégration. Pour cette tâche, voici les éléments dont vous avez besoin :

    | Élément | Description |
    |------|-------------|
    | [Compte Azure Storage](../storage/common/storage-account-overview.md) | Dans ce compte, créez un conteneur d’objets blob Azure pour votre shéma. Découvrez comment [créer un compte de stockage](../storage/common/storage-account-create.md). |
    | Conteneur d’objets blob | Dans ce conteneur, vous pouvez charger votre schéma. Vous aurez également besoin de l’URI de contenu de votre conteneur plus tard, lorsque vous ajouterez le schéma à votre compte d’intégration. Découvrez comment [créer un conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Cet outil facilite la gestion des comptes de stockage et des conteneurs d’objets blob. Pour utiliser l’Explorateur de stockage, choisissez une étape : <p>- Dans le portail Azure, sélectionnez votre compte de stockage. Dans le menu de votre compte de stockage, sélectionnez **Explorateur Stockage**. <p>- Pour la version de bureau, [téléchargez et installez l’Explorateur de stockage Azure](https://www.storageexplorer.com/). Ensuite, connectez l’Explorateur Stockage à votre compte de stockage en suivant les étapes décrites dans [Prise en main de l’Explorateur Stockage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Pour en savoir plus, consultez [Démarrage rapide : Créer un objet blob dans le stockage d’objets avec l’Explorateur Stockage Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).  |
    |||

  Pour ajouter des cartes de plus grande taille pour la consommation des ressources d’application logique, vous pouvez également utiliser [API REST Applications logiques Azure - Schémas](/rest/api/logic/schemas/create-or-update). Toutefois, pour les ressources d’application logique Standard, l’API REST Applications logiques Azure n’est pas disponible actuellement.

<a name="add-schemas"></a>

## <a name="add-schemas"></a>Ajouter des schémas

### <a name="consumption-resource"></a>[Ressource de consommation](#tab/consumption-1)

1. Dans le [portail Azure](https://portal.azure.com) connectez-vous avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter votre schéma.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Schémas**.

1. Dans la barre d’outils du volet **Schémas**, sélectionnez **Ajouter**.

En fonction de la taille de votre schéma (.xsd), suivez les étapes de chargement pour les schémas de taille [inférieure ou égale à 2 Mo](#smaller-schema) ou pour les schémas dont la taille est comprise entre [2 Mo et 8 Mo](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Ajouter des schémas de taille inférieure ou égale à 2 Mo

1. Sous **Ajouter un schéma**, saisissez un nom pour votre schéma. L’option **Fichier de petite taille** doit rester cochée. À côté de la zone **Schéma**, choisissez l’icône de dossier. Recherchez puis sélectionnez le schéma à charger.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Lorsque le chargement de votre schéma est terminé, celui-ci s’affiche dans la liste **Schémas**.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Ajouter des schémas de taille supérieure à 2 Mo

Pour ajouter des schémas plus volumineux, vous pouvez charger votre schéma dans un conteneur d’objets blob Azure de votre compte de stockage Azure. Les étapes pour ajouter des schémas varient selon que votre conteneur d’objets blob dispose ou non d’un accès en lecture public. Effectuez donc les étapes suivantes pour vérifier si votre conteneur d’objets blob dispose d’un accès en lecture public : [Définir le niveau d’accès public pour le conteneur d’objets blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Vérifier le niveau d’accès du conteneur

1. Ouvrez l’Explorateur de stockage Azure. Dans la fenêtre de l’Explorateur, développez votre abonnement Azure, si ce n’est déjà fait.

1. Développez **Comptes de stockage** > {*votre compte de stockage*} > **Conteneurs d’objets blob**. Sélectionnez votre conteneur blob.

1. Dans le menu contextuel de votre conteneur d’objets blob, sélectionnez **Définir le niveau d’accès public**.

   * Si votre conteneur d’objets blob dispose au minimum d’un accès public, sélectionnez **Annuler**, puis suivez les étapes fournies plus loin sur cette page : [Charger vers des conteneurs avec un accès public](#public-access)

     ![Accès public](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Si votre conteneur d’objets blob ne dispose pas d’un accès public, sélectionnez **Annuler**, puis suivez les étapes fournies plus loin sur cette page : [Charger vers des conteneurs sans accès public](#public-access)

     ![Aucun accès public](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Charger dans les conteneurs avec accès public

1. Téléchargez le schéma vers votre compte de stockage. Dans la fenêtre de droite, sélectionnez **Télécharger**.

1. Une fois son chargement terminé, sélectionnez votre schéma. Dans la barre d’outils, choisissez **Copier l’URL** pour copier l’URL du schéma.

1. Retournez au portail Azure où le volet **Ajouter un schéma** est ouvert. Entrez un nom pour votre assembly. Sélectionnez **Fichier volumineux (supérieur à 2 Mo)** .

   La zone **URI de contenu** s’affiche à la place de la zone **Schéma**.

1. Dans la zone **URI de contenu**, collez l’URL de votre schéma. Terminez l’ajout de votre schéma.

Lorsque le chargement de votre schéma est terminé, celui-ci s’affiche dans la liste **Schémas**. Sur la page **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche votre schéma téléchargé.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Charger dans les conteneurs sans accès public

1. Téléchargez le schéma vers votre compte de stockage. Dans la fenêtre de droite, sélectionnez **Télécharger**.

1. Une fois le chargement terminé, générez une signature d’accès partagé (SAP) pour votre schéma. Dans le menu contextuel de votre schéma, sélectionnez **Obtenir une signature d’accès partagé**.

1. Dans le volet **Signature d’accès partagé**, sélectionnez **Generate container-level shared access signature URI (Générer un URI de signature d’accès partagé au niveau du conteneur)**  > **Créer**. Une fois que vous avez généré l’URL de signature d’accès partagé, à côté de la zone **URL**, sélectionnez **Copier**.

1. Retournez au portail Azure où le volet **Ajouter un schéma** est ouvert. Choisissez **Fichier volumineux**.

   La zone **URI de contenu** s’affiche à la place de la zone **Schéma**.

1. Dans la zone **URI de contenu**, collez l’URI de signature d’accès partagé généré précédemment. Terminez l’ajout de votre schéma.

Lorsque le chargement de votre schéma est terminé, celui-ci s’affiche dans la liste **Schémas**. Sur la page **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche votre schéma téléchargé.

### <a name="standard-resource"></a>[Ressource standard](#tab/standard-1)

#### <a name="azure-portal"></a>Portail Azure

1. Dans le menu de la ressource votre application logique, sous **Paramètres**, sélectionnez **Schémas**.

1. Dans la barre d’outils du volet **Schémas**, sélectionnez **Ajouter**.

1. Sous **Ajouter un schéma**, saisissez un nom unique pour votre schéma.

1. À côté de la zone **Schéma**, choisissez l’icône de dossier. Sélectionnez le schéma à télécharger.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Lorsque le chargement de votre schéma est terminé, celui-ci s’affiche dans la liste **Schémas**. Sur la page **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre schéma téléchargé.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. Dans la structure du projet de votre application logique, ouvrez le dossier **Artefacts**, puis le dossier **Schémas**.

1. Dans le dossier **Schémas**, ajoutez votre schéma.

---

## <a name="edit-schemas"></a>Modification de schémas

Pour mettre à jour un schéma existant, vous devez charger un nouveau fichier de schéma qui comporte les modifications souhaitées. Cependant, vous pouvez d’abord télécharger le schéma existant pour le modifier.

### <a name="consumption-resource"></a>[Ressource de consommation](#tab/consumption-2)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma. Pour d’abord télécharger et modifier le schéma, dans la barre d’outils du volet **Schémas**, sélectionnez **Télécharger**, puis enregistrez le schéma.

1. Lorsque vous êtes prêt à charger le schéma mis à jour, dans le volet **Schémas**, sélectionnez le schéma que vous souhaitez mettre à jour. Dans la barre d’outils du volet **Schémas**, sélectionnez **Mettre à jour**.

1. Recherchez puis sélectionnez le schéma mis à jour que vous souhaitez charger.

   Lorsque le chargement du schéma mis à jour est terminé, celui-ci s’affiche dans la liste **Schémas**.

### <a name="standard-resource"></a>[Ressource standard](#tab/standard-2)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource de votre application logique, si elle n’est pas encore ouverte.

1. Dans le menu de la ressource votre application logique, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma. Pour d’abord télécharger et modifier le schéma, dans la barre d’outils du volet **Schémas**, sélectionnez **Télécharger**, puis enregistrez le schéma.

1. Dans la barre d’outils du volet **Schémas**, sélectionnez **Ajouter**.

1. Sous **Ajouter un schéma**, saisissez un nom unique pour votre schéma.

1. À côté de la zone **Schéma**, choisissez l’icône de dossier. Sélectionnez le schéma à télécharger.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Lorsque le chargement du schéma mis à jour est terminé, celui-ci s’affiche dans la liste **Schémas**.

---

## <a name="delete-schemas"></a>Suppression de schémas

### <a name="consumption-resource"></a>[Ressource de consommation](#tab/consumption-3)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma, puis sélectionnez **Supprimer**.

1. Pour confirmer que vous souhaitez supprimer le schéma, sélectionnez **Oui**.

### <a name="standard-resource"></a>[Ressource standard](#tab/standard-3)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource de votre application logique, si elle n’est pas encore ouverte.

1. Dans le menu de la ressource votre application logique, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma, puis sélectionnez **Supprimer**.

1. Pour confirmer que vous souhaitez supprimer le schéma, sélectionnez **Oui**.

---

## <a name="next-steps"></a>Étapes suivantes

* [Valider du code XML pour des flux de travail dans les Applications logiques Azure](logic-apps-enterprise-integration-xml-validation.md)
* [Transformer du code XML pour des flux de travail dans les Applications logiques Azure](logic-apps-enterprise-integration-transform.md)