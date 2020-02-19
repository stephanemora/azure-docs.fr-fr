---
title: Créer une nouvelle offre Dynamics 365 Business Central dans la Place de marché commerciale existante
description: Comment créer une nouvelle offre Dynamics 365 Business Central pour répertorier ou vendre dans la Place de marché Azure, AppSource ou via le programme de fournisseur de solutions Cloud (CSP) avec le portail de la Place de marché commerciale sur Microsoft Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 4c0467039cf4fefd7625f1146c4bade99b49304d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048724"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Créer une offre Dynamics 365 Business Central

Cette rubrique explique comment créer une nouvelle offre Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) est un système de planification des ressources d’entreprise (ERP) qui gère un vaste éventail de processus d’entreprise, notamment en lien avec les finances, les opérations, la chaîne logistique, la gestion de la relation client et le commerce électronique. Les packages Premium prennent également en charge le modèle de déploiement classique et la fabrication. Toutes les offres pour Dynamics 365 Business Central doivent passer par notre processus de certification.

Pour commencer à créer des offres Dynamics 365 Business Central, veillez à d’abord [Créer un compte Partner Center](./create-account.md) et ouvrez le [tableau de bord de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), et sélectionnez la page **Présentation**.

![Tableau de bord de la Place de marché commerciale sur Partner Center](./media/new-offer-overview.png)

>[!Note]
> Dès lors qu’une offre est publiée, les modifications dont elle fait l’objet dans l’Espace partenaires sont uniquement mises à jour dans le système. Elles le sont dans le store après republication. Veillez à soumettre l’offre pour publication après y avoir apporté des modifications.

## <a name="create-a-new-offer"></a>Créer une offre

Sélectionnez le bouton **+ Nouvelle offre**, puis sélectionnez l'élément de menu **Dynamics 365 Business Central**. La boîte de dialogue **Nouvelle offre** s’affiche.

### <a name="offer-id-and-alias"></a>ID et alias de l’offre

- **ID de l’offre** : Identificateur unique par offre dans votre compte. Cet ID sera visible par les clients dans l’adresse URL de l’offre de la Place de marché et des modèles Resource Manager (si applicable). L’ID de l’offre doit être en caractères alphanumériques minuscules (traits d’union et traits de soulignement compris, mais sans espaces blancs), est limité à 50 caractères et n’est pas modifiable une fois que vous avez sélectionné **Créer**.  Par exemple, si vous entrez *test-offer-1* ici, l’URL de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- **Alias de l’offre** : Le nom utilisé pour faire référence à l’offre dans l’espace partenaires. Ce nom n’est pas utilisé dans la place de marché et est différent du nom de l’offre et d’autres valeurs qui seront présentées aux clients. Cette valeur ne peut pas être modifiée après que vous ayez sélectionné **Créer**.

Une fois que vous avez entré **l’ID de l’offre** et **l’alias de l’offre**, sélectionnez **Créer**. Vous pourrez alors travailler sur les différentes parties de votre offre.

## <a name="offer-setup"></a>Configuration de l’offre

La page **Configuration de l’offre** nécessite les informations suivantes. N’oubliez pas de sélectionner **Enregistrer** après avoir renseigné ces champs.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Comment voulez-vous qu’interagissent les clients avec l’offre de liste ?

Sélectionnez l’option que vous souhaitez utiliser pour cette offre.

#### <a name="get-it-now-free"></a>Obtenez-en une maintenant (gratuit)

Répertoriez votre offre gratuitement pour les clients en fournissant une URL valide (qui commence par *http* ou *https*) où ils peuvent accéder à votre application.  Par exemple : `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Essai gratuit (annonce)

Répertoriez votre offre en tant qu’essai gratuit pour les clients en fournissant une URL valide (qui commence par *http* ou *https*) où ils peuvent accéder obtenir un essai.  Par exemple : `https://contoso.com/trial/my-app`. Les versions d’évaluation gratuites sont créées, gérées et configurées par votre service et n’ont pas d’abonnements gérés par Microsoft.

> [!NOTE]
> Les jetons que votre application recevra via votre lien d’évaluation peuvent uniquement être utilisés pour obtenir des informations utilisateur par le biais de Azure Active Directory (Azure AD) pour automatiser la création de comptes dans votre application. Les comptes Microsoft ne sont pas pris en charge pour l’authentification à l’aide de ce jeton.

#### <a name="contact-me"></a>Me contacter

Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Connecter la gestion des prospects](#connect-lead-management). 

### <a name="test-drive"></a>Test drive

Une version d’évaluation vous permet de présenter votre offre à vos clients potentiels en leur donnant la possibilité de l’« essayer avant de l’acheter », ce qui se traduit par une hausse du taux de conversion et par la génération de prospects hautement qualifiés. [Découvrez-en plus sur les versions d’évaluation.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Pour activer une version d'évaluation, activez la case à cocher **Activer une version d'évaluation**. Vous devez ensuite configurer un environnement de démonstration dans la [Configuration technique de la version d'évaluation](#test-drive-technical-configuration) pour permettre aux clients d’essayer votre offre pendant une période de temps fixe. 

#### <a name="type-of-test-drive"></a>Type de version d’évaluation

Choisissez parmi les options suivantes :

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Un modèle de déploiement qui contient toutes les ressources Azure comprenant votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure.
- **[Dynamics 365 pour Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft héberge et conserve le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système de planification des ressources Business Central (finance, opérations, chaîne d’approvisionnement, CRM, etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft héberge et conserve le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système Customer Engagement (ventes, service, service de projet, service après-vente, etc.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft héberge et conserve le service de version d’évaluation (dont l’approvisionnement et le déploiement) pour un système de planification des ressources d’une entreprise Finance et opérations (finance, opérations, manufacture, chaîne d’approvisionnement, etc.). 
- **[Application logique](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Un modèle de déploiement englobant toutes les architectures de solution complexe. Tous les produits personnalisés doivent tous utiliser ce type de version d’évaluation.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Un lien hypertexte vers un tableau de bord personnalisé. Les produits dotés d’un visuel Power BI interactif doivent utiliser ce type de version d’évaluation. Il vous suffit de charger votre URL Power BI incorporée.

#### <a name="additional-test-drive-resources"></a>Ressources supplémentaires pour version d’évaluation

- [Meilleures pratiques techniques pour version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Meilleures pratiques marketing pour version d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Document d’une page pour présenter une version d’évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Connecter la gestion des prospects

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Pour plus d’informations, consultez [Vue d’ensemble de la gestion des prospects](./commercial-marketplace-get-customer-leads.md).

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

## <a name="properties"></a>Propriétés

La page **Propriétés** est l’emplacement où vous définissez les catégories et secteurs utilisés pour grouper votre offre selon les places de marché, la version de votre application et les contrats légaux associés. Sélectionnez **Enregistrer** après avoir renseigné cette page.

### <a name="category"></a>Category

Sélectionnez un minimum d’une catégorie et un maximum de trois catégories qui permettent de placer votre offre dans les zones de recherche appropriées sur la Place de marché. Veillez à expliquer dans la description comment votre offre prend en charge ces catégories dans la description de l’offre. 

### <a name="industry"></a>Secteur d’activité

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Version de l’application

Entrez le numéro de version de votre offre. Cette version est indiquée aux clients sur la page de détails de l’offre.

### <a name="terms-and-conditions"></a>Conditions générales

Fournissez vos propres conditions générales dans le champ **Conditions générales**. Vous pouvez également fournir l’URL permettant d’accéder à vos conditions générales. Les clients doivent accepter ces conditions avant de pouvoir essayer votre offre.

## <a name="offer-listing"></a>Annonce de l’offre

La page de référencement de l’offre affiche les langues dans lesquelles votre offre sera répertoriée. Actuellement, **Anglais (États-Unis)** est la seule option disponible.

Vous devez définir les informations de la Place de marché (nom de l’offre, description, images, etc.) pour chaque langue/marché. Sélectionnez le nom de la langue/du marché pour fournir ces informations.

> [!NOTE]
> Il n’est pas nécessaire que le contenu du référencement de l’offre (par exemple, la description, les documents, les captures d’écran, les conditions d’utilisation, etc.) soit en anglais, tant que sa description commence par l’expression : « This application is available only in [langue autre que l’anglais]. » Il est également possible de fournir une *URL de lien utile* pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de la liste des offres.

### <a name="name"></a>Name

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

Pour que la description de votre offre soit plus attrayante, utilisez l’éditeur de texte enrichi pour mettre en forme votre description.

![Utilisation de l’éditeur de texte enrichi](./media/text-editor2.png)

Suivez les instructions suivantes pour utiliser l’éditeur de texte enrichi :

- Pour changer la mise en forme de votre contenu, mettez en surbrillance le texte que vous souhaitez mettre en forme, puis sélectionnez un style de texte, comme indiqué ci-dessous :

     ![Utilisation de l’éditeur de texte enrichi pour changer la mise en forme du texte](./media/text-editor3.png)

- Pour ajouter une liste à puces ou une liste numérotée au texte, utilisez les options ci-dessous :

     ![Utilisation de l’éditeur de texte enrichi pour ajouter des listes](./media/text-editor4.png)

- Pour ajouter ou supprimer une mise en retrait du texte, utilisez les options ci-dessous :

     ![Utilisation de l’éditeur de texte enrichi pour mettre en retrait](./media/text-editor5.png)

### <a name="search-keywords"></a>Mots clés de recherche

Vous pouvez éventuellement entrer jusqu’à trois mots clés de recherche pour aider les clients à trouver votre offre sur la place de marché. Pour obtenir des résultats optimaux, essayez également d’utiliser ces mots clés dans votre description.

### <a name="products-your-app-works-with"></a>Produits utilisés par votre application

Si vous souhaitez permettre aux clients de savoir que votre application fonctionne avec des produits spécifiques, saisissez jusqu’à trois noms de produit ici.

### <a name="support-urls"></a>URL de support technique

Cette section vous permet de fournir des liens permettant aux clients d’en savoir plus sur votre offre.

#### <a name="help-link"></a>Lien d'aide

Entrez l’URL à partir de laquelle les clients peuvent en savoir plus sur votre offre.

#### <a name="privacy-policy-url"></a>URL de la politique de confidentialité

Entrez l’URL du lien de la politique de confidentialité de votre organisation. Vous devez veiller à ce que votre application respecte les lois et réglementations en matière de confidentialité et à renseigner une politique de confidentialité conforme.

### <a name="contacts"></a>Contacts

Dans cette section, entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact d’ingénierie** et d’un **contact du service clientèle**. Ces informations ne sont pas présentées aux clients, mais seront disponibles pour Microsoft et pourront être fournies aux partenaires CSP.

Dans la section **Contact de support**, vous devez également fournir **l’URL du support technique** où les partenaires CSP peuvent trouver l’assistance pour votre offre.

### <a name="supporting-documents"></a>Documents de soutien

Fournissez au moins un (et au plus trois) documents marketing associés ici, comme des livres blancs, des brochures, des listes de contrôle ou des présentations. Ces documents doivent être au format. pdf.

### <a name="marketplace-images"></a>Images de la Place de marché

Dans cette section, vous pouvez fournir des logos et des images qui seront utilisés pour montrer votre offre au client. Toutes les images doivent être au format .png.

#### <a name="store-logos"></a>Stocker des logos

Fournissez le logo de votre offre en deux tailles : **Petit (48 x 48)** et **Grand (216 x 216)** .

#### <a name="hero"></a>Bannière

L’image de héros est facultative. Si vous en fournissez une, elle doit être au format 815 x 290 pixels.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez des captures d’écran qui illustrent le fonctionnement de votre offre. Au moins trois captures d’écran sont requises, et vous pouvez en ajouter jusqu’à cinq. Toutes les captures d’écran doivent être au format 1280 x 720 pixels.

#### <a name="videos"></a>Videos

Vous pouvez éventuellement ajouter jusqu’à cinq vidéos qui illustrent votre offre. Ces vidéos doivent être hébergées sur YouTube et/ou Vimeo. Pour chacune d’entre elles, entrez le nom de la vidéo, son URL et une image miniature de la vidéo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

- [Meilleures pratiques pour les annonces d’offre d’une place de marché](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Disponibilité

La page **Disponibilité** vous offre des options pour déterminer où et comment rendre votre offre disponible.

### <a name="markets"></a>Marchés

Cette section vous permet de spécifier les marchés dans lesquels votre offre doit être disponible. Pour ce faire, sélectionnez **Edit markets** (Modifier les marchés), ce qui affiche la fenêtre contextuelle **Sélection du marché**.

Par défaut, aucun marché n’est sélectionné, mais vous devez sélectionner au moins un marché pour pouvoir publier votre offre. Cliquez sur **Sélectionner tout** pour rendre votre offre disponible sur tous les marchés possibles ou sélectionnez les marchés spécifiques que vous souhaitez ajouter. Quand vous avez terminé, sélectionnez **Enregistrer**.

Vos sélections ici s’appliquent uniquement aux nouvelles acquisitions. Si quelqu’un a déjà votre application sur un marché donné et que vous supprimez par la suite ce marché, les personnes qui disposent déjà de cette offre peuvent continuer à l’utiliser, mais aucun nouveau client sur ce marché ne pourra bénéficier de votre offre.

> [!IMPORTANT]
> Il vous incombe de respecter les exigences légales locales, même si ces exigences ne sont pas répertoriées ici ou dans l’Espace partenaires.

Gardez à l’esprit que même si vous sélectionnez tous les marchés, les lois et restrictions locales ou d’autres facteurs peuvent empêcher certaines offres d’être répertoriées dans certains pays et régions.

### <a name="preview-audience"></a>Public de la version préliminaire

Avant de publier votre offre sur la place de marché générale, vous devez d’abord la mettre à disposition d’un **public limité en préversion**. Entrez une **Clé de masquage** (n’importe quelle chaîne utilisant uniquement des lettres minuscules et/ou des chiffres) ici. Les membres de votre public en préversion peuvent utiliser cette clé de masquage comme jeton pour afficher un aperçu de votre offre sur la place de marché.

Ensuite, lorsque vous êtes prêt à mettre votre offre à disposition générale et supprimer la restriction de la préversion, vous devez supprimer la **clé de masquage** et publier à nouveau.

## <a name="technical-configuration"></a>Configuration technique

La page **Configuration technique** définit les détails techniques utilisés pour se connecter à votre offre. Cette connexion permet d’approvisionner votre offre pour le client final, s’il choisit de l’acquérir.

### <a name="package-type"></a>Type de package

Sélectionnez l’option qui s’applique à votre offre :

- **Module complémentaire** : Une application de module complémentaire étend l’expérience et les fonctionnalités existantes de Dynamics 365 Business Central. Pour plus d’informations, consultez [Applications de module complémentaire](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Connecter** : Une application de connexion peut être utilisée dans le scénario où une connexion point à point doit être établie entre Dynamics 365 Business Central et une solution ou un service tiers. Pour plus d’informations, consultez [Connexion](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Chargement de fichiers

Si vous avez sélectionné **Module complémentaire** ci-dessus, vous allez charger le fichier de package de votre offre, ainsi que les fichiers de package pour toute extension sur laquelle il a des dépendances.

#### <a name="extensions-package-file"></a>Fichier de package d’extension

Téléchargez le fichier de package d’extension (.app) pour votre offre.

#### <a name="library-package-file"></a>Fichier de package de bibliothèque

Obligatoire si votre offre doit être installée avec une autre extension qui n’est pas publiée sur la place de marché. Si c’est le cas, téléchargez le fichier .app ici.

#### <a name="dependency-package-file"></a>Fichier de package de dépendance

Obligatoire si votre offre doit être installée avec une autre extension qui a déjà été publiée sur la place de marché. Si c’est le cas, téléchargez le fichier `.app` ou `.zip` ici.

### <a name="url-to-app-installation"></a>URL d’installation de l’application

Si vous avez sélectionné **Connexion** ci-dessus, indiquez l’URL de l’installation de votre application ici.

## <a name="test-drive-technical-configuration"></a>Configuration technique de la version d’évaluation

Si vous avez sélectionné **Activer une version d'évaluation** dans la page [Configuration de l’offre](#offer-setup), vous devez fournir des détails ici pour permettre aux clients de bénéficier d’une version d'évaluation de votre offre.

La page **Version d’évaluation** vous permet de configurer une démonstration (ou « version d’évaluation ») qui permettra à des clients d’essayer votre offre avant de s’engager et l’acheter. Pour en savoir, consultez l’article [Qu’est-ce qu’une version d’évaluation ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Si vous ne souhaitez plus fournir une version d’évaluation de votre offre, retournez sur la page **[Configuration de l’offre](#offer-setup)** et décochez **Activer la version d’évaluation**.

Les types de versions d’évaluation suivants sont disponibles, chacun avec leurs propres configurations techniques requises.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Application logique](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuration technique non requise)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuration technique pour la version d’évaluation d’Azure Resource Manager

Un modèle de déploiement qui contient toutes les ressources Azure comprenant votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure. En savoir plus sur la configuration d’une [version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Régions** (obligatoire) : Il existe actuellement 26 régions Azure prises en charge où votre version d’évaluation peut être rendue disponible. En règle générale, vous devez rendre votre version d’évaluation disponible dans les régions où vous prévoyez le plus grand nombre de clients, de sorte qu’ils puissent sélectionner la région la plus proche pour des performances optimales. Vous devez vous assurer que votre abonnement a l’autorisation de déployer toutes les ressources nécessaires dans chacune des régions sélectionnées.

- **Instances** : Sélectionnez le type (chaud ou froid) et le nombre d’instances disponibles, qui seront multipliés par le nombre de régions dans lesquelles votre offre est disponible.

**Hot** : Ce type d’instance est déployé et en attente de l’accès par région sélectionnée. Les clients peuvent accéder instantanément aux instances *à chaud* d’une version d’évaluation au lieu d’attendre un déploiement. L’inconvénient est que ces instances sont toujours en cours d’exécution sur votre abonnement Azure, entraînant des coûts de fonctionnement plus importants. Il est vivement recommandé d’avoir au moins une instance *à chaud*, étant donné que la plupart de vos clients ne souhaitent pas attendre la fin des déploiements complets, et il y a donc une chute dans l’utilisation du client si aucune instance *à chaud* n’est disponible.

**À froid** : Ce type d’instance représente le nombre total d’instances pouvant éventuellement être déployées par région. Les instances à froid nécessitent le modèle Resource Manager entier de la version d’évaluation pour être déployées lorsqu’un client demande la version d’évaluation, les instances *à froid* sont donc plus lentes à charger que les instances *à chaud*. L’inconvénient est que vous avez uniquement à payer pour la durée de la version d’évaluation, elle n’est *pas* toujours en cours d’exécution sur votre abonnement Azure comme c’est le cas avec une instance *à chaud*.

- **Modèle Azure Resource Manager de version d’évaluation** : Téléchargez le fichier .zip contenant votre modèle Azure Resource Manager.  Apprenez-en plus sur la création d’un modèle Azure Resource Manager dans l’article de démarrage rapide [Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durée d’une version d’évaluation** (obligatoire) : Saisissez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période. Vous ne pouvez définir la durée qu’en nombre d’heures complètes (par exemple, « 2 » pour 2 heures, « 1,5 » est incorrect).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuration technique pour la version d’évaluation de Dynamics 365

Microsoft peut retirer la complexité de la configuration d’une version d’évaluation en hébergeant et en conservant l’approvisionnement et le déploiement du service à l’aide de ce type de version d’évaluation. La configuration de ce type de version d’évaluation hébergé est la même, peu importe si la version d’évaluation cible un public Business Central, Customer Engagement ou Operations.

- **Versions d’évaluation simultanées max** (obligatoire) : Définissez le nombre maximum de client pouvant utiliser votre version d’évaluation à la fois. Chaque utilisateur simultané consommera une licence Dynamics 365 tant que la version d’évaluation est active. Veillez donc à disposer de suffisamment de licences pour prendre en charge la limite maximale définie. La valeur recommandée est de 3 à 5.

- **Durée d’une version d’évaluation** (obligatoire) : Saisissez la durée d’activité de la version d’évaluation, en indiquant le nombre d’heures. Passée cette durée, la session prendra fin et aucune de vos licences ne sera plus utilisée. La valeur recommandée est de 2 à 24 heures, selon la complexité de votre offre. Vous ne pouvez définir la durée qu’en nombre d’heures complètes (par exemple, « 2 » pour 2 heures, « 1,5 » est incorrect).  L’utilisateur peut demander une nouvelle session s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.

- **URL de l’instance** (obligatoire) : L’URL où le client commencera sa version d’évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 sur laquelle votre application et les exemples de données sont installés (par exemple, https://testdrive.crm.dynamics.com).

- **URL d’API web de l’instance** (obligatoire): Récupérez l’URL d’API web pour votre instance Dynamics 365 en vous connectant à votre compte Microsoft 365 et en allant dans **Paramètres** \&gt; **Personnalisation** \&gt; **Ressources développeur** \&gt; **API web de l’instance (URL racine de service)** , copiez l’URL trouvée ici (par exemple, https://testdrive.crm.dynamics.com/api/data/v9.0) ).

- **Nom de rôle** (obligatoire).: Renseignez le nom du rôle de sécurité que vous avez défini dans votre version d’évaluation personnalisée de Dynamics 365, qui sera attribué à l’utilisateur lors de la version d’évaluation (par exemple, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuration technique pour la version d’évaluation d’applications logiques

Tous les produits personnalisés doivent utiliser ce type de modèle de déploiement de version d’évaluation, qui englobe une variété d’architectures de solution complexes. Pour plus d’informations sur la configuration de versions d’évaluation d’applications logiques, consultez [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) et [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) sur GitHub.

- **Région** (liste déroulante obligatoire, à sélection unique) : Il existe actuellement 26 régions Azure prises en charge où votre version d’évaluation peut être rendue disponible. Les ressources pour votre application logique seront déployées dans la région que vous sélectionnez. Si votre application logique compte des ressources personnalisées stockées dans une région spécifique, assurez-vous que celle-ci est sélectionnée ici. La meilleure façon de procéder consiste à déployer entièrement votre application logique localement vers votre abonnement Azure dans le portail et à vérifier qu’elle fonctionne correctement avant de la sélectionner.

- **Versions d’évaluation simultanées max** (obligatoire) : Définissez le nombre maximum de client pouvant utiliser votre version d’évaluation à la fois. Ces versions d’évaluation sont déjà déployées, permettant ainsi aux clients d’y accéder instantanément sans attendre un déploiement.

- **Durée d’une version d’évaluation** (obligatoire) : Saisissez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période.

- **Nom du groupe de ressources Azure** (obligatoire) : Entrez le nom du [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) où la version d’évaluation de votre application logique est enregistrée.

- **Nom de l’application logique Azure** (obligatoire) : Entrez le nom de l’application logique qui affecte la version d’évaluation à l’utilisateur. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

- **Nom de l’application logique de déprovisionnement** (obligatoire) : Entrez le nom de l’application logique qui déprovisionne la version d’évaluation, une fois que le client a terminé. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuration technique non requise pour les versions d’évaluation de Power BI

Les produits souhaitant afficher un visuel interactif Power BI peuvent utiliser un lien hypertexte pour partager un tableau de bord personnalisé et intégré qui sert de version d’évaluation, sans configuration technique supplémentaire. En savoir plus sur la configuration d’applications de modèle [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview).

### <a name="deployment-subscription-details"></a>Détails de l’abonnement de déploiement

Pour déployer la version d’évaluation de votre côté, créez et fournissez un abonnement Azure séparé et unique. (Non obligatoire pour les versions d’évaluation Power BI).

- **ID d’abonnement Azure** (obligatoire pour Azure Resource Manager et les applications logiques) : Entrez l’ID d’abonnement pour accorder l’accès à vos services de compte Azure pour obtenir un rapport et une tarification sur l’utilisation des ressources. Nous vous recommandons d’envisager la [création d’un abonnement Azure distinct](https://docs.microsoft.com/azure/billing/billing-create-subscription) à utiliser pour les versions d’évaluation si vous n’en avez pas déjà. Pour trouver vos ID d’abonnement Azure en vous connectant au [portail Azure](https://portal.azure.com/) et en accédant à l’onglet **Abonnements** dans le menu de gauche. Sélectionnez l’onglet pour afficher votre ID d’abonnement (par exemple, « a83645ac-1234-5ab6-6789-1h234g764ghty »).

- **ID du locataire Azure AD** (obligatoire) : Saisissez l’[ID client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) de votre Azure Active Directory. Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Propriétés, cherchez le numéro de l’ **ID du répertoire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Vous pouvez aussi rechercher l’ID locataire de votre organisation à l’aide l’URL de votre nom de domaine : [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nom du locataire Azure AD** (obligatoire pour Dynamics 365) : Entrez le nom de votre locataire Azure Active Directory. Pour rechercher ce nom, connectez-vous au [portail Azure](https://portal.azure.com/), et dans le coin supérieur droit, le nom de votre locataire s’affichera sous le nom de votre compte.

- **ID de l’application Azure AD** (obligatoire) : Entrez l’[ID de votre application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory. Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Inscriptions d’applications**, cherchez le numéro de l’**ID de l’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **Clé secrète client de l’application Azure AD** (obligatoire) : Entrez la [clé secrète client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) d’application Azure AD. Pour trouver cette valeur, connectez-vous au [portail Azure](https://portal.azure.com/). Dans le volet de navigation gauche, sélectionnez l’onglet **Azure Active Directory**,puis **Inscriptions d’applications**, et enfin votre application de version d’évaluation. Ensuite, sélectionnez **Certificats et clés secrètes**, **Nouvelle clé secrète client**, entrez une description, sélectionnez **Jamais** sous **Expiration** puis choisissez **Ajouter**. Veillez à copier la valeur. (Ne quittez pas la page avant d’avoir terminé, ou vous n’aurez pas accès à la valeur.)

N’oubliez pas d’**Enregistrer** avant de passer à la section suivante.

### <a name="test-drive-marketplace-listings"></a>Référencements de versions d'évaluation sur la place de marché

L'option **Référencement sur la place de marché** située sous l’onglet **Version d'évaluation** affiche les langues dans lesquelles votre version d'évaluation est disponible. Actuellement, **Anglais (États-Unis)** est le seul emplacement disponible. Sélectionnez le nom de la langue pour entrer des informations qui décrivent l’expérience de la version d'évaluation.

- **Description** (obligatoire) : Décrivez votre version d’évaluation, ce qui sera présenté, les objectifs que l’utilisateur pourra essayer, les fonctionnalités à explorer, et toute autre information qui aidera l’utilisateur à déterminer ou non s’il souhaite faire l’acquisition de votre offre. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ. 

- **Accéder aux informations** (obligatoire pour les versions d’évaluation Azure Resource Manager et d’applications logiques) : Expliquez ce qu’un client doit savoir pour accéder à cette version d’évaluation et l’utiliser. Détaillez un scénario d’utilisation de votre offre ainsi que tout ce que le client doit savoir pour accéder aux fonctionnalités disponibles dans la version d’évaluation. Jusqu’à 10 000 caractères peuvent être entrés dans ce champ.

- **Manuel de l’utilisateur** (obligatoire) : Procédure approfondie de l’expérience de votre version d’évaluation. Le manuel de l’utilisateur doit couvrir exactement ce que vous souhaitez que le client accomplisse dans la version d’évaluation et sert de référence pour toutes les questions qu’ils peuvent avoir. Le fichier doit être au format PDF et être nommé (255 caractères maximum) après le chargement.

- **Vidéos : Ajouter des vidéos** (facultatif) : Vous pouvez charger des vidéos sur YouTube ou Vimeo et les référencer via un lien et une image miniature (533 x 324 pixels), pour proposer au client un guide à suivre et l’aider à mieux comprendre la version d’évaluation, dont l’utilisation des fonctionnalités de votre offre et la compréhension des scénarios qui présentent les avantages.
  - **Nom** (obligatoire)
  - **URL (YouTube ou Vimeo uniquement)** (obligatoire)
  - **Miniature (533 x 324 px)**  : L’image doit être au format .PNG.

## <a name="supplemental-content"></a>Contenu supplémentaire

Cette page vous permet de fournir des informations supplémentaires sur votre offre pour que nous puissions la valider. Ces informations ne sont pas montrées aux clients ni publiées sur la Place de marché.

### <a name="target-release"></a>Version cible

Indiquez la version de Microsoft Dynamics Business Central ciblée par votre solution : **Actuelle**, **Majeure suivante** ou **Mineure suivante**. Ces informations nous permettent de tester votre solution de manière appropriée.

### <a name="supported-editions"></a>Éditions prises en charge

Si votre offre nécessite l’édition Premium de Microsoft Dynamics 365 Business Central, sélectionnez **Premium** uniquement. Sinon, sélectionnez à la fois **Essentials** et **Premium**.

### <a name="key-usage-scenario"></a>Principaux scénarios d’utilisation

Vous devez charger un fichier `.pdf` qui répertorie les scénarios d’utilisation principaux de votre offre dans un document (au format .pdf). Tous les scénarios répertoriés ici peuvent être vérifiés par notre équipe de validation avant que nous approuvions votre offre pour la place de marché.

### <a name="app-tests-automation"></a>Automatisation des tests d’application

Vous devez également charger un fichier d’**automatisation des tests d’application** (au format .app).

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
