---
title: Créer une offre d’application Azure – Place de marché commerciale de Microsoft
description: Découvrez les étapes et considérations relatives à la création d’une offre d’application Azure via le portail de la Place de marché commerciale dans l’Espace partenaires. Vous pouvez répertorier ou vendre votre offre d’application Azure sur la Place de marché Azure ou via le programme Fournisseur de solutions Cloud (CSP).
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: ace85727680ecf6d62860ac2239a8c0b68ae6e0e
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848785"
---
# <a name="create-an-azure-application-offer"></a>Créer une offre d’application Azure

Cet article explique les étapes et considérations relatives à la création d’une offre d’application Azure sur la Place de marché commerciale. Vous devez connaître ces concepts avant de créer une offre d’application Azure.

Avant de publier une nouvelle offre d’application Azure, vous devez [créer un compte sur la Place de marché commerciale dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), et vérifiez que votre compte est inscrit dans le programme de la Place de marché commerciale.

## <a name="before-you-begin"></a>Avant de commencer

Concevoir, créer et tester une offre d’application Azure nécessite de disposer de connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre. Votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes :

* Avoir une connaissance générale des [services Azure](https://azure.microsoft.com/services/).
* Savoir comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/).
* Avoir une expérience de travail avec les [Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), le [Stockage Azure](https://azure.microsoft.com/services/?filter=storage#storage) et la [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Avoir une expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Avoir une expérience de travail avec [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Documentation et ressources techniques

Lors de la préparation de votre offre d’application Azure pour la Place de marché commerciale, consultez les ressources suivantes.

* [Comprendre les modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

* Guides de démarrage rapide :

    * [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/)
    * [Modèles de démarrage rapide Azure sur GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Publier une définition d’application](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Déployer une application de catalogue de services](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Tutoriels :

    * [Créer les fichiers de définition](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publier l’application de la Place de marché](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Exemples :

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Solutions d’applications managées](https://docs.microsoft.com/azure/managed-applications/sample-projects)

La vidéo sur la [Création de modèles de solution et d’applications managées pour la Place de marché Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603) présente de façon exhaustive le type d’offre d’application Azure :

* Quels sont les types d’offres disponibles ?
* Quelles sont les ressources techniques requises ?
* Comment créer un modèle Azure Resource Manager ?
* Comment développer et tester l’interface utilisateur d’une application ?
* Comment publier l’offre d’application ?
* Quel est le processus de revue d’une application ?

### <a name="suggested-tools"></a>Outils suggérés

Choisissez un ou plusieurs des environnements de scripts suivants pour faciliter la gestion de votre application Azure :

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Nous vous recommandons d’ajouter les outils suivants à votre environnement de développement :

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) avec les extensions suivantes :
    * Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vous pouvez examiner les outils disponibles dans la page [Outils de développement Azure](https://azure.microsoft.com/tools/) et, si vous utilisez Visual Studio, ceux du [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Types de plans d’applications Azure

Il existe deux types de plans d’application Azure : les modèles de solution et les applications managées.

* Le **modèle de solution** constitue l’un des principaux moyens de publier une solution sur la Place de marché. Utilisez ce type de plan si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, au-delà d’une seule machine virtuelle. Avec un modèle de solution, vous pouvez automatiser la fourniture de plusieurs ressources, notamment les machines virtuelles, les ressources réseau et les ressources de stockage nécessaires pour fournir des solutions IaaS complexes.  Pour plus d’informations sur la création de modèles de solution, consultez la documentation [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

* Une **application managée** est similaire aux modèles de solution, à une importante différence près. Dans une application managée, les ressources sont déployées vers un groupe de ressources géré par l’éditeur de l’application. Le groupe de ressources est présent dans l’abonnement du consommateur, mais une identité du locataire de l’éditeur a accès au groupe de ressources. En tant qu’éditeur, vous spécifiez le coût de l’assistance en continu de la solution. Utilisez des applications managées pour créer et fournir facilement des applications clés en main et entièrement managées à vos clients.  Pour plus d’informations sur les avantages et les différents types d’applications managées, consultez [Vue d’ensemble des applications managées Azure](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="technical-requirements"></a>Exigences techniques

Toutes les applications Azure incluent au moins deux fichiers dans le dossier racine d’une archive `.zip` :

* Un fichier de modèle Resource Manager nommé [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Ce modèle qui définit les ressources à déployer dans l’abonnement Azure du client.  Pour obtenir des exemples de modèles Resource Manager, consultez la [Galerie de modèles de démarrage rapide Microsoft Azure](https://azure.microsoft.com/resources/templates/) ou le dépôt [GitHub : Modèles de démarrage rapide Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) correspondant.

* Une définition d’interface utilisateur pour l’expérience de création d’applications Azure nommée [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Dans l’interface utilisateur, vous spécifiez les éléments qui permettent aux consommateurs de fournir des valeurs de paramètre.

Toutes les nouvelles offres d’applications Azure doivent inclure un [GUID d’attribution de l’utilisation de client partenaire Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution). 

Pour en savoir plus sur la publication des conditions requises pour chaque plan d’application, consultez [Conditions requises pour la publication d’une offre de modèle de solution](../marketplace-solution-templates.md) et [Exigences relatives à la publication d’une offre d’application managée](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Créer une offre

>[!NOTE]
>Une fois l’offre publiée, les modifications que vous y apportez dans l’Espace partenaires n’apparaissent pas dans les vitrines tant que vous ne republiez pas l’offre. Veillez à toujours republier votre offre après y avoir apporté des modifications.

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).

1. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.

1. Dans la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Application Azure**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-azure-app.png)

1. Dans la page **Nouvelle offre**, entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

     * Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché et des modèles Resource Manager, le cas échéant.
     * Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
     * L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

1. Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

     * Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.
     * L’alias d’offre ne peut pas être modifié une fois que vous avez sélectionné **Créer**.

1. Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-setup"></a>Configuration de l’offre

La page **Configuration de l’offre** vous permet de configurer une version d’évaluation et une gestion des prospects pour votre offre.

### <a name="test-drive"></a>Test drive

Une version d’évaluation constitue un excellent moyen de présenter votre offre à vos clients potentiels en leur offrant la possibilité de l’essayer avant d’acheter, ce qui se traduit par une hausse du taux de conversion et par la génération de prospects de qualité. [Découvrez-en plus sur les versions d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Pour activer une version d’évaluation pour une durée déterminée, cochez la case **Activer une version d’évaluation**. Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher. Configurez l’environnement de la version d’évaluation dans la section [Configuration technique de la version d’évaluation](#test-drive-technical-configuration) plus loin dans cette rubrique.

Pour plus d’informations, consultez [Proposer une version d’évaluation de votre offre sur la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive). Vous pouvez également découvrir les [meilleures pratiques en matière de version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) et télécharger le [PDF de présentation des versions d’évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (assurez-vous que le bloqueur de fenêtres publicitaires est désactivé).

>[!Note]
>Du fait que toutes les applications Azure sont implémentées à l’aide d’un modèle Azure Resource Manager, le seul type de version d’évaluation disponible pour une application Azure est une [version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

### <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Pour plus d’informations, consultez [Vue d’ensemble de la gestion des prospects](./commercial-marketplace-get-customer-leads.md).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="properties"></a>Propriétés

La page **Propriétés** vous permet de définir les catégories et secteurs utilisés pour grouper votre offre selon les places de marché, la version de votre application et les contrats légaux associés.

Sélectionnez de une à trois catégories pour placer votre offre dans les zones de recherche appropriées de la Place de marché. Veillez à décrire en quoi votre offre prend en charge ces catégories dans la description de l’offre.

### <a name="legal"></a>Informations juridiques

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Annonce de l’offre

Cette page vous permet de gérer la copie et les images de votre offre sur la Place de marché commerciale. 

### <a name="marketplace-details"></a>Détails de la Place de marché

> [!NOTE]
> Il n’est pas nécessaire que le contenu du référencement de l’offre (par exemple, la description, les documents, les captures d’écran et les conditions d’utilisation) soit en anglais, tant que sa description commence par l’expression : « This application is available only in [langue autre que l’anglais]. » Il est également possible de fournir une *URL de lien utile* pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de la liste des offres.

#### <a name="name"></a>Nom

Le nom que vous entrez ici s’affichera auprès des clients comme titre de votre référencement d’offre. Ce champ est prérempli avec le texte que vous avez entré pour **Alias d’offre** lorsque vous avez créé l’offre, mais vous pouvez modifier cette valeur. Ce nom peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright). Le nom ne peut pas comporter plus de 50 caractères et ne peut pas contenir d’emoji.

#### <a name="search-results-summary"></a>Résumé des résultats de recherche

Fournissez une brève description de votre offre, jusqu’à 100 caractères. Cette description apparaîtra peut-être dans les résultats de recherche.

#### <a name="long-summary"></a>Résumé long

Fournissez une description plus longue de votre offre, jusqu’à 256 caractères. Cette description apparaîtra peut-être dans les résultats de recherche.

#### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Mots clés de recherche

Vous pouvez éventuellement entrer jusqu’à trois mots clés de recherche pour aider les clients à trouver votre offre sur la place de marché. Pour obtenir des résultats optimaux, utilisez également ces mots clés dans votre description.

#### <a name="privacy-policy-link"></a>Lien de la politique de confidentialité

Entrez l’URL du lien de la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre application respecte les lois et réglementations en matière de confidentialité et à renseigner une politique de confidentialité conforme.

### <a name="useful-links"></a>Liens utiles

Ajoutez des liens vers des documents en ligne supplémentaires facultatifs concernant votre solution en sélectionnant **+ Ajouter un lien**.

### <a name="contact-information"></a>Informations de contact

Entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **Contact de support**, d’un **Contact ingénierie** et d’un **Contact du programme du fournisseur de solutions Cloud**. Ces informations ne sont pas présentées aux clients, mais destinées à Microsoft qui peut les communiquer aux fournisseurs de solutions Cloud partenaires. Certains contacts peuvent nécessiter des informations supplémentaires.

### <a name="marketplace-media"></a>Médias de la Place de marché

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format PNG. Votre demande sera rejetée si les images sont floues.

>[!Note]
>Si vous rencontrez un problème lors du chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com que l’Espace partenaires utilise.

#### <a name="store-logos"></a>Stocker des logos

Fournissez des fichiers PNG du logo de votre offre dans les quatre tailles en pixels suivantes :

- **Petit** (48 x 48)
- **Moyen** (90 x 90)
- **Grand** (216 x 216)
- **Large** (255 x 115)

Les trois logos sont requis et utilisés à différents endroits de la description.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez jusqu’à cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chaque capture d’écran doit avoir une taille de 1280 x 720 pixels et être au format PNG. Chaque capture d’écran doit inclure une légende.

#### <a name="videos"></a>Videos

Ajoutez jusqu’à cinq vidéos qui illustrent votre offre. Elles doivent être hébergées sur un service vidéo externe. Entrez le nom, l’adresse web et une image miniature PNG de 1280 x 720 pixels de chaque vidéo.

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

- [Meilleures pratiques pour les annonces d’offre d’une place de marché](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="preview-audience"></a>Public de la version préliminaire

Sous l’onglet Préversion, choisissez de restreindre le **public de préversion** en vue de valider votre offre avant de la publier plus largement en ligne sur la Place de marché.

> [!IMPORTANT]
> Après avoir vérifié votre offre en Préversion, sélectionnez **Publier** pour publier votre offre sur la place de marché commerciale.

Le public de votre préversion est identifié par des ID (GUID) d’abonnements Azure, avec une description facultative de chacun. Aucun de ces champs ne peut être vu par les clients. Vous trouverez votre ID d’abonnement Azure dans la page **Abonnements** du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, soit séparément (jusqu’à 10), soit en chargeant un fichier CSV (jusqu’à 100). En ajoutant ces ID d’abonnement, vous définissez qui peut afficher un aperçu de votre offre avant qu’elle ne soit publiée en ligne. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de test de changements ou de mises à jour futurs de l’offre.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de préversion peut accéder à votre offre _avant_ sa publication sur les places de marché. Il peut voir et valider tous les plans, y compris les plans qui seront disponibles uniquement pour un public privé une fois que votre offre sera entièrement publiée sur la Place de marché. Un public privé (défini dans l’onglet **Tarification et disponibilité** du plan) dispose d’un accès exclusif à un plan particulier.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="technical-configuration"></a>Configuration technique

Suivez les instructions de cette section uniquement si votre offre comprend une application managée qui doit émettre des événements de mesure à l’aide de l’API du service de mesure de la Place de marché. Entrez l’**ID de locataire Azure Active Directory** et l’**ID d’application Azure Active Directory** que votre service doit utiliser lors de l’émission d’événements de mesure.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="technical-configuration-offer-level"></a>Configuration technique (niveau de l’offre)

>[!Note]
>Les détails techniques au niveau de l’offre sont facultatifs.  Vous ne devez configurer ces détails que si vous publiez une application managée avec facturation basée sur des mesures et disposez d’un service qui s’authentifie avec un jeton de sécurité Azure AD. Pour plus d’informations, consultez les [stratégies d’authentification](./marketplace-metering-service-authentication.md) des différentes options d’authentification.

La configuration technique définit les détails (ID de locataire et ID d’application) utilisés pour identifier votre service qui émettra les événements de mesure pour une application managée à l’aide des [API du service de mesure de la Place de marché](./marketplace-metering-service-apis.md).  Entrez l’identité que votre service utilisera lors de l’émission d’événements de mesure.

* **ID du locataire Azure AD** (obligatoire) : Dans le portail Azure, vous devez [créer une application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) afin que nous puissions confirmer que la connexion entre nos deux services se fait bien dans le cadre d’une communication authentifiée. Pour trouver l’[ID de locataire](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), accédez à votre Azure Active Directory et sélectionnez **Propriétés**, puis cherchez le numéro de l’**ID de répertoire** affiché (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).
* **ID de l’application Azure AD** (obligatoire) : Vous avez également besoin de l’[ID de votre application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) et d’une clé d’authentification. Pour obtenir ces valeurs, accédez à votre Azure Active Directory et sélectionnez **Inscriptions des applications**, puis cherchez le numéro d’**ID d’application** affiché (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Pour trouver la clé d’authentification, accédez à **Paramètres** et sélectionnez **Clés**. Vous devez fournir une description et une durée, et vous obtiendrez ensuite une valeur numérique.

>[!Note]
>L’ID d’application Azure est associé à votre ID d’éditeur et ne peut être réutilisé que dans ce compte d’éditeur.

>[!Note]
>Cette configuration est requise si vous souhaitez utiliser l’option [Événement d’utilisation par lot](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#batch-usage-event).  Si vous souhaitez soumettre un [événement d’utilisation](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#usage-event), vous pouvez également utiliser le [service de métadonnées d’instance](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pour obtenir le [Jeton du porteur du JSON Web Token (JWT)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app).

## <a name="plan-overview"></a>Vue d’ensemble du plan

Cet onglet permet de proposer différentes options de plan dans la même offre. Ces plans (désignés sous le terme de références SKU dans le portail Cloud Partner) peuvent se différencier par le type de plan (modèle de solution ou application managée), la monétisation ou le public.  Créez au moins un plan avant de pouvoir référencer votre offre sur la Place de marché.

Une fois votre plan créé, vous verrez dans cet onglet diverses informations le concernant : son nom, son ID, son type, sa disponibilité (public ou privé), l’état actuel de sa publication et les actions autorisées.

Les **Actions** disponibles dans **Vue d’ensemble du plan** varient selon l’état actuel de votre plan et peuvent inclure :

* Si le plan est dans l’état **Brouillon** : « Supprimer le brouillon ».
* Si le plan est dans l’état **Publié** : « Arrêter la vente du plan » ou « Synchroniser le public privé ».

### <a name="create-new-plan"></a>Créer un plan

***ID du plan*** : créez un ID de plan unique pour chaque plan contenu dans cette offre. Cet ID sera visible par les clients dans l’URL du produit.  Utilisez uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. 50 caractères maximum autorisés pour cet ID de plan. Cet ID n’est plus modifiable une fois que vous avez sélectionné Créer.

***Nom du plan*** : les clients verront ce nom au moment de choisir le plan à sélectionner dans votre offre. Créez un ID d’offre unique pour chaque plan dans cette offre. Le nom du plan sert à différencier les abonnements logiciels pouvant faire partie de la même offre (par exemple, nom de l’offre : Windows Server ; plans : Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuration du plan

Cet onglet permet de définir la configuration générale du type de plan, si le plan réutilise des packages d’un autre plan et les clouds dans lesquels le plan sera disponible. Les choix effectués dans cet onglet déterminent les champs affichés dans les autres onglets du même plan.

#### <a name="plan-type"></a>Type de plan
Sélectionnez le type de plan de votre offre. Un plan **modèle de solution** est entièrement managé par le client. Un plan d’**application managée** permet aux éditeurs de manager l’application pour le compte du client. Pour plus d’informations, consultez [Types de plans d’application Azure](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Réutiliser la configuration technique

Si vous avez plusieurs plans du même type et que les packages qu’ils utilisent sont identiques, vous pouvez sélectionner l’option pour que **ce plan réutilise les packages d’un autre plan**.  Le cas échéant, vous pourrez sélectionner, parmi tous les autres plans du même type contenus dans cette offre, le plan dont vous voulez réutiliser les packages. 

>[!Note]
>Si vous réutilisez les packages d’un autre plan, l’onglet Configuration technique ne s’affichera plus pour ce plan. En effet, tous les paramètres de la configuration technique de l’autre plan, y compris les mises à jour effectuées ultérieurement, seront également appliqués à ce plan.<br><br>Ce paramètre n’est plus modifiable une fois le plan publié.

#### <a name="azure-regions-cloud"></a>Régions Azure (cloud)

Votre plan doit être rendu disponible dans au moins une région Azure.

Sélectionnez l’option **Azure Global** pour mettre votre plan à la disposition des clients de toutes les régions Azure qui disposent d’une intégration de la Place de marché commerciale. Pour plus d’informations, consultez [Prise en charge de la disponibilité géographique et des devises](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Sélectionnez l’option **Azure Government** pour proposer votre plan dans la région [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). Cette région fournit un accès contrôlé aux clients d’entités gouvernementales fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’aux partenaires éligibles pour les servir. En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires. Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement).

Avant de le publier dans [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), testez et validez votre plan dans l’environnement, car certains points de terminaison peuvent être différents. Pour configurer et tester votre plan, demandez un compte d’essai depuis la page [Microsoft Azure Government Trial](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Une fois votre plan publié et disponible dans une région Azure spécifique, vous ne pouvez pas supprimer cette région.

#### <a name="azure-government-certifications"></a>Certifications Azure Government

Cette option n’est visible que si vous avez sélectionné **Azure Government**.

Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales. Par exemple, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS. Afin de renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens décrivant ces certifications. Ces liens peuvent être des liens vers votre liste de plans directement sur le programme, ou des liens vers des descriptions de votre conformité avec ces certifications sur vos propres sites web. Ces liens sont visibles uniquement par les clients Azure Government.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="plan-listing"></a>Liste des plans

C’est ici que vous configurez les détails de la description du plan. Cet onglet affiche des informations spécifiques à chaque plan au sein de la même offre.

#### <a name="plan-name"></a>Nom du plan

Ce champ est pré-rempli avec le nom que vous avez donné à votre plan quand vous l’avez créé. Ce nom apparaît dans la place de marché en tant que titre de ce plan et est limité à 100 caractères.

#### <a name="plan-summary"></a>Résumé du plan

Fournissez un bref résumé de votre plan (pas de l’offre). Ce résumé est limité à 100 caractères.

#### <a name="plan-description"></a>Description du plan

Décrivez ce qui rend ce plan logiciel unique, ainsi que les différences entre les plans de votre offre. Ne décrivez pas l’offre, mais simplement le plan. La description du plan peut contenir jusqu’à 2 000 caractères.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="availability-solution-template-plans-only"></a>Disponibilité (plans de modèle de solution uniquement)

Vous pouvez rendre le plan visible par tout le monde ou uniquement par des clients spécifiques (public privé), et spécifier si le plan doit être masqué pour l’utilisation par d’autres modèles de solution ou applications managées uniquement.

#### <a name="plan-visibility"></a>Visibilité du plan

Vous pouvez configurer chaque plan de sorte qu’il soit visible de tous ou d’un public spécifique. Vous pouvez définir l’appartenance à ce public restreint à l’aide des ID d’abonnement Azure.

Activez l’option **Il s’agit d’un plan privé** pour que votre plan ne soit visible que du public restreint de votre choix. Une fois publié en tant que plan privé, vous pouvez mettre à jour le public ou choisir de rendre le plan disponible pour tous. Une fois un plan publié comme visible de tous, il doit le rester. Il n’est pas possible de le reconfigurer en tant que plan privé.

Si vous rendez le plan privé, entrez un **ID d’abonnement Azure** et sa description. Définissez un public qui aura accès à ce plan privé. Attribuez l’accès à l’aide des ID d’abonnements Azure, en ajoutant facultativement une description de chaque ID d’abonnement Azure affecté. Vous pouvez ajouter jusqu’à 10 ID d’abonnement client individuellement ou 20 000 en important un fichier CSV. Les ID d’abonnements Azure sont représentés par des GUID, où les lettres doivent être en minuscules.

>[!Note]
>Un public privé ou restreint est différent du public de préversion que vous avez défini sous l’onglet **Préversion**. Un public de préversion peut accéder à votre offre _avant_ sa publication sur la place de marché. Tandis que la sélection d’un public privé ne s’applique qu’à un plan spécifique, le public de préversion peut voir tous les plans (privés ou non) dans le cadre de la validation.

#### <a name="hide-plan"></a>Masquer le plan

Si vous comptez déployer votre modèle de solution uniquement de manière indirecte quand il est référencé par un autre modèle de solution ou une application managée, cochez cette case pour publier votre modèle de solution, tout en le masquant pour les clients qui essaient de le rechercher et d’y accéder directement.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Tarification et disponibilité (plans d’applications managées uniquement)

Utilisez cette option pour configurer les **marchés** dans lesquels ce plan sera disponible, la **Tarification** par mois de la gestion de la solution et la **Visibilité du plan** si seuls des clients spécifiques doivent le voir (public privé).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

#### <a name="markets"></a>Marchés

Chaque plan doit être disponible sur au moins un marché. Cochez la case des emplacements dans lesquels vous souhaitez que ce plan soit disponible. Une zone de recherche et un bouton pour sélectionner les pays/régions « reversant les taxes », pour lesquels Microsoft reverse les ventes et utilise les taxes en votre nom, sont inclus pour vous aider.

Si vous avez déjà fixé des prix pour votre plan en dollar américain (USD) et ajouté un autre emplacement de marché, le prix de ce nouveau marché sera calculé selon le taux de change en vigueur. Vérifiez toujours le prix de chaque place de marché avant toute publication. La tarification peut être vérifiée à l’aide du lien « Exporter les prix (xlsx) », après avoir enregistré vos changements.

#### <a name="pricing"></a>Tarifs

Indiquez le tarif mensuel de ce plan.  Ce tarif s’ajoute aux coûts logiciels de paiement à l’utilisation et aux coûts de l’infrastructure Azure qui sont engendrés par les ressources déployées par cette solution.

En plus du tarif mensuel, vous pouvez fixer des prix pour l’utilisation d’unités non standard assortie d’une [facturation basée sur des mesures](./azure-app-metered-billing.md).  Si vous le souhaitez, vous pouvez définir le tarif mensuel sur zéro et facturer exclusivement via une facturation basée sur des mesures. 

Les prix fixés en USD (USD = dollar américain) sont convertis dans la devise locale de tous les marchés sélectionnés selon les taux de change en vigueur disponibles lors de l’enregistrement. Validez ces prix avant la publication en exportant la feuille de calcul de tarification et en vérifiant le prix de chaque marché. Si vous souhaitez fixer des prix personnalisés dans un marché individuel, modifiez et importez la feuille de calcul de tarification. 

>[!Note]
>Vous devez d’abord enregistrer les modifications de vos tarifs pour autoriser l’exportation des données de tarification.

Vérifiez vos tarifs avec soin avant de publier votre plan, car après la publication, certains éléments ne sont plus modifiables.  

>[!Note]
>Une fois qu’un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement.

#### <a name="plan-visibility"></a>Visibilité du plan

Vous pouvez configurer chaque plan de sorte qu’il soit visible de tous ou d’un public spécifique. Vous pouvez définir l’appartenance à ce public restreint à l’aide des ID d’abonnement Azure.

Activez l’option **Il s’agit d’un plan privé** pour que votre plan ne soit visible que du public restreint de votre choix. Une fois publié en tant que plan privé, vous pouvez mettre à jour le public ou choisir de rendre le plan disponible pour tous. Une fois un plan publié comme visible de tous, il doit le rester. Il n’est pas possible de le reconfigurer en tant que plan privé.

Si vous rendez le plan privé, entrez un **ID d’abonnement Azure** et sa description. Définissez un public qui aura accès à ce plan privé. Attribuez l’accès à l’aide des ID d’abonnements Azure, en ajoutant facultativement une description de chaque ID d’abonnement Azure affecté. Vous pouvez ajouter jusqu’à 10 ID d’abonnement client individuellement ou 20 000 en important un fichier CSV. Les ID d’abonnements Azure sont représentés par des GUID, où les lettres doivent être en minuscules.

>[!Note]
>Un public privé ou restreint est différent du public de préversion que vous avez défini sous l’onglet **Préversion**. Un public de préversion peut accéder à votre offre _avant_ sa publication sur la place de marché. Tandis que la sélection d’un public privé ne s’applique qu’à un plan spécifique, le public de préversion peut voir tous les plans (privés ou non) dans le cadre de la validation.

### <a name="technical-configuration"></a>Configuration technique 

Cet onglet vous permet de charger le package de déploiement qui permettra aux clients de déployer votre plan.

>[!Note]
>Cet onglet ne s’affiche pas si vous avez configuré ce plan pour réutiliser les packages d’un autre plan sous l’onglet **Configuration du plan**.

#### <a name="package-details"></a>Détails du package

Cet onglet vous permet de modifier la version provisoire de votre configuration technique.

**Version** : attribuez la version actuelle de la configuration technique.  Incrémentez cette version chaque fois que vous publiez une modification apportée à cette page. La version doit être au format `{integer}.{integer}.{integer}`.

**Fichier de package** (.zip) : ce package regroupe tous les fichiers de modèle requis pour ce plan, ainsi toutes les ressources supplémentaires, dans un seul fichier `.zip`.

Tous les packages de plan d’application Azure doivent inclure ces deux fichiers dans le dossier racine d’une archive `.zip` :

* Un fichier de modèle Resource Manager nommé [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Ce modèle automatise le déploiement de ressources dans l’abonnement Azure des clients.  Pour obtenir des exemples de modèles Resource Manager, consultez la [Galerie de modèles de démarrage rapide Microsoft Azure](https://azure.microsoft.com/documentation/templates/) ou le dépôt [GitHub : Modèles de démarrage rapide Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) correspondant.
* Une définition d’interface utilisateur pour l’expérience de création d’applications Azure nommée [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Tailles maximales de fichiers prises en charge :

* Jusqu’à 1 Go dans la taille totale des archives `.zip` compressées
* Jusqu’à 1 Go pour tous les fichiers non compressés individuels au sein de l’archive `.zip`  

Toutes les nouvelles offres d’applications Azure doivent également inclure un GUID d’[attribution de l’utilisation de client partenaire Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

>[!Note]
>Si vous rencontrez un problème de chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

Les plans d’applications managées nécessitent l’entrée d’informations supplémentaires sous cet onglet.

#### <a name="previously-published-packages"></a>Packages précédemment publiés

Le sous-onglet **Packages précédemment publiés** affiche toutes les versions publiées de votre configuration technique.

#### <a name="enable-just-in-time-jit-access"></a>Activer l’accès juste-à-temps (JIT)

Sélectionnez cette option pour activer l’accès juste-à-temps (JIT) pour ce plan.  L'accès JIT vous permet de demander un accès élevé aux ressources d'une application managée à des fins de résolution des problèmes ou de maintenance. Vous bénéficiez toujours d'un accès en lecture seule aux ressources, mais pour une durée spécifique, votre accès est étendu.  Pour plus d’informations, consultez [Activer et demander l’accès juste-à-temps pour les applications managées Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Pour exiger que les consommateurs de votre application managée accordent à votre compte un accès permanent, laissez cette option non cochée.

>[!Note]
>N’oubliez pas de mettre à jour votre fichier `createUiDefinition.json` pour la prise en charge de cette fonctionnalité.  

#### <a name="deployment-mode"></a>Mode de déploiement

Configurez le **mode de déploiement incrémentiel** ou **complet** pour ce plan : 

* En **mode complet**, un redéploiement de l’application par le client entraîne la suppression des ressources dans le groupe de ressources managées si les ressources ne sont pas définies dans le fichier `mainTemplate.json`. 
* En **mode incrémentiel**, le redéploiement de l’application laisse les ressources existantes inchangées.

Pour en savoir plus sur les modes de déploiement, consultez [Modes de déploiement Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

#### <a name="notification-endpoint-url"></a>URL de point de terminaison de notification

Spécifiez un point de terminaison webhook HTTPS pour recevoir des notifications de toutes les opérations CRUD effectuées sur les instances d’application managée de cette version de plan.

#### <a name="customize-allowed-customer-actions"></a>Personnaliser les actions des clients autorisées

Sélectionnez cette option pour spécifier les actions que les clients peuvent effectuer sur les ressources managées en plus des actions « `*/read` » autorisées par défaut. 

Listez les actions supplémentaires que vous souhaitez autoriser pour votre client, en séparant les actions par des points-virgules.  Pour plus d’informations, consultez [Comprendre les affectations de refus relatives aux ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Pour obtenir la liste des actions disponibles, consultez [Opérations du fournisseur de ressources Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Par exemple, pour permettre aux consommateurs de redémarrer des machines virtuelles, ajoutez `Microsoft.Compute/virtualMachines/restart/action` aux actions autorisées.

#### <a name="global-azure--azure-government-cloud"></a>Azure international / Cloud Azure Government

Définissez qui doit avoir un accès administrateur à cette application managée dans chaque cloud pris en charge. Les utilisateurs, les groupes ou les applications auxquels vous souhaitez accorder l’autorisation d’accès au groupe de ressources managées sont définis à l’aide d’identités Azure Active Directory (AAD).

**ID de locataire Azure Active Directory** : ID du locataire AAD (également appelé ID d’annuaire) contenant les identités des utilisateurs, des groupes ou des applications auxquels vous souhaitez accorder des autorisations. Cet ID est indiqué dans les [propriétés d’Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) sur le portail Azure.

**Autorisations** : ajoutez l’ID d’objet Azure Active Directory de l’utilisateur, du groupe ou de l’application que vous souhaitez autoriser à accéder au groupe de ressources managées. Identifiez l’utilisateur par son ID de principal, que vous trouverez dans le [panneau des utilisateurs Azure Active Directory sur le portail Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Pour chaque principal, sélectionnez l’un des rôles intégrés Azure AD dans la liste (Propriétaire ou Contributeur). Le rôle sélectionné détermine les autorisations que le principal aura sur les ressources dans l’abonnement client. Pour plus d’informations, consultez [Rôles intégrés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Pour plus d’informations sur le contrôle d’accès en fonction du rôle (RBAC), consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Vous avez la possibilité d'ajouter jusqu'à 100 autorisations par cloud. Toutefois, il est généralement plus simple de créer un groupe d'utilisateurs Active Directory et de spécifier son ID dans le champ « ID du principal ». Cela vous permet d’ajouter des utilisateurs au groupe d’administration après le déploiement du plan, et a pour effet de réduire le besoin de mise à jour du plan à l’ajout de quelques autorisations.

#### <a name="policy-settings"></a>Paramètres de stratégie

Appliquez des [stratégies Azure](https://docs.microsoft.com/azure/governance/policy/overview) à votre application managée afin de spécifier des exigences de conformité pour la solution déployée. Pour les définitions de stratégie et le format des valeurs de paramètre, consultez [Exemples Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index). Vous pouvez configurer un maximum de cinq stratégies, et une seule instance de chaque option de stratégie. Certaines stratégies requièrent des paramètres supplémentaires. La référence SKU Standard est requise pour les stratégies d’audit. Le nom de la stratégie est limité à 50 caractères.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="co-sell-with-microsoft"></a>Co-vendre avec Microsoft

L’entrée d’informations sous l’onglet Co-vente est totalement facultative pour la publication de votre offre. Ces informations sont toutefois nécessaires pour devenir « Co-sell Ready » et « IP Co-sell Ready ». Les informations que vous fournissez seront utilisées par les équipes commerciales de Microsoft pour en savoir plus sur votre solution lors de l'évaluation de son adéquation avec les besoins des clients. Les clients n'y auront pas directement accès.

Pour plus d’informations sur cet onglet, consultez [Option Co-vente de l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="resell-through-csps"></a>Revendre via des fournisseurs de solutions cloud

Développez la portée de votre offre en la mettant à la disposition des partenaires du programme [fournisseur de solutions cloud](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Cela permet aux revendeurs de vendre votre offre à leurs clients et de créer des solutions groupées.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="test-drive"></a>Test drive

Configurez une démonstration (version d'évaluation) permettant aux clients d’évaluer votre offre avant de l’acheter. Pour créer un environnement de démonstration permettant aux clients d’évaluer votre offre pendant une période fixe, consultez [Évaluer votre offre sur la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Pour activer une version d’évaluation, cochez la case **Activer une version d’évaluation** sous l’onglet [Configuration de l’offre](#test-drive). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

### <a name="test-drive-technical-configuration"></a>Configuration technique de la version d’évaluation

- **ID de l’application Azure AD** (obligatoire) : Entrez l’[ID de votre application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory. Pour trouver cet ID, connectez-vous au [portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, choisissez **Inscriptions d’applications**, puis cherchez le numéro d’**ID d’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Détails de l’abonnement de déploiement

Pour autoriser le déploiement de la version d’évaluation pour votre compte, créez et fournissez un abonnement Azure unique distinct (non requis pour les versions d’évaluation de Power BI).

* **ID d’abonnement Azure** (obligatoire pour Azure Resource Manager et Logic Apps) : entrez l’ID d’abonnement pour accorder l’accès aux services de votre compte Azure pour les rapports d’utilisation des ressources et la facturation. Nous vous recommandons d’envisager la [création d’un abonnement Azure distinct](https://docs.microsoft.com/azure/billing/billing-create-subscription) à utiliser pour les versions d’évaluation si vous n’en avez pas déjà. Pour trouver vos ID d’abonnement Azure en vous connectant au [portail Azure](https://portal.azure.com/) et en accédant à l’onglet **Abonnements** dans le menu de gauche. Sélectionnez l’onglet pour afficher votre ID d’abonnement (par exemple, « a83645ac-1234-5ab6-6789-1h234g764ghty »).
* **ID de locataire Azure AD** (obligatoire) : entrez votre [ID de locataire](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory (AD). Pour obtenir cet ID, connectez-vous au [portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, choisissez **Propriétés**, puis cherchez le numéro d’**ID d’annuaire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Vous pouvez aussi rechercher l’ID locataire de votre organisation à l’aide l’URL de votre nom de domaine : [https://www.whatismytenantid.com](https://www.whatismytenantid.com).
* **Nom de locataire Azure AD** (obligatoire pour Dynamic 365) : entrez votre nom Azure Active Directory (AD). Pour rechercher ce nom, connectez-vous au [portail Azure](https://portal.azure.com/), et dans le coin supérieur droit, le nom de votre locataire s’affichera sous le nom de votre compte.
* **ID d’application Azure AD** (obligatoire) : entrez votre [ID d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory (AD). Pour trouver cet ID, connectez-vous au [portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, choisissez **Inscriptions d’applications**, puis cherchez le numéro d’**ID d’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).
* **Clé secrète client de l’application Azure Active Directory** (obligatoire) : entrez la [clé secrète client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) de votre application Azure AD. Pour trouver cette valeur, connectez-vous au [portail Azure](https://portal.azure.com/). Dans le volet de navigation de gauche, sélectionnez l’onglet **Azure Active Directory**, choisissez **Inscriptions d’applications**, puis sélectionnez votre application de version d’évaluation. Ensuite, sélectionnez **Certificats et clés secrètes**, **Nouvelle clé secrète client**, entrez une description, sélectionnez **Jamais** sous **Expiration** puis choisissez **Ajouter**. Veillez à copier la valeur avant de quitter cette page.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="marketplace-listing-optional"></a>Description sur la Place de marché (facultatif)

Décrivez la version d’évaluation.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Description** (obligatoire) : décrivez votre version d’évaluation, ce qu’elle montre, des objectifs d’expérimentation pour l’utilisateur, des fonctionnalités à explorer et fournissez toute autre information pouvant aider l’utilisateur dans sa décision d’achat. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ. 
* **Informations d’accès** (obligatoire pour les versions d’évaluation Azure Resource Manager et logiques) – Expliquez ce qu’un client doit savoir pour accéder et utiliser la version d’évaluation en question. Détaillez un scénario d’utilisation de votre offre ainsi que tout ce que le client doit savoir pour accéder aux fonctionnalités disponibles dans la version d’évaluation. Jusqu’à 10 000 caractères peuvent être entrés dans ce champ.
* **Manuel de l’utilisateur** (obligatoire) : présentation approfondie de l’expérience de votre version d’évaluation. Le manuel de l’utilisateur doit couvrir exactement ce que vous souhaitez que le client accomplisse dans la version d’évaluation et sert de référence pour toutes les questions qu’ils peuvent avoir. Le fichier doit être au format PDF et être nommé (255 caractères maximum) après le chargement.
* **Vidéos : Ajouter des vidéos** (facultatif) : vous pouvez charger des vidéos sur YouTube ou Vimeo et les référencer ici via un lien et une image miniature (533 x 324 pixels) pour présenter au client des informations qui l’aideront à mieux comprendre la version d’évaluation, notamment la façon d’exploiter les fonctionnalités de l’offre, ainsi que des scénarios qui mettent en avant ses avantages.
  * **Nom** (obligatoire)
  * **Adresse** (obligatoire, YouTube ou Vimeo uniquement)
  * **Miniature** (ce fichier image doit être au format PNG et d’une taille de 533 x 324 pixels)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="publish"></a>Publish

Après avoir complété toutes les sections nécessaires de l’offre, sélectionnez **Vérifier et publier** en haut à droite du portail.

Révisez le statut d’achèvement de chaque section de l’offre.
    - *Non démarrée* : cela signifie que la section n’a pas encore été modifiée et doit être complétée.
    - *Incomplète* : cela signifie que la section comporte des erreurs qui doivent être corrigées ou qui nécessitent plus d’informations. Revenir à la ou les sections et les mettre à jour.
    - *Complète* : cela signifie que la section est terminée, que toutes les données requises ont été renseignées et qu’il n’y a aucune erreur. Toutes les sections de l’offre doivent être dans l’état Complète avant de pouvoir envoyer l’offre.

Si vous publiez cette offre pour la première fois, vous pouvez fournir des instructions de test à l’équipe de certification pour veiller à ce que votre application soit testée correctement, en plus de toutes autres notes supplémentaires utiles pour comprendre l’application.

Sélectionnez **Soumettre** pour envoyer votre offre en vue de sa publication. Nous vous enverrons un e-mail pour vous faire savoir si une version d’essai de l’offre est disponible pour vérification et approbation.

Retourner dans Partner Center et sélectionner **Publier** pour que votre offre soit publiée pour le public (ou au public privé, si l’offre est privée).

### <a name="errors-and-review-feedback"></a>Erreurs et commentaires de révision

L’étape **Validation manuelle** dans le processus de publication équivaut à une revue complète de votre offre et des ressources techniques associées à celle-ci (en particulier, le modèle Azure Resource Manager) ; les problèmes sont généralement présentés sous la forme de liens de demande de tirage (pull request). Pour savoir comment afficher ces demandes de tirage (pull requests) et y répondre, consultez [Gestion des commentaires de révision](./azure-apps-review-feedback.md).

Si vous rencontrez des erreurs durant une ou plusieurs des étapes de publication, corrigez-les avant de republier votre offre.

## <a name="next-steps"></a>Étapes suivantes

* [Mettre à jour une offre existante dans la Place de marché commerciale](./update-existing-offer.md)
