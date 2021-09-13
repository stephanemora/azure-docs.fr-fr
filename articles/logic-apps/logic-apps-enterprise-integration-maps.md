---
title: Ajouter des mappages XSLT pour les transformations XML
description: Créer et ajouter des mappages XSLT pour transformer des données XML dans Azure Logic Apps avec Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: c597a7d44b620ee33acec028812aa2d1651283ff
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100432"
---
# <a name="add-xslt-maps-for-xml-transformation-in-azure-logic-apps"></a>Ajouter des mappages XSLT pour la transformation XML dans Azure Logic Apps

Pour convertir des données XML dans un autre format pour des scénarios d’intégration d’entreprise dans Azure Logic Apps, votre application logique peut utiliser des mappages, notamment des mappages XSLT (Extensible Stylesheet Language Transformations). Un mappage est un document XML qui décrit comment convertir les données d’un document XML dans un autre format.

Par exemple, imaginons que vous receviez régulièrement des commandes ou des factures B2B de la part d’un client qui utilise le format de date YearMonthDay (AAAAMMJJ). Votre organisation, quant à elle, utilise le format de date MonthDayYear (MMJJAAAA). Vous pouvez définir et utiliser un mappage qui transforme le format de AAAAMMJJ au format MMJJAAAA avant d’enregistrer les détails de la commande ou de la facture dans votre base de données d’activité client.

Avec un mappage, vous pouvez définir une transformation simple, par exemple la copie d’un nom et de l’adresse d’un document vers un autre. Vous pouvez aussi créer des transformations plus complexes à l'aide des opérations de mappage prêtes à l'emploi.

> [!NOTE]
> Azure Logic Apps alloue de la mémoire finie pour le traitement des transformations XML. Si vous créez des applications logiques basées sur le type de ressource **Application logique (consommation)** , et que vos transformations de mappage ou de charge utile ont une consommation de mémoire élevée, ces transformations peuvent échouer, provoquant des erreurs de mémoire insuffisante. Pour éviter ce scénario, envisagez les options suivantes :
>
> * Modifiez vos cartes ou charges utiles pour réduire la consommation de mémoire.
>
> * Créez vos applications logiques à l’aide du type de ressource **Application logique (Standard)** à la place.
>
>   Ces flux de travail s’exécutent dans les instances Azure Logic Apps monolocataires, qui offrent des options dédiées et flexibles pour les ressources de calcul et de mémoire. 
>   Toutefois, le type de ressource d’application logique standard ne prend pas actuellement en charge le référencement d’assemblys externes à partir de mappages. En outre, seule XSLT (Extensible Stylesheet Language Transformation) 1.0 est actuellement pris en charge.

Si vous débutez avec les applications logiques, consultez la documentation suivante :

* [En quoi consiste Azure Logic Apps - Type de ressource et environnements d’hôte ?](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [Créer un flux de travail d’intégration avec les applications Azure Logic Apps monolocataires (standard)](create-single-tenant-workflows-azure-portal.md)

* [Créer des flux de travail d’application logique à un seul locataire](create-single-tenant-workflows-azure-portal.md)

* [Modèles de contrôle de l’utilisation, de facturation et de tarifs pour Azure Logic Apps](logic-apps-pricing.md)

## <a name="limits"></a>Limites

* Pour les ressources d’applications logiques **standards**, il n’existe aucune limite concernant les tailles de fichier de mappage.

* Pour les ressources d’applications logiques de **consommation**, il existe des limites pour les comptes et artefacts d’intégration comme les mappages. Pour plus d’informations, consultez [Informations relatives aux limites et à la configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Si vous utilisez le type de ressource **Application logique (Standard)** , vous n’avez pas besoin de compte d’intégration. Au lieu de cela, vous pouvez ajouter des mappages directement à votre ressource d’application logique dans le Portail Azure ou Visual Studio Code. Seul XSLT 1.0 est actuellement pris en charge. Vous pouvez ensuite utiliser ces mappages sur plusieurs workflows au sein de la *même ressource d’application logique*.

* Si vous utilisez le type de ressource **Application logique (Consommation)** , vous devez posséder une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) où vous pouvez stocker vos mappages et autres artefacts à utiliser dans les solutions d’intégration d’entreprise et interentreprises (B2B). Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existante dans le même emplacement ou la même région Azure que la ressource d’application logique dans laquelle vous envisagez d’utiliser l’action **Transformer au format XML**.

  * Est [liée](logic-apps-enterprise-integration-create-integration-account.md#link-account) à votre ressource d’application logique lorsque vous souhaitez utiliser des mappages.

    Pour l’instant, vous n’avez pas besoin d’une ressource d’application logique pour créer et ajouter des mappages à utiliser dans les workflows d’application logique de consommation. Toutefois, lorsque vous êtes prêt à utiliser ces mappages dans vos workflows, votre ressource d’application logique requiert un compte d’intégration lié pour les stocker.

* Tandis qu’une **Application logique (Consommation)** prend en charge le référencement d’assemblys externes à partir de mappages, l’**Application logique (Standard)** ne prend, pour l’instant, pas en charge cette fonctionnalité. Le fait de référencer un assembly permet d’effectuer des appels directs de mappages XSLT vers du code .NET personnalisé.

  * Vous avez besoin d’un assembly 64 bits. Le service de transformation exécute un processus 64 bits, de sorte que les assemblages 32 bits ne sont pas pris en charge. Si vous disposez du code source pour un assemblage 32 bits, recompilez le code en un assemblage 64 bits. Si vous n’avez pas le code source, mais que vous avez obtenu le fichier binaire d’un fournisseur tiers, obtenez la version 64 bits de ce fournisseur. Par exemple, certains vendeurs fournissent des assemblages dans des packages qui ont à la fois des versions 32 bits et 64 bits. Si vous avez l’option, utilisez la version 64 bits à la place.

  * Vous devez charger *l’assembly et le mappage* dans une ordre spécifique dans votre compte d’intégration. Vous devez [*d’abord charger votre assembly*](#add-assembly), puis charger le mappage qui le référence.

  * Si la taille de votre assembly est de [2 Mo ou moins](#smaller-map), vous pouvez ajouter votre assembly et votre mappage à votre compte d’intégration *directement* depuis le Portail Azure. Toutefois, si votre assembly ou mappage a une taille supérieure à 2 Mo mais qui ne dépasse pas la [limite de taille pour les assemblys ou les mappages](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), vous pouvez utiliser un conteneur d’objets blob Azure dans lequel vous pouvez charger votre assembly et l’emplacement de ce conteneur. De cette façon, vous pourrez fournir cet emplacement lorsque vous ajouterez l’assembly à votre compte d’intégration. Pour cette tâche, vous avez besoin des éléments suivants :

    | Élément | Description |
    |------|-------------|
    | [Compte Azure Storage](../storage/common/storage-account-overview.md) | Dans ce compte, créez un conteneur d’objets blob Azure pour votre assembly. Découvrez comment [créer un compte de stockage](../storage/common/storage-account-create.md). |
    | Conteneur d’objets blob | Dans ce conteneur, vous pouvez charger votre assembly. Vous aurez également besoin de l’emplacement de l’URI de redirection de ce conteneur lorsque vous ajouterez l’assembly à votre compte d’intégration. Découvrez comment [créer un conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Cet outil facilite la gestion des comptes de stockage et des conteneurs d’objets blob. Pour utiliser l’Explorateur Stockage, [téléchargez et installez l’Explorateur Stockage Azure](https://www.storageexplorer.com/). Ensuite, connectez l’Explorateur Stockage à votre compte de stockage en suivant les étapes décrites dans [Prise en main de l’Explorateur Stockage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Pour en savoir plus, consultez [Démarrage rapide : Créer un objet blob dans le stockage d’objets avec l’Explorateur Stockage Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Dans le Portail Azure, sélectionnez votre compte de stockage. Dans le menu de votre compte de stockage, sélectionnez **Explorateur Stockage**. |
    |||

  * Pour ajouter des mappages de plus grande taille pour les ressources d’application logique Consommation, vous pouvez également utiliser l’[API REST Azure Logic Apps - Mappages](/rest/api/logic/maps/createorupdate). Toutefois, pour les ressources d’application logique Standard, l’API REST Azure Logic Apps n’est pas disponible actuellement.

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies-consumption-resource-only"></a>Ajouter des assemblys référencés (ressource de consommation uniquement)

1. Dans le [portail Azure](https://portal.azure.com) connectez-vous avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter votre assembly.

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

     ![Accès public](media/logic-apps-enterprise-integration-maps/azure-blob-container-public-access.png)

   * Si votre conteneur d’objets blob ne dispose pas d’un accès public, sélectionnez **Annuler**, puis suivez les étapes fournies plus loin sur cette page : [Charger vers des conteneurs avec un accès public](#no-public-access-assemblies)

     ![Aucun accès public](media/logic-apps-enterprise-integration-maps/azure-blob-container-no-public-access.png)

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

<a name="create-maps"></a>

## <a name="create-maps"></a>Créer des mappages

Pour créer un document XSLT (Extensible Stylesheet Language Transformation) que vous pouvez utiliser comme mappage, vous pouvez utiliser Visual Studio 2015 ou 2019 afin de créer un projet d’intégration à l’aide du [kit de développement logiciel (SDK) Intégration Entreprise](https://aka.ms/vsmapsandschemas). Dans ce projet, vous pouvez créer un fichier de mappage d’intégration qui vous permet de représenter graphiquement les éléments de deux fichiers de schéma XML. Une fois le projet créé, vous obtenez un document XSLT. Pour connaître le nombre limite de mappages que peut comprendre un compte d’intégration, consultez [Limites et configuration pour Azure Logic Apps](logic-apps-limits-and-config.md#artifact-number-limits).

Votre mappage doit également comporter les attributs suivants et une section `CDATA` contenant l’appel au code de l’assembly :

* `name` est le nom d’assembly personnalisé.

* `namespace` est l’espace de noms de l’assembly qui comprend le code personnalisé.

L’exemple suivant montre un mappage qui fait référence à un assembly nommé `XslUtilitiesLib` et appelle la méthode `circumference` à partir de celui-ci.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
<msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
</msxsl:script>
<xsl:template match="data">
<circles>
    <xsl:for-each select="circle">
        <circle>
            <xsl:copy-of select="node()"/>
                <circumference>
                    <xsl:value-of select="user:circumference(radius)"/>
                </circumference>
        </circle>
    </xsl:for-each>
</circles>
</xsl:template>
</xsl:stylesheet>
```

## <a name="tools-and-capabilities-for-maps"></a>Outils et fonctionnalités pour les mappages

* Lorsque vous créez un mappage à l’aide de Visual Studio et du [kit de développement logiciel (SDK) Intégration Entreprise](https://aka.ms/vsmapsandschemas), vous travaillez avec une représentation graphique du mappage, qui affiche toutes les relations et les liens que vous créez.

* Vous pouvez effectuer une copie de données directe entre les schémas. Le [kit de développement logiciel (SDK) Intégration Entreprise](https://aka.ms/vsmapsandschemas) pour Visual Studio comprend un mappeur qui rend cette tâche aussi simple de dessiner une ligne qui connecte les éléments du schéma XML source à leurs équivalents dans le schéma XML cible.

* Des opérations ou des fonctions de mappages multiples sont disponibles, y compris des fonctions de chaîne, des fonctions de date et d’heure, etc.  

* Pour ajouter un exemple de message XML, vous pouvez utiliser la fonctionnalité de test de mappage. En un seul clic, vous pouvez tester le mappage que vous avez créé et évaluer la sortie générée.

## <a name="add-maps"></a>Ajouter des mappages

### <a name="consumption-resource"></a>[Ressource de consommation](#tab/consumption-1)

Une fois que vous avez chargé les assemblys que votre mappage référence, vous pouvez charger votre mappage.

1. Dans le Portail Azure, si votre compte d’intégration n’est pas déjà ouvert, entrez `integration accounts` dans la zone de recherche principale d’Azure, puis sélectionnez **Comptes d’intégration**.

1. Sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter votre mappage.

1. Dans le menu de votre compte d’intégration, sélectionnez **Vue d’ensemble**. Sous **Paramètres**, sélectionnez **Mappages**.

1. Dans la barre d’outils du volet **Mappages**, sélectionnez **Ajouter**.

1. Continuez à ajouter soit un mappage [jusqu’à 2 MB](#smaller-map) ou [supérieur à 2 MB](#larger-map).

<a name="smaller-map"></a>

#### <a name="add-maps-up-to-2-mb"></a>Ajouter des mappages de taille inférieure ou égale à 2 Mo

1. Sous **Ajouter un mappage**, entrez un nom unique pour votre mappage.

1. Sous **Type de mappage**, sélectionnez un type, par exemple : **Liquid**, **XSLT**, **XSLT 2.0** ou **XSLT 3.0**.

1. À côté de la zone **Mappage**, choisissez l’icône de dossier. Sélectionnez le mappage à télécharger.

   Si vous avez laissé la propriété **Nom** vide, le nom de fichier du mappage s’affiche automatiquement dans cette propriété après avoir sélectionné le fichier du mappage.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Une fois que le chargement de votre fichier de mappage est terminé, le mappage s’affiche dans la liste **Mappages**. Sur la page **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre mappage téléchargé.

<a name="larger-map"></a>

#### <a name="add-maps-more-than-2-mb"></a>Ajouter des mappages de taille inférieure ou égale à 2 Mo

Pour ajouter des mappages plus volumineux, utilisez l’[API REST Azure Logic Apps - Maps](/rest/api/logic/maps/createorupdate).

### <a name="standard-resource"></a>[Ressource standard](#tab/standard-1)

#### <a name="azure-portal"></a>Portail Azure

1. Dans le menu de votre ressource d’application logique, sous **Paramètres**, sélectionnez **Mappages**.

1. Dans la barre d’outils du volet **Mappages**, sélectionnez **Ajouter**.

1. Sous **Ajouter un mappage**, entrez un nom unique pour votre mappage en incluant le nom d’extension `.xslt`.

1. À côté de la zone **Mappage**, choisissez l’icône de dossier. Sélectionnez le mappage à télécharger.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Une fois que le chargement de votre fichier de mappage est terminé, le mappage s’affiche dans la liste **Mappages**. Sur la page **Vue d’ensemble** de votre compte d’intégration, sous **Artefacts**, s’affiche également votre mappage téléchargé.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. Dans la structure du projet de votre application logique, ouvrez le dossier **Artefacts**, puis le dossier **Mappages**.

1. Dans le dossier **Mappages**, ajoutez votre mappage.

---

## <a name="edit-maps"></a>Modifier des mappages

Pour mettre à jour un mappage existant, vous devez charger un nouveau fichier de mappage qui comporte les modifications souhaitées. Cependant, vous pouvez d’abord télécharger le mappage existant pour le modifier.

### <a name="consumption-resource"></a>[Ressource de consommation](#tab/consumption-2)

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Mappages**.

1. Une fois la page **Mappages** ouverte, sélectionnez votre mappage. Pour d’abord télécharger et modifier le mappage, dans la barre d’outils du volet **Mappages**, sélectionnez **Télécharger**, puis enregistrez le mappage.

1. Lorsque vous êtes prêt à charger le mappage mis à jour, dans le volet **Mappages**, sélectionnez le mappage que vous souhaitez mettre à jour. Dans la barre d’outils du volet **Mappages**, sélectionnez **Mettre à jour**.

1. Recherchez puis sélectionnez le mappage mis à jour que vous souhaitez charger.

   Une fois que le chargement de votre fichier de mappage est terminé, le mappage mis à jour s’affiche dans la liste **Mappages**.

### <a name="standard-resource"></a>[Ressource standard](#tab/standard-2)

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique, si elle n’est pas encore ouverte.

1. Dans le menu de votre ressource d’application logique, sous **Paramètres**, sélectionnez **Mappages**.

1. Une fois la page **Mappages** ouverte, sélectionnez votre mappage. Pour d’abord télécharger et modifier le mappage, dans la barre d’outils du volet **Mappages**, sélectionnez **Télécharger**, puis enregistrez le mappage.

1. Dans la barre d’outils du volet **Mappages**, sélectionnez **Ajouter**.

1. Sous **Ajouter un mappage**, entrez un nom unique pour votre mappage en incluant le nom d’extension `.xslt`.

1. À côté de la zone **Mappage**, choisissez l’icône de dossier. Sélectionnez le mappage à télécharger.

1. Quand vous êtes prêt, sélectionnez **OK**.

   Une fois que le chargement de votre fichier de mappage est terminé, le mappage mis à jour s’affiche dans la liste **Mappages**.

---

## <a name="delete-maps"></a>Supprimer des mappages

### <a name="consumption-resource"></a>[Ressource de consommation](#tab/consumption-3)

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration, s’il n’est pas déjà ouvert.

1. Dans le menu de votre compte d’intégration, sous **Paramètres**, sélectionnez **Mappages**.

1. Une fois le volet **Mappages** ouvert, sélectionnez votre mappage, puis sélectionnez **Supprimer**.

1. Pour confirmer que vous souhaitez supprimer le mappage, choisissez **Oui**.

### <a name="standard-resource"></a>[Ressource standard](#tab/standard-3)

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique, si elle n’est pas encore ouverte.

1. Dans le menu de votre ressource d’application logique, sous **Paramètres**, sélectionnez **Mappages**.

1. Une fois le volet **Mappages** ouvert, sélectionnez votre mappage, puis sélectionnez **Supprimer**.

1. Pour confirmer que vous souhaitez supprimer le mappage, choisissez **Oui**.

---

## <a name="next-steps"></a>Étapes suivantes

* [Transformer du code XML pour des flux de travail dans Azure Logic Apps](logic-apps-enterprise-integration-transform.md)
* [Valider des données XML pour les workflows dans Azure Logic Apps](logic-apps-enterprise-integration-xml-validation.md)
