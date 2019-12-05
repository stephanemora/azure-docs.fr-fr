---
title: Créer une offre Azure Apps dans la Place de marché commerciale
description: Découvrez comment créer une offre Azure Apps pour référencer ou vendre votre application sur la Place de marché Azure, sur AppSource ou via le programme Fournisseur de solutions cloud (CSP), en utilisant le portail de la Place de marché commerciale dans l’Espace partenaires Microsoft.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: d7e05f12c04136c8394dbcb27b7a950fc5ce85d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281043"
---
# <a name="create-an-azure-application-offer"></a>Créer une offre d’application Azure

Les étapes à suivre pour publier une offre d’application Azure sur la Place de marché commerciale sont décrites ci-après.

## <a name="azure-application-offer-type"></a>Type d’offre d’application Azure

Cette rubrique explique les concepts de base des offres d’applications Azure.  Il est important de vous familiariser avec ces concepts avant de démarrer le processus de publication d’une nouvelle offre d’application Azure sur la Place de marché.

### <a name="publishing-overview"></a>Présentation de la publication

La vidéo sur la [création de modèles de solution et d’applications managées pour la Place de marché Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603) est une introduction aux types d’offres d’applications Azure, et répond à ces questions :

* Quels sont les types d’offres disponibles ?
* Quelles sont les ressources techniques requises ?
* Comment créer un modèle Azure Resource Manager ?
* Comment développer et tester l’interface utilisateur d’une application ?
* Comment publier l’offre d’application ?
* Quel est le processus de revue d’une application ?

### <a name="types-of-azure-application-plans"></a>Types de plans d’applications Azure

Il existe deux types de plans d’applications Azure : les applications managées et les modèles de solution.

* Le **modèle de solution** constitue l’un des principaux moyens de publier une solution sur la Place de marché. Ce type de plan est adapté si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, au-delà d’une seule machine virtuelle.  Avec un modèle de solution, vous pouvez automatiser la fourniture de plusieurs ressources, notamment les machines virtuelles, les ressources réseau et les ressources de stockage nécessaires pour fournir des solutions IaaS complexes.  Pour plus d’informations sur la création de modèles de solution, consultez la documentation [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

* Une **application managée** est similaire aux modèles de solution, à une importante différence près. Dans une application managée, les ressources sont déployées vers un groupe de ressources géré par l’éditeur de l’application. Le groupe de ressources est présent dans l’abonnement du consommateur, mais une identité du locataire de l’éditeur a accès au groupe de ressources. En tant qu’éditeur, vous spécifiez le coût de l’assistance en continu de la solution. Utilisez des applications managées pour créer et fournir facilement des applications clés en main et entièrement managées à vos clients.  Pour plus d’informations sur les avantages et les différents types d’applications managées, consultez [Vue d’ensemble des applications managées Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Toutes les applications Azure incluent au moins deux fichiers dans le dossier racine d’une archive `.zip` :

* Un fichier de modèle Resource Manager nommé [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Il s’agit du modèle qui définit les ressources à déployer dans l’abonnement Azure du client.  Pour obtenir des exemples de modèles Resource Manager, consultez la [Galerie de modèles de démarrage rapide Microsoft Azure](https://azure.microsoft.com/resources/templates/) ou le dépôt [GitHub : Modèles de démarrage rapide Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) correspondant.

* Une définition d’interface utilisateur pour l’expérience de création d’applications Azure nommée [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  Dans l’interface utilisateur, vous spécifiez les éléments qui permettent aux consommateurs de fournir des valeurs de paramètre.

Toutes les nouvelles offres d’applications Azure doivent inclure un [GUID d’attribution de l’utilisation de client partenaire Azure](??).

### <a name="before-you-begin"></a>Avant de commencer

Examinez la documentation suivante relative aux applications Azure, qui fournit des démarrages rapides, des tutoriels et des exemples.

* [Comprendre les modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Guides de démarrage rapide :

    * [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/)
    * [Modèles de démarrage rapide Azure sur GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Publier une définition d’application](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Déployer une application de catalogue de services](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Tutoriels :

    * [Créer les fichiers de définition](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publier l’application de la Place de marché](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Exemples :

    * [Interface de ligne de commande Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Solutions d’applications managées](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Notions de base des connaissances techniques

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre.

Votre équipe d’ingénierie doit connaître les technologies Microsoft suivantes :

* Avoir une connaissance générale des [services Azure](https://azure.microsoft.com/services/).
* Savoir comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/).
* Avoir une expérience de travail avec les [Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), le [Stockage Azure](https://azure.microsoft.com/services/?filter=storage#storage) et la [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Avoir une expérience de travail avec [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Avoir une expérience de travail avec [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Outils suggérés

Choisissez un ou plusieurs des environnements de scripts suivants pour faciliter la gestion de votre application Azure :

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)

Nous vous recommandons d’ajouter les outils suivants à votre environnement de développement :

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) avec les extensions suivantes :
    * Extension : [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Extension : [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Extension : [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vous pouvez examiner les outils disponibles dans la page [Outils de développement Azure](https://azure.microsoft.com/tools/) et,  si vous utilisez Visual Studio, ceux du [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Créer une offre d’application Azure

Pour créer une offre d’application Azure, commencez par [créer un compte Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), puis ouvrez le [tableau de bord de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) et sélectionnez l’onglet **Présentation**.

>[!Note]
>Si vous modifiez une offre publiée dans l’Espace partenaires, les modifications seront répercutées dans le système et les vitrines des Stores uniquement après la republication de votre offre.  Vous devez donc toujours soumettre l’offre pour publication après y avoir apporté des modifications.

### <a name="create-a-new-offer"></a>Créer une offre

Sélectionnez le bouton **+ Nouvelle offre**, puis sélectionnez l'élément de menu **Azure Application**. La boîte de dialogue **Nouvelle offre** s’affiche.

### <a name="offer-id-and-alias"></a>ID et alias de l’offre

* **ID de l’offre** : Identificateur unique de chaque offre dans votre compte. Cet ID sera visible par les clients dans l’adresse URL de l’offre de la Place de marché et des modèles Resource Manager (si applicable). <br> <br> L’ID de votre offre doit être en caractères alphanumériques minuscules (traits d’union et traits de soulignement compris, mais sans espaces blancs). Il est limité à 50 caractères et n’est plus modifiable une fois que vous avez sélectionné Créer. <br> <br> Par exemple, si vous entrez `test-offer-1` ici, l’URL de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`. 

* **Alias de l’offre** : Le nom utilisé pour faire référence à l’offre dans l’espace partenaires. Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations qui seront présentées aux clients. Cette valeur ne peut pas être modifiée après que vous ayez sélectionné **Créer**.

Quand vous avez entré l’**ID de l’offre** et l’**alias de l’offre**, sélectionnez **Créer**. Vous pouvez ensuite travailler sur tous les autres aspects de votre offre.

## <a name="offer-setup"></a>Configuration de l’offre

La page **Configuration de l’offre** nécessite les informations suivantes. N’oubliez pas de sélectionner **Enregistrer** après avoir renseigné ces champs.

### <a name="test-drive"></a>Test drive

Une version d’évaluation vous permet de présenter votre offre à vos clients potentiels en leur donnant la possibilité de l’« essayer avant de l’acheter », ce qui se traduit par une hausse du taux de conversion et par la génération de prospects hautement qualifiés. [Découvrez-en plus sur les versions d’évaluation.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Pour activer une version d'évaluation, activez la case à cocher **Activer une version d'évaluation**. Vous devez ensuite configurer un environnement de démonstration dans la [Configuration technique de la version d'évaluation](#types-of-azure-application-plans) pour permettre aux clients d’essayer votre offre pendant une période de temps fixe. 

>[!Note]
>Du fait que toutes les applications Azure sont implémentées à l’aide d’un modèle Azure Resource Manager, le seul type de version d’évaluation pouvant être configuré pour une application Azure est une [version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Ressources supplémentaires pour version d’évaluation

- [Meilleures pratiques techniques pour version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Meilleures pratiques marketing pour version d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Document d’une page pour présenter une version d’évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Connecter la gestion des prospects

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Pour plus d’informations, consultez [Vue d’ensemble de la gestion des prospects](./commercial-marketplace-get-customer-leads.md).

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

## <a name="properties"></a>properties

La page **Propriétés** est l’emplacement où vous définissez les catégories et secteurs utilisés pour grouper votre offre selon les places de marché, la version de votre application et les contrats légaux associés. Sélectionnez **Enregistrer** après avoir renseigné cette page.

### <a name="category"></a>Category

Sélectionnez un minimum d’une catégorie et un maximum de trois catégories qui permettent de placer votre offre dans les zones de recherche appropriées sur la Place de marché. Veillez à expliquer dans la description comment votre offre prend en charge ces catégories dans la description de l’offre. 

### <a name="standard-marketplace-terms-and-conditions"></a>Conditions générales standard de la Place de marché

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un modèle de contrat Standard afin de faciliter les transactions sur la place de marché.

Plutôt que d’élaborer des conditions générales personnalisées, vous pouvez choisir de proposer votre logiciel dans le cadre du contrat Standard, que les clients ne doivent accepter qu’une seule fois.

Le contrat Standard est disponible ici : https://go.microsoft.com/fwlink/?linkid=2041178

Pour utiliser le contrat standard, cochez la case **Utiliser le contrat standard ?** .

#### <a name="terms-of-use"></a>Conditions d’utilisation

Si vous ne cochez pas la case **Utiliser le contrat standard ?** , vous devrez fournir vos propres conditions d’utilisation dans le champ **Conditions d’utilisation**. Entrez jusqu’à 10 000 caractères de texte ou, si vos conditions d’utilisation requièrent une description plus longue, indiquez l’URL où se trouvent les conditions supplémentaires du contrat de licence. Les clients doivent accepter ces conditions avant de pouvoir essayer de votre application.

## <a name="offer-listing"></a>Annonce de l’offre

La page de référencement de l’offre affiche les langues dans lesquelles votre offre sera répertoriée. Actuellement, **Anglais (États-Unis)** est la seule option disponible.

Vous devez définir les informations de la Place de marché (nom de l’offre, description, images, etc.) pour chaque langue/marché. Sélectionnez le nom de la langue/du marché pour fournir ces informations.

> [!NOTE]
> Il n’est pas nécessaire que le contenu du référencement de l’offre (par exemple, la description, les documents, les captures d’écran, les conditions d’utilisation, etc.) soit en anglais, tant que sa description commence par l’expression : « This application is available only in [langue autre que l’anglais]. » Il est également possible de fournir une *URL de lien utile* pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de la liste des offres.

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affichera auprès des clients comme titre de votre référencement d’offre. Ce champ est prérempli avec le texte que vous avez entré pour **Alias d’offre** lorsque vous avez créé l’offre, mais vous pouvez modifier cette valeur. Ce nom peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright). Le nom ne peut pas comporter plus de 50 caractères et ne peut pas contenir d’emoji.

### <a name="summary"></a>Résumé

Renseignez une courte description de votre offre (100 caractères maximum) à utiliser dans les résultats de recherche dans une place de marché.

### <a name="long-summary"></a>Résumé long

Fournissez une description plus longue de votre offre (jusqu’à 256 caractères). Cette description pourra être utilisée dans les résultats de recherche sur la Place de marché.

### <a name="description"></a>Description

Fournissez une description plus longue de votre offre (jusqu’à 3 000 caractères). Cette description s’affichera pour les clients dans la vue d’ensemble de la place de marché. Incluez la proposition de valeur de votre offre, les principaux avantages, les associations de catégorie et/ou de secteur d’activité, les opportunités d’achat dans l’application et toutes les informations requises. 

Quelques conseils pour rédiger votre description :  

- Décrivez clairement la proposition de valeur de votre offre dès les premières phrases de votre description. Incluez les éléments suivants dans votre proposition de valeur :
  - Description du produit
  - Type d’utilisateur qui tire parti du produit
  - Les clients ont des besoins ou des problèmes auxquels le produit répond
- Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats de recherche.  
- Ne comptez pas sur les fonctionnalités de votre produit pour convaincre. Concentrez-vous plutôt sur ce que votre application peut apporter aux clients.  
- Autant que possible, utilisez le vocabulaire propre au secteur visé, ainsi que des termes décrivant les avantages proposés. 
- Envisagez d’utiliser des balises HTML pour mettre en forme votre description et la rendre plus attrayante.

### <a name="search-keywords"></a>Mots clés de recherche

Vous pouvez éventuellement entrer jusqu’à trois mots clés de recherche pour aider les clients à trouver votre offre sur la place de marché. Pour obtenir des résultats optimaux, essayez également d’utiliser ces mots clés dans votre description.

### <a name="support-urls"></a>URL de support technique

Cette section vous permet de fournir des liens permettant aux clients d’en savoir plus sur votre offre.

#### <a name="privacy-policy-url"></a>URL de la politique de confidentialité

Entrez l’URL du lien de la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre application respecte les lois et réglementations en matière de confidentialité et à renseigner une politique de confidentialité conforme.

#### <a name="useful-links"></a>Liens utiles

Fournissez facultativement des documents en ligne complémentaires à propos de votre solution.  Ajoutez des liens utiles supplémentaires en cliquant sur **+ Ajouter un lien**.

### <a name="contacts"></a>Contacts

Dans cette section, entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact d’ingénierie** et d’un **contact du service clientèle**. Ces informations ne sont pas présentées aux clients, mais seront disponibles pour Microsoft et pourront être fournies aux partenaires CSP.

Dans la section **Contact de support**, vous devez également fournir **l’URL du support technique** où les partenaires CSP peuvent trouver l’assistance pour votre offre.

### <a name="marketplace-images"></a>Images de la Place de marché

Dans cette section, vous pouvez fournir des logos et des images qui seront utilisés pour montrer votre offre au client. Toutes les images doivent être au format .png.

#### <a name="store-logos"></a>Stocker des logos

Fournissez le logo de votre offre en trois tailles : **Petit (48 x 48)** , **Moyen (90 x 90)** et **Grand (216 x 216)** .

#### <a name="hero"></a>Bannière

L’image de héros est facultative. Si vous en fournissez une, elle doit être au format 815 x 290 pixels.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez des captures d’écran qui illustrent le fonctionnement de votre offre. Vous pouvez ajouter jusqu’à cinq captures d’écran. Toutes les captures d’écran doivent être au format 1280 x 720 pixels.

#### <a name="videos"></a>Videos

Vous pouvez éventuellement ajouter jusqu’à cinq vidéos qui illustrent votre offre. Ces vidéos doivent être hébergées sur YouTube et/ou Vimeo. Pour chaque vidéo, entrez son nom, son URL et une image miniature correspondante (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

- [Meilleures pratiques pour les annonces d’offre d’une place de marché](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>PRÉVERSION

L’onglet **Préversion** vous permet de restreindre le **public de la préversion** en vue de valider votre offre avant de la publier plus largement sur la Place de marché.

> [!IMPORTANT]
> Vous devez sélectionner **Publier** avant pour que votre offre soit publiée publiquement sur la place de marché après l’avoir vérifiée en Préversion.

Le public de votre préversion est identifié par des ID (GUID) d’abonnements Azure, associés à une description facultative de chacun.  Aucun de ces champs n’est visible par les clients.

Vous pouvez ajouter jusqu’à 10 ID d’abonnements Azure manuellement, ou jusqu’à 100 en chargeant un fichier CSV.  En ajoutant ces abonnements, vous définissez le public qui sera autorisé à accéder à la préversion de votre offre avant sa publication complète.  Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de test de changements ou de mises à jour futurs.

>[!Note]
>Le public pour la préversion n’est pas le même que le public privé. Un public de préversion est autorisé à accéder à votre offre *avant* sa publication sur les places de marché. Vous pouvez aussi choisir de créer un plan et de le rendre disponible uniquement pour un public privé (à partir de l’onglet Disponibilité du plan).  Le public de votre préversion pourra voir et valider tous les plans, y compris les plans qui seront disponibles uniquement pour un public privé une fois que votre offre sera entièrement publiée sur la Place de marché.

## <a name="plan-overview"></a>Vue d’ensemble du plan

L’onglet **Vue d’ensemble du plan** vous permet de proposer différentes options de plan dans la même offre. Ces plans (désignés sous le terme de références SKU dans le portail Cloud Partner) peuvent se différencier par le type de plan (modèle de solution ou application managée), la monétisation ou le public.  Créez au moins un plan avant de pouvoir référencer votre offre sur la Place de marché.

Une fois votre plan créé, vous verrez dans cet onglet diverses informations le concernant : son nom, son ID, son type, sa disponibilité (public ou privé), l’état actuel de sa publication et les actions autorisées.

Les **Actions** disponibles dans **Vue d’ensemble du plan** varient selon l’état actuel de votre plan et peuvent inclure :

* Si le plan est dans l’état **Brouillon** : « Supprimer le brouillon ».
* Si le plan est dans l’état **Publié** : « Arrêter la vente du plan » ou « Synchroniser le public privé ».

### <a name="create-new-plan"></a>Créer un plan

***ID du plan*** : créez un ID de plan unique pour chaque plan contenu dans cette offre. Cet ID sera visible par les clients dans l’URL du produit.  Utilisez uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. 50 caractères maximum autorisés pour cet ID de plan. Cet ID n’est plus modifiable une fois que vous avez sélectionné Créer.

***Nom du plan*** : les clients verront ce nom au moment de choisir le plan à sélectionner dans votre offre. Créez un ID d’offre unique pour chaque plan dans cette offre. Le nom du plan sert à différencier les plans logiciels pouvant faire partie de la même offre (par exemple, Nom de l’offre : Windows Server ; plans : Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Configuration du plan

L’onglet **Configuration du plan** vous permet de définir la configuration générale du type de plan, si le plan réutilisera les packages d’un autre plan et dans quels clouds le plan sera disponible.  Les choix effectués dans cet onglet déterminent les champs affichés dans les autres onglets du même plan.

#### <a name="plan-type"></a>Type de plan

Comme expliqué dans la section [Types de plans d’applications Azure](#types-of-azure-application-plans), indiquez si votre plan contiendra un modèle de solution ou une application managée.

#### <a name="this-plan-reuses-packages"></a>Ce plan réutilise les packages

Si vous avez plusieurs plans du même type et que les packages qu’ils utilisent sont identiques, vous pouvez sélectionner l’option pour que **ce plan réutilise les packages d’un autre plan**.  Le cas échéant, vous pourrez sélectionner, parmi tous les autres plans du même type contenus dans cette offre, le plan dont vous voulez réutiliser les packages. 

>[!Note]
>Si vous réutilisez les packages d’un autre plan, l’onglet Configuration technique ne s’affichera plus pour ce plan.  En effet, tous les paramètres de la configuration technique de l’autre plan, y compris les mises à jour effectuées ultérieurement, seront également appliqués à ce plan. <br> <br> Par ailleurs, notez que ces paramètres ne sont plus modifiables après la publication du plan.

#### <a name="cloud-availability"></a>Disponibilité dans le cloud

Ce plan doit être rendu disponible dans au moins un cloud. 

Sélectionnez l’option **Azure public** pour que votre solution puisse être déployée vers des clients dans toutes les régions Azure publiques intégrées à la Place de marché.  Apprenez-en davantage sur la [disponibilité géographique](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Sélectionnez l’option **Azure Government Cloud** pour que votre solution puisse être déployée dans le [cloud Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). Ce cloud de la communauté du secteur public est accessible aux clients d’administrations fédérales, étatiques, locales ou communautaires des États-Unis ainsi qu’à leurs partenaires éligibles.  En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires pour publier dans le cloud de cette communauté.  Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement).  Avant de publier dans le cloud [Azure Government](https://aka.ms/azuregovpublish), Microsoft vous recommande de tester et de valider votre solution dans l’environnement, car certains points de terminaison peuvent être différents. Pour tester votre solution dans un environnement de préproduction, demandez un compte d’essai en utilisant ce [lien](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Une fois qu’un plan est publié comme disponible dans un cloud, ce cloud ne peut plus être supprimé.

**Certifications du cloud Azure Government**

Cette option est visible uniquement si **Cloud Azure Government** est sélectionné sous **Disponibilité dans le cloud**.

Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales, comme FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS.  Pour renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens vers les détails de vos certifications.  Ces liens peuvent être des liens vers votre liste de plans directement sur le programme, ou des liens vers des descriptions de votre conformité avec ces certifications sur vos propres sites web.  Ces liens sont visibles uniquement par les clients du cloud Azure Government.

## <a name="plan-listing"></a>Liste des plans

L’onglet **Liste des plans** affiche les informations de chaque plan listé, qui peuvent être différentes d’un plan à un autre dans la même offre.

### <a name="name"></a>Nom

Il est prérempli sur la base du nom que vous avez attribué à votre plan au moment de sa création.  Ce nom est utilisé comme titre de ce « plan logiciel » affiché dans la Place de marché.  Il peut comporter jusqu’à 100 caractères.

### <a name="summary"></a>Résumé

Fournissez un bref résumé de votre plan logiciel.  Il peut comporter jusqu’à 100 caractères.

### <a name="description"></a>Description

Cette description permet d’expliquer ce qui rend ce plan logiciel unique et de pointer les différences par rapport aux autres plans logiciels dans votre offre. Elle peut comporter jusqu’à 2 000 caractères.

Sélectionnez **Enregistrer** après avoir renseigné ces champs.

## <a name="availability"></a>Disponibilité

L’onglet **Disponibilité** s’affiche uniquement pour les plans de modèle de solution.  Vous pouvez rendre le plan visible par tout le monde ou uniquement par des clients spécifiques (public privé), et spécifier si le plan doit être masqué pour l’utilisation par d’autres modèles de solution ou applications managées uniquement.

### <a name="plan-audience"></a>Public du plan

Vous pouvez configurer chaque plan de sorte qu’il soit visible pour tous ou pour un public spécifique. Vous pouvez définir l’appartenance à ce public restreint à l’aide des ID d’abonnement Azure.

**Confidentialité / Il s’agit d’un plan privé** (case à cocher facultative). Cochez cette case pour rendre votre plan privé et visible uniquement par le public restreint de votre choix. Une fois publié en tant que plan privé, vous pouvez mettre à jour le public ou choisir de rendre le plan disponible pour tous. Une fois un plan publié comme visible pour tous, il doit le rester pour tout le monde. (Vous ne pouvez plus configurer ce plan comme plan privé).

**Public restreint (ID d’abonnements Azure)** . Définissez le public qui aura accès à ce plan privé. Attribuez l’accès à l’aide des ID d’abonnements Azure, en ajoutant facultativement une description de chaque ID d’abonnement Azure affecté. Vous pouvez ajouter jusqu’à 10 ID d’abonnements manuellement, ou jusqu’à 20 000 ID d’abonnements clients en important un fichier de feuille de calcul (.csv).  Les ID d’abonnements Azure sont représentés par des GUID, où les lettres doivent être en minuscules.

>[!Note]
>Le public privé (ou public restreint) n’est pas le même que le public de préversion que vous avez défini sous l’onglet [**Préversion**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview).  Un public de préversion est autorisé à accéder à votre offre *avant* sa publication sur la place de marché. Tandis que la désignation de public privé ne s’applique qu’à un plan spécifique, le public de préversion peut voir tous les plans (privés ou non) dans le cadre de la validation.

### <a name="hide-plan"></a>Masquer le plan

Si vous comptez déployer votre modèle de solution uniquement de manière indirecte quand il est référencé par un autre modèle de solution ou une application managée, cochez cette case pour publier votre modèle de solution, tout en le masquant pour les clients qui essaient de le rechercher et d’y accéder directement.

## <a name="pricing-and-availability"></a>Tarification et disponibilité

L’onglet **Tarification et disponibilité** s’affiche uniquement pour les plans d’applications managées.  Vous pouvez configurer les places de marché où ce plan sera disponible, le tarif mensuel de la gestion de la solution et si le plan sera visible par tout le monde ou uniquement par des clients spécifiques (public privé).

### <a name="markets"></a>Marchés

Chaque plan doit être disponible sur au moins un marché. Cochez la case des emplacements dans lesquels vous souhaitez que ce plan soit disponible. Une zone de recherche et un bouton pour sélectionner les pays « reversant les taxes », pour lesquels Microsoft reverse les ventes et utilise les taxes en votre nom, sont inclus pour vous aider.

Si vous avez déjà fixé des prix pour votre plan en dollar américain (USD) et ajouté un autre emplacement de marché, le prix de ce nouveau marché sera calculé selon le taux de change en vigueur. Vérifiez toujours le prix de chaque place de marché avant toute publication. La tarification peut être vérifiée à l’aide du lien « Exporter les prix (xlsx) », après avoir enregistré vos changements.

### <a name="pricing"></a>Tarifs

Indiquez le tarif mensuel de ce plan.  Ce tarif s’ajoute aux coûts logiciels de paiement à l’utilisation et aux coûts de l’infrastructure Azure qui sont engendrés par les ressources déployées par cette solution.

Les prix fixés dans une devise locale (USD = dollar américain) sont convertis dans la devise locale de tous les marchés sélectionnés selon les taux de change en vigueur disponibles lors de la configuration. Validez ces prix avant la publication en exportant la feuille de calcul de tarification et en vérifiant le prix de chaque marché. Si vous souhaitez fixer des prix personnalisés dans un marché individuel, modifiez et importez la feuille de calcul de tarification. 

>[!Note]
>Vous devez d’abord enregistrer les modifications de vos tarifs pour autoriser l’exportation des données de tarification.

Vérifiez vos tarifs avec soin avant de publier votre plan, car après la publication, certains éléments ne sont plus modifiables.  

>[!Note]
>Une fois qu’un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement.

### <a name="plan-audience"></a>Public du plan

Vous pouvez configurer chaque plan de sorte qu’il soit visible pour tous ou pour un public spécifique. Vous pouvez définir l’appartenance à ce public restreint à l’aide des ID d’abonnement Azure.

**Confidentialité / Il s’agit d’un plan privé** (case à cocher facultative). Cochez cette case pour rendre votre plan privé et visible uniquement par le public restreint de votre choix. Une fois publié en tant que plan privé, vous pouvez mettre à jour le public ou choisir de rendre le plan disponible pour tous. Une fois un plan publié comme visible pour tous, il doit le rester pour tout le monde. (Vous ne pouvez plus configurer ce plan comme plan privé).

**Public restreint (ID d’abonnements Azure)** . Définissez le public qui aura accès à ce plan privé. Attribuez l’accès à l’aide des ID d’abonnements Azure, en ajoutant facultativement une description de chaque ID d’abonnement Azure affecté. Vous pouvez ajouter jusqu’à 10 ID d’abonnements manuellement, ou jusqu’à 20 000 ID d’abonnements clients en important un fichier de feuille de calcul (.csv).  Les ID d’abonnements Azure sont représentés par des GUID, où les lettres doivent être en minuscules.

>[!Note]
>Le public privé (ou public restreint) n’est pas le même que le public de préversion que vous avez défini sous l’onglet [**Préversion**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview).  Un public de préversion est autorisé à accéder à votre offre *avant* sa publication sur la place de marché. Tandis que la désignation de public privé ne s’applique qu’à un plan spécifique, le public de préversion peut voir tous les plans (privés ou non) dans le cadre de la validation.

## <a name="technical-configuration"></a>Configuration technique 

L’onglet **Configuration technique** vous permet de charger le package de déploiement qui permettra aux clients de déployer votre plan.

>[!Note]
>Cet onglet ne s’affiche pas si vous avez configuré ce plan pour réutiliser les packages d’un autre plan sous l’onglet **Configuration du plan**.

### <a name="package-details"></a>Détails du package

Dans le sous-onglet **Détails du package**, vous pouvez modifier la version brouillon de votre configuration technique.

***Version*** : définissez la version actuelle de la configuration technique.  Incrémentez cette version chaque fois que vous publiez une modification apportée à cette page. La version doit être au format `{integer}.{integer}.{integer}`.

***Fichier de package*** (`.zip`) : ce package regroupe tous les fichiers de modèle requis pour ce plan, et toutes les ressources supplémentaires, dans un seul fichier `.zip`.

Tous les packages de plan d’application Azure doivent inclure ces deux fichiers dans le dossier racine d’une archive `.zip` :

* Un fichier de modèle Resource Manager nommé [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Ce modèle automatise le déploiement de ressources dans l’abonnement Azure des clients.  Pour obtenir des exemples de modèles Resource Manager, consultez la [Galerie de modèles de démarrage rapide Microsoft Azure](https://azure.microsoft.com/documentation/templates/) ou le dépôt [GitHub : Modèles de démarrage rapide Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) correspondant.

* Une définition d’interface utilisateur pour l’expérience de création d’applications Azure nommée [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Toutes les nouvelles offres d’applications Azure doivent également inclure un GUID d’[attribution de l’utilisation de client partenaire Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

### <a name="previously-published-packages"></a>Packages précédemment publiés 

Le sous-onglet **Packages précédemment publiés** affiche toutes les versions publiées de votre configuration technique.

## <a name="technical-configuration-managed-application-plans-only"></a>Configuration technique (plans d’applications managées uniquement)

Les plans d’applications managées nécessitent une configuration supplémentaire sous l’onglet **Configuration technique** (en plus des champs **Version** et **Fichier de package** décrits ci-dessus). 

### <a name="enable-just-in-time-jit-access"></a>Activer l’accès juste-à-temps (JIT)

Sélectionnez cette option pour activer l’accès juste-à-temps (JIT) pour ce plan.  L'accès JIT vous permet de demander un accès élevé aux ressources d'une application managée à des fins de résolution des problèmes ou de maintenance. Vous bénéficiez toujours d'un accès en lecture seule aux ressources, mais pour une durée spécifique, votre accès est étendu.  Pour plus d’informations, consultez [Activer et demander l’accès juste-à-temps pour les applications managées Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Pour exiger que les consommateurs de votre application managée accordent à votre compte un accès permanent, laissez cette option non cochée.

>[!Note]
>N’oubliez pas de mettre à jour votre fichier `createUiDefinition.json` pour la prise en charge de cette fonctionnalité.  

### <a name="deployment-mode"></a>Mode de déploiement

Configurez le **mode de déploiement incrémentiel** ou **complet** pour ce plan : 

* En **mode complet**, un redéploiement de l’application par le client entraîne la suppression des ressources dans le groupe de ressources managées si les ressources ne sont pas définies dans le fichier `mainTemplate.json`. 
* En **mode incrémentiel**, le redéploiement de l’application laisse les ressources existantes inchangées.

Pour en savoir plus sur les modes de déploiement, consultez [Modes de déploiement Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>URL de point de terminaison de notification

Spécifiez un point de terminaison webhook HTTPS pour recevoir des notifications de toutes les opérations CRUD effectuées sur les instances d’application managée de cette version de plan.

### <a name="customize-allowed-customer-actions"></a>Personnaliser les actions des clients autorisées

Sélectionnez cette option pour spécifier les actions que les clients peuvent effectuer sur les ressources managées en plus des actions « `*/read` » autorisées par défaut. 

Listez les actions supplémentaires que vous souhaitez autoriser pour votre client, en séparant les actions par des points-virgules.  Pour plus d’informations, consultez [Comprendre les affectations de refus relatives aux ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Pour obtenir la liste des actions disponibles, consultez [Opérations du fournisseur de ressources Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Par exemple, pour permettre aux consommateurs de redémarrer des machines virtuelles, ajoutez `Microsoft.Compute/virtualMachines/restart/action` aux actions autorisées.

### <a name="global-azure--azure-government-cloud"></a>Azure international / Cloud Azure Government

Définissez qui doit avoir un accès administrateur à cette application managée dans chaque cloud pris en charge.  Les utilisateurs, les groupes ou les applications auxquels vous souhaitez accorder l’autorisation d’accès au groupe de ressources managées sont définis à l’aide d’identités Azure Active Directory (AAD).

***ID de locataire Azure Active Directory*** : ID de locataire AAD (également appelé ID d’annuaire) renfermant les identités des utilisateurs, des groupes ou des applications auxquels vous souhaitez accorder des autorisations.  Cet ID est indiqué dans les [propriétés d’Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) sur le portail Azure.

***Autorisations*** : ajoutez l’ID d’objet Azure Active Directory de l’utilisateur, du groupe ou de l’application que vous souhaitez autoriser à accéder au groupe de ressources managées. Identifiez l’utilisateur par son ID de principal, que vous trouverez dans le [panneau des utilisateurs Azure Active Directory sur le portail Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Pour chaque principal, sélectionnez l’un des rôles intégrés Azure AD dans la liste (Propriétaire ou Contributeur). Le rôle sélectionné détermine les autorisations que le principal aura sur les ressources dans l’abonnement client. Pour plus d’informations, consultez [Rôles intégrés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Pour plus d’informations sur le contrôle d’accès en fonction du rôle (RBAC), consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Vous avez la possibilité d’ajouter jusqu’à 100 autorisations par cloud. Toutefois, il est généralement plus simple de créer un groupe d’utilisateurs Active Directory et de spécifier son ID dans le champ « ID du principal ».  Avec cette méthode, vous pourrez ajouter des utilisateurs au groupe d’administration après le déploiement du plan et vous n’aurez pas besoin de mettre à jour le plan simplement pour ajouter des autorisations.

### <a name="policy-settings"></a>Paramètres de stratégie

Appliquez des [stratégies Azure](https://docs.microsoft.com/azure/governance/policy/overview) à votre application managée afin de spécifier des exigences de conformité pour la solution déployée.  Pour les définitions de stratégie et le format des valeurs de paramètre, consultez [Exemples Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).  Vous pouvez configurer un maximum de cinq stratégies, et une seule instance de chaque option de stratégie.  Certaines stratégies requièrent des paramètres supplémentaires.  La référence SKU Standard est requise pour les stratégies d’audit.  Le nom de la stratégie est limité à 50 caractères.

## <a name="co-sell"></a>Co-vente

Renseigner les informations sous l’onglet Co-vente n’est pas obligatoire pour publier votre offre. Ces informations sont toutefois nécessaires pour devenir « Co-sell Ready » et « IP Co-sell Ready ». Les informations que vous fournissez seront utilisées par les équipes commerciales de Microsoft pour en savoir plus sur votre solution lors de l'évaluation de son adéquation avec les besoins des clients. Les clients n'y auront pas directement accès.

Pour plus d’informations sur les informations à renseigner dans cet onglet, consultez [Option Co-vente de l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Test drive

L’onglet **Version d’évaluation** vous permet de configurer une démonstration (ou « version d’évaluation ») qui permettra à des clients d’essayer votre offre avant de s’engager et l’acheter. Pour en savoir, consultez l’article [Qu’est-ce qu’une version d’évaluation ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Si vous ne souhaitez plus fournir une version d’évaluation de votre offre, retournez sur la page **Configuration de l’offre** et décochez **Activer la version d’évaluation**.

### <a name="technical-configuration"></a>Configuration technique

Les applications Azure héritent le type de version d’évaluation d’Azure Resource Manager.  Pour plus d’informations, consultez [Configuration technique pour la version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive).

### <a name="deployment-subscription-details"></a>Détails de l’abonnement de déploiement

Pour déployer la version d’évaluation de votre côté, veuillez créer et fournir un abonnement Azure séparé et unique. (Non obligatoire pour les versions d’évaluation Power BI).

- **ID d’abonnement Azure** (obligatoire pour Azure Resource Manager et les applications logiques) : Entrez l’ID d’abonnement pour accorder l’accès à vos services de compte Azure pour obtenir un rapport et une tarification sur l’utilisation des ressources. Nous vous recommandons d’envisager la [création d’un abonnement Azure distinct](https://docs.microsoft.com/azure/billing/billing-create-subscription) à utiliser pour les versions d’évaluation si vous n’en avez pas déjà. Pour trouver vos ID d’abonnement Azure en vous connectant au [portail Azure](https://portal.azure.com/) et en accédant à l’onglet **Abonnements** dans le menu de gauche. Sélectionnez l’onglet pour afficher votre ID d’abonnement (par exemple, « a83645ac-1234-5ab6-6789-1h234g764ghty »).

- **ID du locataire Azure AD** (obligatoire) : Saisissez l’[ID client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) de votre Azure Active Directory. Pour obtenir cet ID, connectez-vous au [portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, sélectionnez **Propriétés, puis cherchez le numéro de l’**ID d’annuaire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Vous pouvez aussi rechercher l’ID locataire de votre organisation à l’aide l’URL de votre nom de domaine : [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nom du locataire Azure AD** (obligatoire pour Dynamics 365) : Entrez le nom de votre locataire Azure Active Directory. Pour rechercher ce nom, connectez-vous au [portail Azure](https://portal.azure.com/), et dans le coin supérieur droit, le nom de votre locataire s’affichera sous le nom de votre compte.

- **ID de l’application Azure AD** (obligatoire) : Entrez l’[ID de votre application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory. Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Inscriptions d’applications**, cherchez le numéro de l’**ID de l’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **Clé secrète client de l’application Azure AD** (obligatoire) : Entrez la [clé secrète client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) d’application Azure AD. Pour trouver cette valeur, connectez-vous au [portail Azure](https://portal.azure.com/). Dans le volet de navigation gauche, sélectionnez l’onglet **Azure Active Directory**,puis **Inscriptions d’applications**, et enfin votre application de version d’évaluation. Ensuite, sélectionnez **Certificats et clés secrètes**, **Nouvelle clé secrète client**, entrez une description, sélectionnez **Jamais** sous **Expiration** puis choisissez **Ajouter**. Veillez à copier la valeur. (Ne quittez pas la page avant d’avoir copié cette valeur, car vous ne pourrez plus obtenir la valeur ensuite.)

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

### <a name="test-drive-listings-optional"></a>Listes des versions d’évaluation (facultatives)

L’option **Liste des versions d’évaluation** disponible sous l’onglet **Version d’évaluation** affiche les langues (et les marchés) disponibles pour votre version d’évaluation. English (États-Unis) est actuellement le seul emplacement disponible. De plus, cette page affiche le statut de l’annonce spécifique à une langue et l’heure et la date à laquelle elle a été ajoutée. Vous devez définir les détails de la version d’évaluation (description, manuel de l’utilisateur, vidéos, etc.) pour chaque langue et marché.

- **Description** (obligatoire) : Décrivez votre version d’évaluation, ce qui sera présenté, les objectifs que l’utilisateur pourra essayer, les fonctionnalités à explorer, et toute autre information qui aidera l’utilisateur à déterminer ou non s’il souhaite faire l’acquisition de votre offre. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ. 

- **Accéder aux informations** (obligatoire pour les versions d’évaluation Azure Resource Manager et d’applications logiques) : Expliquez ce qu’un client doit savoir pour accéder à cette version d’évaluation et l’utiliser. Détaillez un scénario d’utilisation de votre offre ainsi que tout ce que le client doit savoir pour accéder aux fonctionnalités disponibles dans la version d’évaluation. Jusqu’à 10 000 caractères peuvent être entrés dans ce champ.

- **Manuel de l’utilisateur** (obligatoire) : Procédure approfondie de l’expérience de votre version d’évaluation. Le manuel de l’utilisateur doit couvrir exactement ce que vous souhaitez que le client accomplisse dans la version d’évaluation et sert de référence pour toutes les questions qu’ils peuvent avoir. Le fichier doit être au format PDF et être nommé (255 caractères maximum) après le chargement.

- **Vidéos : Ajouter des vidéos** (facultatif) : Vous pouvez charger des vidéos sur YouTube ou Vimeo et les référencer via un lien et une image miniature (533 x 324 pixels), pour proposer au client un guide à suivre et l’aider à mieux comprendre la version d’évaluation, dont l’utilisation des fonctionnalités de votre offre et la compréhension des scénarios qui présentent les avantages.
  - **Nom** (obligatoire)
  - **URL (YouTube ou Vimeo uniquement)** (obligatoire)
  - **Miniature (533 x 324 px)**  : L’image doit être au format .PNG.

Sélectionnez **Enregistrer** après avoir renseigné ces champs.

## <a name="publish"></a>Publish

### <a name="submit-offer-to-preview"></a>Soumettre l’offre en préversion

Une fois que vous avez rempli toutes les sections nécessaires de l’offre, sélectionnez **Publier** en haut à droite du portail. Vous serez redirigé vers la page **Vérifier et publier**. 

S’il s’agit de votre première publication de cette offre :

- Consultez l’état d’achèvement de chaque section de l’offre.
    - *Non démarrée* : cela signifie que la section n’a pas encore été modifiée et doit être complétée.
    - *Incomplète* : cela signifie que la section comporte des erreurs qui doivent être corrigées ou qui nécessitent plus d’informations. Veuillez revenir à la ou les sections et mettez-les à jour.
    - *Complète* : cela signifie que la section est terminée, que toutes les données requises ont été renseignées et qu’il n’y a aucune erreur. Toutes les sections de l’offre doivent être dans l’état Complète avant de pouvoir envoyer l’offre.
- Fournissez des instructions de test à l’équipe de certification pour veiller à ce que votre application soit testée correctement, en plus de toutes autres notes supplémentaires utiles pour comprendre l’application.
- Envoyez l’offre pour publication en sélectionnant **Envoyer**. Nous vous enverrons un e-mail pour vous faire savoir si une version d’essai de l’offre est disponible pour vérification et approbation. Retourner dans Partner Center et sélectionner **Publier** pour que votre offre soit publiée pour le public (ou au public privé, si l’offre est privée).

### <a name="errors-and-review-feedback"></a>Erreurs et commentaires de révision

L’étape **Validation manuelle** dans le processus de publication équivaut à une revue complète de votre offre et des ressources techniques associées à celle-ci (en particulier, le modèle Azure Resource Manager) ; les problèmes sont généralement présentés sous la forme de liens de demande de tirage (pull request). Pour savoir comment afficher ces demandes de tirage (pull requests) et y répondre, consultez [Gestion des commentaires de révision](./azure-apps-review-feedback.md).

Si vous rencontrez des erreurs au cours d'une ou plusieurs étapes de publication, vous devez les corriger et republier votre offre.

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](./update-existing-offer.md)
