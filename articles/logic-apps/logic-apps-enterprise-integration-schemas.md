---
title: Ajouter des schémas de validation XML dans des flux de travail
description: Ajoutez des schémas pour valider les documents XML pour les flux de travail dans Azure Logic Apps à l’aide d’Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: f80ed9c7fa9aa2d291e4f045b6cfc7da695cb22b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611431"
---
# <a name="add-schemas-to-validate-xml-documents-for-workflows-in-azure-logic-apps"></a>Ajouter des schémas pour valider les documents XML pour les flux de travail dans Azure Logic Apps

Pour vérifier que les documents utilisent un XML valide et contiennent les données attendues dans le format prédéfini, le flux de travail de votre application logique peut utiliser des schémas XML avec l’action **Validation XML**. Un schéma XML décrit un document d’entreprise qui est représenté en XML à l’aide de la [définition de schéma XML (XSD)](https://www.w3.org/TR/xmlschema11-1/).

Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Pour créer des schémas, vous pouvez utiliser les outils suivants :

  * Visual Studio 2019 et l’extension [Microsoft Azure Logic Apps Enterprise Integration Tools](https://aka.ms/vsenterpriseintegrationtools).

  * Visual Studio 2015 et l’extension [Microsoft Azure Logic Apps Enterprise Integration Tools pour Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas).

   > [!IMPORTANT]
   > N’installez pas l’extension à côté de l’extension BizTalk Server. Ces deux extensions installées côte à côte risquent d’entraîner un comportement inattendu. Vérifiez que seule l’une de ces extensions est installée.

   > [!NOTE]
   > Sur les moniteurs à haute résolution, vous risquez de rencontrer un [problème d’affichage avec le concepteur de mappages](/visualstudio/designers/disable-dpi-awareness) dans Visual Studio. Pour résoudre ce problème d’affichage, [redémarrez Visual Studio en mode sans prise en charge DPI](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process) ou ajoutez la [valeur de Registre DPIUNAWARE](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos workflows d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existant dans le même emplacement ou la même région Azure que la ressource d’application logique dans laquelle vous envisagez d’utiliser l’action **Validation XML**.

  * Si vous utilisez le [**type de ressource Application logique (Consommation)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), vous devez [lier votre compte d’intégration à votre ressource d’application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account) avant de pouvoir utiliser vos artefacts dans votre workflow.

    Pour l’instant, vous n’avez pas besoin d’une ressource d’application logique pour créer et ajouter des schémas à utiliser dans les flux de travail **Application logique (consommation)** . Toutefois, lorsque vous êtes prêt à utiliser ces schémas dans vos flux de travail, votre ressource d’application logique requiert un compte d’intégration lié qui stocke ces schémas.

  * Si vous utilisez le [type de ressource **Application logique (standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), vous avez besoin d’une ressource d’application logique existante, car vous ne stockez pas de schémas dans votre compte d’intégration. Au lieu de cela, vous pouvez directement ajouter des schémas à votre ressource d’application logique à l’aide du portail Azure ou de Visual Studio Code. Vous pouvez ensuite utiliser ces schémas sur plusieurs flux de travail au sein de la *même ressource d’application logique*.

    Vous avez tout de même besoin d’un compte d’intégration pour stocker d’autres artefacts, tels que les partenaires, les contrats et les certificats, en plus d’utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) et [EDIFACT](logic-apps-enterprise-integration-edifact.md). Toutefois, vous n’avez pas besoin de lier votre ressource d’application logique à votre compte d’intégration ; la fonctionnalité de liaison n’existe donc pas. Votre compte d’intégration doit encore répondre à d’autres exigences, telles que l’utilisation du même abonnement Azure et le même emplacement que votre ressource d’application logique.

    > [!NOTE]
    > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Si la taille de votre schéma est [inférieure ou égale à 2 Mo](#smaller-schema), vous pouvez ajouter votre schéma à votre compte d’intégration *directement* à partir du portail Azure. Toutefois, si la taille de votre schéma est supérieure à 2 Mo, mais qu’elle ne dépasse pas la [taille limite des schémas](logic-apps-limits-and-config.md#artifact-capacity-limits), vous pouvez charger votre schéma vers un compte de stockage Azure. Pour ajouter ce schéma à votre compte d’intégration, vous pouvez associer votre compte de stockage à votre compte d’intégration. Pour cette tâche, voici les éléments dont vous avez besoin :

    | Élément | Description |
    |------|-------------|
    | [Compte Azure Storage](../storage/common/storage-account-overview.md) | Dans ce compte, créez un conteneur d’objets blob Azure pour votre shéma. Découvrez comment [créer un compte de stockage](../storage/common/storage-account-create.md). |
    | Conteneur d’objets blob | Dans ce conteneur, vous pouvez charger votre schéma. Vous aurez également besoin de l’URI de contenu de votre conteneur plus tard, lorsque vous ajouterez le schéma à votre compte d’intégration. Découvrez comment [créer un conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Cet outil facilite la gestion des comptes de stockage et des conteneurs d’objets blob. Pour utiliser l’Explorateur de stockage, choisissez une étape : <p>- Dans le portail Azure, sélectionnez votre compte de stockage. Dans le menu de votre compte de stockage, sélectionnez **Explorateur Stockage**. <p>- Pour la version de bureau, [téléchargez et installez l’Explorateur de stockage Azure](https://www.storageexplorer.com/). Ensuite, connectez l’Explorateur Stockage à votre compte de stockage en suivant les étapes décrites dans [Prise en main de l’Explorateur Stockage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Pour en savoir plus, consultez [Démarrage rapide : Créer un objet blob dans le stockage d’objets avec l’Explorateur Stockage Azure](../storage/blobs/quickstart-storage-explorer.md).  |
    |||

  Pour ajouter des schémas de plus grande taille pour le type de ressource [Application logique (consommation)](/rest/api/logic/schemas/create-or-update), vous pouvez également utiliser l’**API REST Azure Logic Apps – Schémas**. Toutefois, pour le type de ressource **Application logique (standard)** , l’API REST Azure Logic Apps n’est pas disponible actuellement.

## <a name="limits"></a>Limites

* Pour le type de ressource **Application logique (standard)** , il n’existe aucune limite aux tailles de fichier de schéma.

* Pour le type de ressource **Application logique (consommation)** , il existe des limites pour les comptes d’intégration et les artefacts comme les schémas. Pour plus d’informations, consultez [Informations relatives aux limites et à la configuration pour les Applications logiques Azure](logic-apps-limits-and-config.md#integration-account-limits).

  En règle générale, lorsque vous utilisez un compte d’intégration avec votre workflow et que vous souhaitez valider le code XML, vous ajoutez ou chargez le schéma dans ce compte. Si vous référencez ou importez un schéma absent de votre compte d’intégration, vous pouvez recevoir l’erreur suivante lorsque vous utilisez l’élément `xsd:redefine` :

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  Pour y remédier, vous devez utiliser l’élément `xsd:import` ou `xsd:include` plutôt que `xsd:redefine`, ou utiliser un URI.

<a name="add-schema"></a>

## <a name="add-schemas"></a>Ajouter des schémas

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com) connectez-vous avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter votre schéma.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Schémas**.

1. Dans la barre d’outils du volet **Schémas**, sélectionnez **Ajouter**.

En fonction de la taille de votre schéma (.xsd), suivez les étapes de chargement pour les schémas de taille [inférieure ou égale à 2 Mo](#smaller-schema) ou pour les schémas dont la taille est comprise entre [2 Mo et 8 Mo](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Ajouter des schémas de taille inférieure ou égale à 2 Mo

1. Dans le volet **Ajouter un schéma**, entrez un nom pour votre schéma. L’option **Fichier de petite taille** doit rester cochée. À côté de la zone **Schéma**, choisissez l’icône de dossier. Recherchez puis sélectionnez le schéma à charger.

1. Quand vous avez terminé, sélectionnez **OK**.

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

   * Si votre conteneur d’objets blob ne dispose pas d’un accès public, sélectionnez **Annuler**, puis suivez les étapes fournies plus loin sur cette page : [Charger vers des conteneurs avec un accès public](#public-access)

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

### <a name="standard"></a>[Standard](#tab/standard)

#### <a name="azure-portal"></a>Portail Azure

1. Dans le menu de la ressource votre application logique, sous **Paramètres**, sélectionnez **Schémas**.

1. Dans la barre d’outils du volet **Schémas**, sélectionnez **Ajouter**.

1. Dans le volet **Ajouter un schéma**, saisissez un nom unique pour votre schéma.

1. À côté de la zone **Schéma**, choisissez l’icône de dossier. Sélectionnez le schéma à télécharger.

1. Quand vous avez terminé, sélectionnez **OK**.

   Lorsque le chargement de votre schéma est terminé, celui-ci s’affiche dans la liste **Schémas**. Sur la page **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre schéma téléchargé.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. Dans la structure du projet de votre application logique, ouvrez le dossier **Artefacts**, puis le dossier **Schémas**.

1. Dans le dossier **Schémas**, ajoutez votre schéma.

---

<a name="edit-schema"></a>

## <a name="edit-a-schema"></a>Modifier un schéma

Pour mettre à jour un schéma existant, vous devez charger un nouveau fichier de schéma qui comporte les modifications souhaitées. Cependant, vous pouvez d’abord télécharger le schéma existant pour le modifier.

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma. Pour d’abord télécharger et modifier le schéma, dans la barre d’outils du volet **Schémas**, sélectionnez **Télécharger**, puis enregistrez le schéma.

1. Lorsque vous êtes prêt à charger le schéma mis à jour, dans le volet **Schémas**, sélectionnez le schéma que vous souhaitez mettre à jour. Dans la barre d’outils du volet **Schémas**, sélectionnez **Mettre à jour**.

1. Recherchez puis sélectionnez le schéma mis à jour que vous souhaitez charger.

1. Quand vous avez terminé, sélectionnez **OK**.

   Lorsque le chargement du schéma mis à jour est terminé, celui-ci s’affiche dans la liste **Schémas**.

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource de votre application logique, si elle n’est pas encore ouverte.

1. Dans le menu de la ressource votre application logique, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma. Pour d’abord télécharger et modifier le schéma, dans la barre d’outils du volet **Schémas**, sélectionnez **Télécharger**, puis enregistrez le schéma.

1. Dans la barre d’outils du volet **Schémas**, sélectionnez **Ajouter**.

1. Dans le volet **Ajouter un schéma**, saisissez un nom unique pour votre schéma.

1. À côté de la zone **Schéma**, choisissez l’icône de dossier. Sélectionnez le schéma à télécharger.

1. Quand vous avez terminé, sélectionnez **OK**.

   Lorsque le chargement du schéma mis à jour est terminé, celui-ci s’affiche dans la liste **Schémas**.

---

<a name="delete-schema"></a>

## <a name="delete-a-schema"></a>Supprimer un schéma

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma, puis sélectionnez **Supprimer**.

1. Pour confirmer que vous souhaitez supprimer le schéma, sélectionnez **Oui**.

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource de votre application logique, si elle n’est pas encore ouverte.

1. Dans le menu de la ressource votre application logique, sous **Paramètres**, sélectionnez **Schémas**.

1. Une fois que le volet **Schémas** est ouvert, sélectionnez votre schéma, puis sélectionnez **Supprimer**.

1. Pour confirmer que vous souhaitez supprimer le schéma, sélectionnez **Oui**.

---

## <a name="next-steps"></a>Étapes suivantes

* [Valider du code XML pour des flux de travail dans les Applications logiques Azure](logic-apps-enterprise-integration-xml-validation.md)
* [Transformer du code XML pour des flux de travail dans les Applications logiques Azure](logic-apps-enterprise-integration-transform.md)