---
title: Créer une offre Dynamics 365 Business Central – Marketplace commercial de Microsoft
description: Découvrez les étapes et les considérations relatives à la création d’une offre Dynamics 365 Business Central dans le portail du marketplace commercial dans l’Espace partenaires. Vous pouvez répertorier ou vendre votre offre sur la Place de marché Azure ou via le programme Fournisseur de solutions Cloud (CSP).
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: c7618bd4408f07b70e2f9fffe23e38ba968e7210
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792392"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Créer une offre Dynamics 365 Business Central

Cet article explique comment créer une offre Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) est un système de planification des ressources d’entreprise (ERP) qui gère un vaste éventail de processus d’entreprise, notamment en lien avec les finances, les opérations, la chaîne logistique, la gestion de la relation client et le commerce électronique. Les packages Premium prennent également en charge le modèle de déploiement classique et la fabrication. Toutes les offres pour Dynamics 365 Business Central doivent passer par notre processus de certification.

Avant de commencer, et si vous ne l’avez pas déjà fait, [créez un compte Marketplace commercial dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account). Vérifiez que votre compte est inscrit dans le programme du marketplace commercial.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Marketplace commercial** > **Vue d’ensemble**.
3. Dans la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Dynamics 365 Business Central**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-dynamics-365-bc.png)

> [!NOTE]
> Après la publication d’une offre, les modifications qui lui ont été apportées dans l’Espace partenaires apparaissent uniquement dans les vitrines après la republication de l’offre. Veillez à toujours effectuer une nouvelle publication après avoir apporté des modifications.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché et des modèles Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.
- L’alias d’offre ne peut pas être modifié une fois que vous avez sélectionné **Créer**.

Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-setup"></a>Configuration de l’offre

Effectuez les étapes suivantes pour configurer votre offre.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Comment voulez-vous qu’interagissent les clients avec l’offre de liste ?

Sélectionnez l’option que vous souhaitez utiliser pour cette offre.

#### <a name="get-it-now-free"></a>Obtenez-en une maintenant (gratuit)

Répertoriez votre offre gratuitement pour les clients en fournissant une URL valide (qui commence par *http* ou *https*) où ils peuvent accéder à votre application.  Par exemple : `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Essai gratuit (annonce)

Répertoriez votre offre en tant qu’essai gratuit pour les clients en fournissant une URL valide (qui commence par *http* ou *https*) où ils peuvent accéder obtenir un essai.  Par exemple : `https://contoso.com/trial/my-app`. Les versions d’évaluation gratuites sont créées, gérées et configurées par votre service et n’ont pas d’abonnements gérés par Microsoft.

> [!NOTE]
> Les jetons que votre application recevra via votre lien d’évaluation peuvent uniquement être utilisés pour obtenir des informations utilisateur par le biais de Azure Active Directory (Azure AD) pour automatiser la création de comptes dans votre application. Les comptes Microsoft ne sont pas pris en charge pour l’authentification à l’aide de ce jeton.

#### <a name="contact-me"></a>Me contacter

Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Connecter la gestion des prospects](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Une version d’évaluation est un bon moyen de présenter votre offre à vos clients potentiels en leur donnant la possibilité de l’« essayer avant de l’acheter », ce qui se traduit par une hausse du taux de conversion et par la génération de prospects de qualité. [Découvrez-en plus sur les versions d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Pour activer une version d’évaluation pour une durée déterminée, cochez la case **Activer une version d’évaluation**. Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher. Configurez l’environnement de la version d’évaluation dans la section [Configuration technique de la version d’évaluation](#test-drive-technical-configuration) plus loin dans cette rubrique.

Pour plus d’informations, consultez [Proposer une version d’évaluation de votre offre sur le marketplace commercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Type de version d’évaluation

Choisissez parmi les options suivantes :

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**  : modèle de déploiement qui contient toutes les ressources Azure qui constituent votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure.
- **[Dynamics 365 Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**  : Microsoft héberge et gère le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système de planification des ressources d’entreprise Business Central (finances, opérations, chaîne d’approvisionnement, CRM, etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**  : Microsoft héberge et gère le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système Customer Engagement (ventes, service, service de projet, service après-vente, etc.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**  : Microsoft héberge et gère le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système de planification des ressources d’entreprise Finances et opérations (finances, opérations, fabrication, chaîne d’approvisionnement, etc.). 
- **[Application logique](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**  : modèle de déploiement englobant toutes les architectures de solution complexes. Tous les produits personnalisés doivent tous utiliser ce type de version d’évaluation.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**  : lien hypertexte vers un tableau de bord personnalisé. Les produits dotés d’un visuel Power BI interactif doivent utiliser ce type de version d’évaluation. Il vous suffit de charger votre URL Power BI incorporée.

#### <a name="additional-test-drive-resources"></a>Ressources supplémentaires pour version d’évaluation

- [Meilleures pratiques techniques pour version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Meilleures pratiques marketing pour version d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- Document PDF de [présentation des versions d’évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé.)

## <a name="connect-lead-management"></a>Connecter la gestion des prospects

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Pour plus d’informations, consultez [Vue d’ensemble de la gestion des prospects](./commercial-marketplace-get-customer-leads.md).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="properties"></a>Propriétés

Cette page vous permet de définir les catégories et les secteurs d’activité auxquels votre offre est destinée sur le marketplace, la version de votre application et les contrats juridiques accompagnant votre offre.

### <a name="category"></a>Category

Sélectionnez un minimum d’une catégorie et un maximum de trois catégories qui permettent de placer votre offre dans les zones de recherche appropriées sur la Place de marché. Veillez à expliquer dans la description comment votre offre prend en charge ces catégories dans la description de l’offre. 

### <a name="industry"></a>Secteur d’activité

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Version de l’application

Entrez le numéro de version de votre offre. Cette version est indiquée aux clients sur la page de détails de l’offre.

### <a name="terms-and-conditions"></a>Conditions générales

Fournissez vos propres conditions légales dans le champ **Conditions générales**. Vous pouvez également fournir l’URL permettant d’accéder à vos conditions générales. Les clients doivent accepter ces conditions avant de pouvoir essayer votre offre.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="offer-listing"></a>Annonce de l’offre

C’est là que vous définissez les détails de votre offre, tels que le nom, la description et les images.

> [!NOTE]
> Vous ne pouvez fournir les détails de l’offre que dans une seule langue. Les détails de l’offre n’ont pas à être en anglais si la description de l’offre commence par l’expression « This application is available only in [langue non anglaise] ». Il est également possible de fournir l’*URL du lien Aide* pour offrir du contenu dans une langue autre que celle utilisée dans la description de l’offre.

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affichera auprès des clients comme titre de votre référencement d’offre. Ce champ est prérempli avec le texte que vous avez entré pour **Alias d’offre** lorsque vous avez créé l’offre, mais vous pouvez modifier cette valeur. Ce nom peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright). Le nom ne peut pas comporter plus de 50 caractères et ne peut pas contenir d’emoji.

### <a name="short-description"></a>Description courte

Renseignez une courte description de votre offre (100 caractères maximum) à utiliser dans les résultats de recherche dans une place de marché.

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

Pour que la description de votre offre soit plus accrocheuse, utilisez l’éditeur de texte enrichi pour appliquer la mise en forme.

![Utilisation de l’éditeur de texte enrichi](./media/rich-text-editor.png)

| <center>Changer la mise en forme du texte | <center>Ajouter des puces ou une numérotation | <center>Ajouter ou supprimer une mise en retrait de texte |
| --- | --- | --- |
| <center>![Using the rich text editor to change text format](./media/text-editor3.png) |  <center>![Using the rich text editor to add lists](./media/text-editor4.png) |  <center>![Using the rich text editor to indent](./media/text-editor5.png) |

### <a name="search-keywords"></a>Mots clés de recherche

Vous pouvez éventuellement entrer jusqu’à trois mots clés de recherche pour aider les clients à trouver votre offre sur la place de marché. Pour obtenir des résultats optimaux, essayez également d’utiliser ces mots clés dans votre description.

### <a name="products-your-app-works-with"></a>Produits utilisés par votre application

Si vous souhaitez permettre aux clients de savoir que votre application fonctionne avec des produits spécifiques, saisissez jusqu’à trois noms de produit ici.

### <a name="helpprivacy-urls"></a>URL Aide et URL Confidentialité

Cette section vous permet de fournir des liens permettant aux clients d’en savoir plus sur votre offre.

#### <a name="help-link"></a>Lien d'aide

Entrez l’URL à partir de laquelle les clients peuvent en savoir plus sur votre offre. Le **lien Aide** ne peut pas être le même que l’**URL du support** (voir ci-dessous).

#### <a name="privacy-policy-link"></a>Lien de la politique de confidentialité

Entrez l’URL du lien de la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre application respecte les lois et réglementations en matière de confidentialité et à renseigner une politique de confidentialité conforme.

### <a name="contact-information"></a>Informations de contact

Dans cette section, entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact d’ingénierie** et d’un **contact du service clientèle**. Ces informations ne sont pas présentées aux clients, mais seront disponibles pour Microsoft et pourront être fournies aux partenaires CSP.

Dans la section **Contact de support**, vous devez également fournir **l’URL du support technique** où les partenaires CSP peuvent trouver l’assistance pour votre offre. L’URL du support ne peut pas être la même que le **lien Aide**.

### <a name="supporting-documents"></a>Documents de soutien

Fournissez au moins un (et au plus trois) documents marketing associés ici, comme des livres blancs, des brochures, des listes de contrôle ou des présentations. Ces documents doivent être au format. pdf.

### <a name="marketplace-images"></a>Images de la Place de marché

Ajoutez des logos et des images pour votre offre. Toutes les images doivent être au format .png. Chargez le logo de votre offre en deux tailles :

* **Petite** (48 x 48 pixels)
* **Grande** (216 x 216 pixels)

>[!NOTE]
>Si vous rencontrez un problème de chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service `https://upload.xboxlive.com` utilisé par l’Espace partenaires.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez des captures d’écran qui illustrent le fonctionnement de votre offre. Au moins trois captures d’écran sont requises, et vous pouvez en ajouter jusqu’à cinq. Toutes les captures d’écran doivent être au format 1280 x 720 pixels.

#### <a name="videos"></a>Videos

Vous pouvez éventuellement ajouter jusqu’à cinq vidéos qui illustrent votre offre. Ces vidéos doivent être hébergées sur YouTube et/ou Vimeo. Pour chacune d’entre elles, entrez le nom de la vidéo, son URL et une image miniature de la vidéo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

[Meilleures pratiques pour les annonces d’offre d’une place de marché](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="availability"></a>Disponibilité

Cette page vous offre des options pour déterminer où et comment rendre votre offre disponible.

### <a name="markets"></a>Marchés

Cette section vous permet de spécifier les marchés dans lesquels votre offre doit être disponible. Pour ce faire, sélectionnez **Edit markets** (Modifier les marchés), ce qui affiche la fenêtre contextuelle **Sélection du marché**.

Sélectionnez au moins un marché pour publier votre offre. Choisissez **Sélectionner tout** pour rendre votre offre disponible sur tous les marchés possibles ou sélectionnez les marchés spécifiques que vous souhaitez ajouter.

Vos sélections ici s’appliquent uniquement aux nouvelles acquisitions. Si quelqu’un a déjà votre application sur un marché donné et que vous supprimez par la suite ce marché, les personnes qui disposent déjà de cette offre peuvent continuer à l’utiliser, mais aucun nouveau client sur ce marché ne pourra bénéficier de votre offre.

> [!IMPORTANT]
> Il vous incombe de respecter les exigences légales locales, même si ces exigences ne sont pas répertoriées ici ou dans l’Espace partenaires.

Gardez à l’esprit que, même si vous sélectionnez tous les marchés, les lois et restrictions locales ou d’autres facteurs peuvent empêcher certaines offres d’être répertoriées dans certains pays et régions.

### <a name="preview-audience"></a>Public de la version préliminaire

Avant de publier votre offre sur la place de marché générale, vous devez d’abord la mettre à disposition d’un **public limité en préversion**. Entrez une **Clé de masquage** (n’importe quelle chaîne utilisant uniquement des lettres minuscules et/ou des chiffres) ici. Les membres de votre public en préversion peuvent utiliser cette clé de masquage comme jeton pour afficher un aperçu de votre offre sur la place de marché.

Ensuite, lorsque vous êtes prêt à mettre votre offre à disposition générale et supprimer la restriction de la préversion, vous devez supprimer la **clé de masquage** et publier à nouveau.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="technical-configuration"></a>Configuration technique

Cette page définit les détails techniques utilisés pour se connecter à votre offre. Cette connexion permet d’approvisionner votre offre pour le client final, s’il choisit de l’acquérir.

### <a name="package-type"></a>Type de package

Sélectionnez l’option qui s’applique à votre offre :

* **Module complémentaire** : une application de module complémentaire étend l’expérience et les fonctionnalités existantes de Dynamics 365 Business Central. Pour plus d’informations, consultez l’article [Applications de module complémentaire](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Connexion** : une application de connexion peut être utilisée dans le scénario où une connexion point à point doit être établie entre Dynamics 365 Business Central et une solution ou un service tiers. Pour plus d’informations, consultez l’article [Applications de connexion](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Chargement de fichiers

Si vous avez sélectionné **Module complémentaire** ci-dessus, vous allez charger le fichier de package de votre offre, ainsi que les fichiers de package pour toute extension sur laquelle il a des dépendances.

#### <a name="extensions-package-file"></a>Fichier de package d’extension

Téléchargez le fichier de package d’extension (.app) pour votre offre.

#### <a name="library-package-file"></a>Fichier de package de bibliothèque

Obligatoire si votre offre doit être installée avec une autre extension qui n’est pas publiée sur la place de marché. Si c’est le cas, téléchargez le fichier .app ici.

#### <a name="dependency-package-file"></a>Fichier de package de dépendance

Obligatoire si votre offre doit être installée avec une autre extension qui a déjà été publiée sur la place de marché. Si c’est le cas, téléchargez le fichier `.app` ou `.zip` ici.

### <a name="url-to-app-installation"></a>URL d’installation de l’application

Si vous avez sélectionné **Connexion** ci-dessus, indiquez l’adresse pour l’installation de votre application ici. Pour les services connectés ne nécessitant pas d’installation, fournissez l’adresse de la page de destination ou d’inscription correspondant à votre service.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="test-drive-technical-configuration"></a>Configuration technique de la version d’évaluation

Cette page vous permet de configurer une démonstration (« version d’évaluation ») qui permet aux clients d’évaluer votre offre avant de l’acheter. Pour en savoir, consultez l’article [Qu’est-ce qu’une version d’évaluation ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Pour activer une version d’évaluation, cochez la case **Activer une version d’évaluation** sous l’onglet [Configuration de l’offre](#test-drive). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

Les types de versions d’évaluation suivants sont disponibles, chacun avec leurs propres configurations techniques requises.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Application logique](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuration technique non requise)

Ressources supplémentaires pour version d’évaluation :

- [Bonnes pratiques marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Meilleures pratiques techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Vue d’ensemble](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF ; veillez à désactiver votre bloqueur de fenêtres publicitaires.)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuration technique pour la version d’évaluation d’Azure Resource Manager

Un modèle de déploiement qui contient toutes les ressources Azure comprenant votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure. En savoir plus sur la configuration d’une [version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Régions** (obligatoire) : à l’heure actuelle, vous pouvez mettre votre version d’évaluation à disposition dans 26 régions Azure. En règle générale, vous devez rendre votre version d’évaluation disponible dans les régions où vous prévoyez le plus grand nombre de clients, de sorte qu’ils puissent sélectionner la région la plus proche pour des performances optimales. Vous devez vous assurer que votre abonnement a l’autorisation de déployer toutes les ressources nécessaires dans chacune des régions sélectionnées.

- **Instances** : sélectionnez le type (chaud ou froid) et le nombre d’instances disponibles, qui seront multipliés par le nombre de régions où votre offre sera disponible.

    **Chaud** : ce type d’instance est déployé et en attente de l’accès par région sélectionnée. Les clients peuvent accéder instantanément aux instances *à chaud* d’une version d’évaluation au lieu d’attendre un déploiement. L’inconvénient est que ces instances sont toujours en cours d’exécution sur votre abonnement Azure, entraînant des coûts de fonctionnement plus importants. Il est vivement recommandé d’avoir au moins une instance *à chaud*, étant donné que la plupart de vos clients ne souhaitent pas attendre la fin des déploiements complets, et il y a donc une chute dans l’utilisation du client si aucune instance *à chaud* n’est disponible.

    **Froid** : ce type d’instance représente le nombre total d’instances pouvant éventuellement être déployées par région. Les instances à froid nécessitent le modèle Resource Manager entier de la version d’évaluation pour être déployées lorsqu’un client demande la version d’évaluation, les instances *à froid* sont donc plus lentes à charger que les instances *à chaud*. L’inconvénient est que vous avez uniquement à payer pour la durée de la version d’évaluation, elle n’est *pas* toujours en cours d’exécution sur votre abonnement Azure comme c’est le cas avec une instance *à chaud*.

- **Version d’évaluation du modèle Azure Resource Manager** : chargez le fichier .zip contenant votre modèle Azure Resource Manager.  Apprenez-en plus sur la création d’un modèle Azure Resource Manager dans l’article de démarrage rapide [Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durée de la version d’évaluation** (obligatoire) : entrez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période. Utilisez uniquement des nombres entiers (par exemple, « 2 » heures est valide, « 1,5 » ne l’est pas).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuration technique pour la version d’évaluation de Dynamics 365

Microsoft peut retirer la complexité de la configuration d’une version d’évaluation en hébergeant et en conservant l’approvisionnement et le déploiement du service à l’aide de ce type de version d’évaluation. La configuration de ce type de version d’évaluation hébergé est la même, peu importe si la version d’évaluation cible un public Business Central, Customer Engagement ou Operations.

- **Nombre maximal de versions d’évaluation simultanées** (obligatoire) : définissez le nombre maximum de clients pouvant utiliser votre version d’évaluation simultanément. Chaque utilisateur simultané consommera une licence Dynamics 365 tant que la version d’évaluation est active. Veillez donc à disposer de suffisamment de licences pour prendre en charge la limite maximale définie. La valeur recommandée est de 3 à 5.

- **Durée de la version d’évaluation** (obligatoire) : entrez la durée d’activité de la version d’évaluation en définissant le nombre d’heures. Passée cette durée, la session prendra fin et aucune de vos licences ne sera plus utilisée. La valeur recommandée est de 2 à 24 heures, selon la complexité de votre offre. Vous ne pouvez définir la durée qu’en nombre d’heures complètes (par exemple, « 2 » pour 2 heures, « 1,5 » est incorrect).  L’utilisateur peut demander une nouvelle session s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.

- **URL de l’instance** (obligatoire) : URL où le client commencera sa version d’évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 exécutant votre application et où les exemples de données sont installés (par exemple `https://testdrive.crm.dynamics.com`).

- **URL de l’API web de l’instance**(obligatoire) : récupérez l’URL de l’API web pour votre instance Dynamics 365 en vous connectant à votre compte Microsoft 365 et en accédant à **Paramètres** \&gt; **Personnalisation** \&gt; **Ressources développeur** \&gt; **API web de l’instance (URL racine du service)** , puis copiez l’URL qui s’y trouve ici (par exemple, `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Nom de rôle** (obligatoire) : renseignez le nom du rôle de sécurité que vous avez défini dans votre version d’évaluation personnalisée de Dynamics 365, qui sera attribué à l’utilisateur lors de la version d’évaluation (par exemple, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuration technique pour la version d’évaluation d’applications logiques

Tous les produits personnalisés doivent utiliser ce type de modèle de déploiement de version d’évaluation, qui englobe une variété d’architectures de solution complexes. Pour plus d’informations sur la configuration de versions d’évaluation d’applications logiques, consultez [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) et [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) sur GitHub.

- **Région** (obligatoire, liste déroulante à sélection unique) : à l’heure actuelle, vous pouvez mettre votre version d’évaluation à disposition dans 26 régions Azure. Les ressources pour votre application logique seront déployées dans la région que vous sélectionnez. Si votre application logique compte des ressources personnalisées stockées dans une région spécifique, assurez-vous que celle-ci est sélectionnée ici. La meilleure façon de procéder consiste à déployer entièrement votre application logique localement vers votre abonnement Azure dans le portail et à vérifier qu’elle fonctionne correctement avant de la sélectionner.

- **Nombre maximal de versions d’évaluation simultanées** (obligatoire) : définissez le nombre maximum de clients pouvant utiliser votre version d’évaluation simultanément. Ces versions d’évaluation sont déjà déployées, permettant ainsi aux clients d’y accéder instantanément sans attendre un déploiement.

- **Durée de la version d’évaluation** (obligatoire) : entrez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période.

- **Nom du groupe de ressources Azure** (obligatoire) : entrez le nom du [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) dans lequel votre version d’évaluation d’application logique est enregistrée.

- **Nom de l’application logique Azure** (obligatoire) : entrez le nom de l’application logique qui attribue la version d’évaluation à l’utilisateur. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

- **Déprovisionner le nom d’application logique** (obligatoire) : entrez le nom de l’application logique qui déprovisionne la version d’évaluation une fois que le client a terminé. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuration technique non requise pour les versions d’évaluation de Power BI

Les produits souhaitant afficher un visuel interactif Power BI peuvent utiliser un lien hypertexte pour partager un tableau de bord personnalisé et intégré qui sert de version d’évaluation, sans configuration technique supplémentaire. En savoir plus sur la configuration d’applications de modèle [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview).

### <a name="deployment-subscription-details"></a>Détails de l’abonnement de déploiement

Pour déployer la version d’évaluation de votre côté, créez et fournissez un abonnement Azure séparé et unique. (Non obligatoire pour les versions d’évaluation Power BI).

- **ID d’abonnement Azure** (obligatoire pour Azure Resource Manager et les applications logiques) : entrez l’ID d’abonnement pour accorder l’accès aux services de votre compte Azure à des fins de facturation et de création de rapports sur l’utilisation des ressources. Nous vous recommandons d’envisager la [création d’un abonnement Azure distinct](https://docs.microsoft.com/azure/billing/billing-create-subscription) à utiliser pour les versions d’évaluation si vous n’en avez pas déjà. Pour trouver vos ID d’abonnement Azure en vous connectant au [portail Azure](https://portal.azure.com/) et en accédant à l’onglet **Abonnements** dans le menu de gauche. Sélectionnez l’onglet pour afficher votre ID d’abonnement (par exemple, « a83645ac-1234-5ab6-6789-1h234g764ghty »).

- **ID de locataire Azure AD** (obligatoire) : entrez votre [ID de locataire](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory (AD). Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Propriétés, cherchez le numéro de l’ **ID du répertoire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Vous pouvez aussi rechercher l’ID de locataire de votre organisation à partir de l’adresse de votre nom de domaine à l’adresse [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nom de locataire Azure AD** (obligatoire pour Dynamic 365) : entrez votre nom Azure Active Directory (AD). Pour rechercher ce nom, connectez-vous au [portail Azure](https://portal.azure.com/), et dans le coin supérieur droit, le nom de votre locataire s’affichera sous le nom de votre compte.

- **ID d’application Azure AD** (obligatoire) : entrez votre [ID d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory (AD). Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Inscriptions d’applications**, cherchez le numéro de l’**ID de l’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **Clé secrète client Azure AD** (obligatoire) : entrez la [clé secrète client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) de votre application Azure AD. Pour trouver cette valeur, connectez-vous au [portail Azure](https://portal.azure.com/). Dans le volet de navigation gauche, sélectionnez l’onglet **Azure Active Directory**,puis **Inscriptions d’applications**, et enfin votre application de version d’évaluation. Ensuite, sélectionnez **Certificats et clés secrètes**, **Nouvelle clé secrète client**, entrez une description, sélectionnez **Jamais** sous **Expiration** puis choisissez **Ajouter**. Veillez à copier la valeur. (Ne quittez pas la page avant d’avoir terminé, ou vous n’aurez pas accès à la valeur.)

### <a name="test-drive-marketplace-listings"></a>Référencements de versions d'évaluation sur la place de marché

L’option **Référencement sur le marketplace** sous l’onglet **Version d’évaluation** vous permet de définir les détails de la version d’évaluation.

> [!NOTE]
> Vous ne pouvez fournir les détails de la version d’évaluation que dans une seule langue. Les détails de l’offre n’ont pas à être en anglais si la description de l’offre commence par l’expression « This application is available only in [langue non anglaise] ». Il est également possible de fournir l’*URL du lien Aide* pour offrir du contenu dans une langue autre que celle utilisée dans la description de la version d’évaluation.

- **Description** (obligatoire) : décrivez votre version d’évaluation, ce qu’elle montre, des objectifs d’expérimentation pour l’utilisateur, des fonctionnalités à explorer et fournissez toute autre information pouvant aider l’utilisateur dans sa décision d’achat. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ. 

- **Informations d’accès** (obligatoire pour les versions d’évaluation Azure Resource Manager et Logic Apps) : expliquez ce qu’un client doit savoir pour pouvoir accéder à cette version d’évaluation et l’utiliser. Détaillez un scénario d’utilisation de votre offre ainsi que tout ce que le client doit savoir pour accéder aux fonctionnalités disponibles dans la version d’évaluation. Jusqu’à 10 000 caractères peuvent être entrés dans ce champ.

- **Manuel de l’utilisateur** (obligatoire) : présentation approfondie de l’expérience de votre version d’évaluation. Le manuel de l’utilisateur doit couvrir exactement ce que vous souhaitez que le client accomplisse dans la version d’évaluation et sert de référence pour toutes les questions qu’ils peuvent avoir. Le fichier doit être au format PDF et être nommé (255 caractères maximum) après le chargement.

- **Vidéos** (facultatif) : vous pouvez charger des vidéos sur YouTube ou Vimeo et les référencer ici via un lien et une image miniature (533 x 324 pixels) pour fournir au client des informations complètes qui l’aideront à mieux comprendre la version d’évaluation, notamment la façon d’exploiter les fonctionnalités de l’offre et de comprendre les scénarios qui mettent en avant ses avantages.
  - **Nom** (obligatoire)
  - **URL (YouTube ou Vimeo uniquement)** (obligatoire)
  - **Image miniature** (ce fichier doit être au format PNG et d’une taille de 533 x 324 pixels)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="supplemental-content"></a>Contenu supplémentaire

Cette page vous permet de fournir des informations supplémentaires sur votre offre pour que nous puissions la valider. Ces informations ne sont pas montrées aux clients ni publiées sur la Place de marché.

### <a name="target-release"></a>Version cible

Indiquez la version de Microsoft Dynamics Business Central ciblée par votre solution : **Actuelle**, **Majeure suivante** ou **Mineure suivante**. Ces informations nous permettent de tester votre solution de manière appropriée.

### <a name="supported-editions"></a>Éditions prises en charge

Si votre offre nécessite l’édition Premium de Microsoft Dynamics 365 Business Central, sélectionnez **Premium** uniquement. Sinon, sélectionnez à la fois **Essentials** et **Premium**.

### <a name="key-usage-scenario"></a>Principaux scénarios d’utilisation

Vous devez charger un fichier PDF dans lequel sont répertoriés les principaux scénarios d’usage de votre offre. Tous les scénarios répertoriés ici peuvent être vérifiés par notre équipe de validation avant que nous approuvions votre offre pour la place de marché.

### <a name="app-tests-automation"></a>Automatisation des tests d’application

Si votre offre est une application de module complémentaire, vous devez charger un fichier d’**automatisation des tests d’application** (.app). Ce fichier ne s’applique pas aux applications de connexion.

### <a name="test-accounts"></a>Comptes de test

Si un compte de test est nécessaire pour que notre équipe de certification puisse examiner correctement votre offre, vous devrez charger un fichier au format .pdf, .doc ou .docx avec les informations de vos **Comptes de test**.

## <a name="publish"></a>Publish

### <a name="submit-offer-to-preview"></a>Soumettre l’offre en préversion

Une fois que vous avez rempli toutes les sections nécessaires de l’offre, sélectionnez **Publier** en haut à droite du portail. Vous serez redirigé vers la page **Vérifier et publier**. 

S’il s’agit de votre première publication de cette offre, vous pouvez :

- Consultez l’état d’achèvement de chaque section de l’offre.
    - *Non démarrée* : cela signifie que la section n’a pas encore été modifiée et doit être complétée.
    - *Incomplète* : cela signifie que la section comporte des erreurs qui doivent être corrigées ou qui nécessitent plus d’informations. Revenir à la ou les sections et les mettre à jour.
    - *Complète* : cela signifie que la section est terminée, que toutes les données requises ont été renseignées et qu’il n’y a aucune erreur. Toutes les sections de l’offre doivent être dans l’état Complète avant de pouvoir envoyer l’offre.
- Dans la sections **Notes pour la certification**, fournissez des instructions de test à l’équipe de certification pour veiller à ce que votre application soit testée correctement, en plus de toutes autres notes supplémentaires utiles pour comprendre l’application.
- Envoyez l’offre pour publication en sélectionnant **Envoyer**. Nous vous enverrons un e-mail si une version d’essai de l’offre est disponible pour vérification et approbation. Retourner dans Partner Center et sélectionner **Publier** pour que votre offre soit publiée pour le public (ou au public privé, si l’offre est privée).

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](./update-existing-offer.md)
