---
title: Créer une offre Dynamics 365 for Operations sur la Place de marché commerciale
description: Comment créer une nouvelle offre Dynamics 365 for Operations pour répertorier ou vendre dans la Place de marché Azure, AppSource ou via le programme de fournisseur de solutions Cloud (CSP) avec le portail de la Place de marché commerciale sur Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: df618e508590f047fb015a8be6c4934d05c04fa9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692578"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Créer une offre Dynamics 365 for Operations

Cette rubrique explique comment créer une nouvelle offre Dynamics 365 for Operations. [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) est un service de type progiciel de gestion intégré (ERP) qui gère les aspects avancés de la finance, des opérations, de la fabrication et de la gestion de la chaîne logistique. Toutes les offres pour Dynamics 365 for Operations doivent passer par notre processus de certification.

Avant de commencer, [créez un compte Place de marché commerciale dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) si vous ne l’avez pas déjà fait. Vérifiez que votre compte est inscrit dans le programme Place de marché commerciale.

>[!NOTE]
> Dès lors qu’une offre est publiée, les modifications dont elle fait l’objet dans l’Espace partenaires sont uniquement mises à jour dans le système. Elles le sont dans le store après republication. Veillez à soumettre l’offre pour publication après y avoir apporté des modifications.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Dans la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Dynamics 365 for Operations**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-dynamics-365-ops.png)

> [!NOTE]
> Après la publication d’une offre, les modifications qui lui ont été apportées dans l’Espace partenaires apparaissent uniquement dans les vitrines après la republication de l’offre. Veillez à toujours effectuer une nouvelle publication après avoir apporté des modifications.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché et des modèles Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1** ici, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.

Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-setup"></a>Configuration de l’offre

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Comment voulez-vous qu’interagissent les clients avec l’offre de liste ?

Sélectionnez l’option que vous souhaitez utiliser pour cette offre.

#### <a name="get-it-now-free"></a>Obtenez-en une maintenant (gratuit)

Répertoriez votre offre gratuitement pour les clients en fournissant une URL valide (qui commence par *http* ou *https*) où ils peuvent accéder à votre application.  Par exemple : `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Essai gratuit (annonce)

Répertoriez votre offre en tant qu’essai gratuit pour les clients en fournissant une URL valide (qui commence par *http* ou *https*) où ils peuvent accéder obtenir un essai. Par exemple : `https://contoso.com/trial/my-app`. Les versions d’évaluation gratuites sont créées, gérées et configurées par votre service et n’ont pas d’abonnements gérés par Microsoft.

> [!NOTE]
> Les jetons que votre application recevra via votre lien d’évaluation peuvent uniquement être utilisés pour obtenir des informations utilisateur par le biais de Azure Active Directory (Azure AD) pour automatiser la création de comptes dans votre application. Les comptes Microsoft ne sont pas pris en charge pour l’authentification à l’aide de ce jeton.

#### <a name="contact-me"></a>Me contacter

Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Connecter la gestion des prospects](#connect-lead-management).

### <a name="test-drive"></a>Test drive

Une version d’évaluation constitue un excellent moyen de présenter votre offre à vos clients potentiels en leur offrant la possibilité de l’essayer avant d’acheter, ce qui se traduit par une hausse du taux de conversion et par la génération de prospects de qualité. [Découvrez-en plus sur les versions d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Pour activer une version d’évaluation pour une durée déterminée, cochez la case **Activer un test drive**. Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher. Configurez l’environnement de la version d’évaluation dans la section [Configuration technique de la version d’évaluation](#test-drive-technical-configuration) plus loin dans cette rubrique.

Pour plus d’informations, consultez [Proposer une version d’évaluation de votre offre sur la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Type de version d’évaluation

Choisissez parmi les options suivantes :

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – Modèle de déploiement qui contient toutes les ressources Azure qui constituent votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure.
- **[Dynamics 365 Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft héberge et gère le service de version d’évaluation (dont le provisionnement et le déploiement) pour un système de planification des ressources d’entreprise Business Central (finances, opérations, chaîne d’approvisionnement, CRM, etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – Microsoft héberge et gère le service de version d’évaluation (dont le provisionnement et le déploiement) pour un système Customer Engagement (ventes, service, service de projet, service après-vente, etc.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – Microsoft héberge et gère le service de version d’évaluation (dont le provisionnement et le déploiement) pour un système de planification des ressources d’entreprise Finances et opérations (finances, opérations, fabrication, chaîne d’approvisionnement, etc.).
- **[Application logique](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – Modèle de déploiement englobant toutes les architectures de solutions complexes. Tous les produits personnalisés doivent tous utiliser ce type de version d’évaluation.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – Lien hypertexte vers un tableau de bord personnalisé. Les produits dotés d’un visuel Power BI interactif doivent utiliser ce type de version d’évaluation. Il vous suffit de charger votre URL Power BI incorporée.

#### <a name="additional-test-drive-resources"></a>Ressources supplémentaires pour version d’évaluation

- [Bonnes pratiques marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Meilleures pratiques techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Vue d’ensemble](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF ; veillez à désactiver votre bloqueur de fenêtres publicitaires)

## <a name="connect-lead-management"></a>Connecter la gestion des prospects

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Pour plus d’informations, consultez [Vue d’ensemble de la gestion des prospects](./commercial-marketplace-get-customer-leads.md).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="properties"></a>Propriétés

Cette page vous permet de définir les catégories et les secteurs d’activité auxquels votre offre est destinée sur la Place de marché, la version de votre application et les contrats juridiques accompagnant votre offre.

### <a name="category"></a>Category

Sélectionnez au minimum une et au maximum trois catégories. Ces catégories serviront à placer votre offre dans les zones de recherche appropriées sur la place de marché. Veillez à expliquer dans la description comment votre offre prend en charge ces catégories dans la description de l’offre.

### <a name="industry"></a>Secteur d’activité

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Version de l’application

Entrez le numéro de version de votre offre. Cette version est indiquée aux clients sur la page de détails de l’offre.

### <a name="terms-and-conditions"></a>Conditions générales

Fournissez vos propres conditions légales dans le champ **Conditions générales**. Vous pouvez également fournir l’URL permettant d’accéder à vos conditions générales. Les clients doivent accepter ces conditions avant de pouvoir essayer votre offre.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="offer-listing"></a>Annonce de l’offre

Cette page présente les langues dans lesquelles votre offre sera listée. Actuellement, **Anglais (États-Unis)** est la seule option disponible.

Vous devez définir les informations de la Place de marché (nom de l’offre, description, images, etc.) pour chaque langue/marché. Sélectionnez le nom de la langue/du marché pour fournir ces informations.

> [!NOTE]
> Il n’est pas nécessaire que le contenu du référencement de l’offre (par exemple, la description, les documents, les captures d’écran, les conditions d’utilisation, etc.) soit en anglais, tant que sa description commence par l’expression : « This application is available only in [langue autre que l’anglais]. » Il est également possible de fournir une *URL de lien utile* pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de la liste des offres.

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affichera auprès des clients comme titre de votre référencement d’offre. Ce champ est prérempli avec le texte que vous avez entré pour **Alias d’offre** lorsque vous avez créé l’offre, mais vous pouvez modifier cette valeur. Ce nom peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright). Le nom ne peut pas comporter plus de 50 caractères et ne peut pas contenir d’emoji.

### <a name="short-description"></a>Description courte

Fournissez une brève description de votre offre (jusqu’à 100 caractères). Cette description pourra être utilisée dans les résultats de recherche de la place de marché.

### <a name="description"></a>Description

Fournissez une description plus longue de votre offre (jusqu’à 3 000 caractères). Cette description s’affichera pour les clients dans la vue d’ensemble de la place de marché. Incluez la proposition de valeur de votre offre, les principaux avantages, les associations de catégorie et/ou de secteur d’activité, les opportunités d’achat dans l’application et toutes les informations requises.

Quelques conseils pour rédiger votre description :

- Décrivez clairement la proposition de valeur de votre offre dès les premières phrases de votre description. Incluez les informations suivantes dans votre proposition de valeur :
  - Description du produit
  - Type d’utilisateur qui tire parti du produit
  - Les clients ont des besoins ou des problèmes auxquels le produit répond
- Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats de recherche.  
- Ne comptez pas sur les fonctionnalités de votre produit pour convaincre. Concentrez-vous plutôt sur ce que votre application peut apporter aux clients.  
- Autant que possible, utilisez le vocabulaire propre au secteur visé, ainsi que des termes décrivant les avantages proposés.

Pour que la description de votre offre soit plus accrocheuse, utilisez l’éditeur de texte enrichi pour appliquer la mise en forme.

![Utilisation de l’éditeur de texte enrichi](./media/rich-text-editor.png)

| <center>Changer la mise en forme du texte | <center>Ajouter des puces ou une numérotation | <center>Ajouter ou supprimer une mise en retrait de texte |
| --- | --- | --- |
| <center>![Changer la mise en forme du texte à l’aide de l’éditeur de texte enrichi](./media/text-editor3.png) |  <center>![Ajouter des listes à l’aide de l’éditeur de texte enrichi](./media/text-editor4.png) |  <center>![Mettre du texte en retrait à l’aide de l’éditeur de texte enrichi](./media/text-editor5.png) |

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

Dans cette section, indiquez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact de support** et d’un **contact d’ingénierie**. Ces informations ne sont pas présentées aux clients, mais seront disponibles pour Microsoft et pourront être fournies aux partenaires CSP.

Dans la section **Contact de support**, indiquez **l’URL du support** où les partenaires CSP peuvent trouver l’assistance pour votre offre.

### <a name="supporting-documents"></a>Documents de soutien

Fournissez au moins un (et au plus trois) documents marketing associés ici, comme des livres blancs, des brochures, des listes de contrôle ou des présentations. Ces documents doivent être au format .PDF.

### <a name="marketplace-images"></a>Images de la Place de marché

Dans cette section, vous pouvez fournir des logos et des images qui seront utilisés pour montrer votre offre au client. Toutes les images doivent être au format .png.

>[!Note]
>Si vous rencontrez un problème de chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

#### <a name="store-logos"></a>Stocker des logos

Fournissez le logo de votre offre en deux tailles (en pixels) :

- **Petit** (48 x 48)
- **Grand** (216 x 216)

#### <a name="hero"></a>Bannière

L’image de héros est facultative. Si vous en fournissez une, elle doit être au format 815 x 290 pixels.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez des captures d’écran qui illustrent le fonctionnement de votre offre. Au moins une capture d’écran est requise, et vous pouvez en ajouter jusqu’à cinq. Toutes les captures d’écran doivent être au format 1280 x 720 pixels.

#### <a name="videos"></a>Videos

Vous pouvez éventuellement ajouter jusqu’à quatre vidéos qui illustrent votre offre. Ces vidéos doivent être hébergées sur YouTube et/ou Vimeo. Pour chacune d’entre elles, entrez le nom de la vidéo, son URL et une image miniature de la vidéo (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

[Meilleures pratiques pour les annonces d’offre d’une place de marché](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="availability"></a>Disponibilité

Cette page vous offre des options pour déterminer où et comment rendre votre offre disponible.

### <a name="markets"></a>Marchés

Cette section vous permet de spécifier les marchés dans lesquels votre offre doit être disponible. Pour ce faire, sélectionnez **Edit markets** (Modifier les marchés), ce qui affiche la fenêtre contextuelle **Sélection du marché**.

Par défaut, aucun marché n’est sélectionné. Sélectionnez au moins un marché pour publier votre offre. Cliquez sur **Sélectionner tout** pour rendre votre offre disponible sur tous les marchés possibles ou sélectionnez les marchés spécifiques que vous souhaitez ajouter. Quand vous avez terminé, sélectionnez **Enregistrer**.

Vos sélections ici s’appliquent uniquement aux nouvelles acquisitions. Si quelqu’un a déjà votre application sur un marché donné et que vous supprimez par la suite ce marché, les personnes qui disposent déjà de cette offre peuvent continuer à l’utiliser, mais aucun nouveau client sur ce marché ne pourra bénéficier de votre offre.

> [!IMPORTANT]
> Il vous incombe de respecter les exigences légales locales, même si ces exigences ne sont pas répertoriées ici ou dans l’Espace partenaires.

Gardez à l’esprit que même si vous sélectionnez tous les marchés, les lois et restrictions locales ou d’autres facteurs peuvent empêcher certaines offres d’être répertoriées dans certains pays et régions.

### <a name="preview-audience"></a>Public de la version préliminaire

Avant de publier votre offre sur la place de marché générale, vous devez d’abord la mettre à disposition d’un **public limité en préversion**. Entrez une **Clé de masquage** (n’importe quelle chaîne utilisant uniquement des lettres minuscules et/ou des chiffres) ici. Les membres de votre public en préversion peuvent utiliser cette clé de masquage comme jeton pour afficher un aperçu de votre offre sur la place de marché.

Ensuite, lorsque vous êtes prêt à mettre votre offre à disposition générale et supprimer la restriction de la préversion, vous devez supprimer la **clé de masquage** et publier à nouveau.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="technical-configuration"></a>Configuration technique

Cette page définit les détails techniques utilisés pour se connecter à votre offre. Cette connexion permet d’approvisionner votre offre pour le client final, s’il choisit de l’acquérir.

### <a name="solution-identifier"></a>Identificateur de solution

Fournissez l’identificateur de solution (GUID) de votre solution.

Pour trouver l’identificateur de votre solution :
1. Dans les services de cycle de vie Microsoft Dynamics (LCS), sélectionnez **Gestion de la solution**.
2. Sélectionnez votre solution, puis recherchez **l'identificateur de solution** dans la **vue d’ensemble du package**. Si l’identificateur est vide, sélectionnez **Modifier** et republiez votre package, puis réessayez.

### <a name="release-version"></a>Version commerciale

Sélectionnez la version de Dynamics 365 for Finance and Operations avec lesquelles cette solution fonctionne.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="test-drive-technical-configuration"></a>Configuration technique de la version d’évaluation

Cette page vous permet de configurer une démonstration (« version d’évaluation ») qui permet aux clients d’évaluer votre offre avant de l’acheter. Pour en savoir, consultez l’article [Qu’est-ce qu’une version d’évaluation ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Pour activer une version d’évaluation, cochez la case **Activer un test drive** sous l’onglet [Configuration de l’offre](#test-drive). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

Les types de versions d’évaluation suivants sont disponibles, chacun avec leurs propres configurations techniques requises.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Application logique](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configuration technique non requise)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuration technique pour la version d’évaluation d’Azure Resource Manager

Un modèle de déploiement qui contient toutes les ressources Azure comprenant votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure. En savoir plus sur la configuration d’une [version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Régions** (obligatoire) – À l’heure actuelle, vous pouvez mettre votre version d’évaluation à disposition dans 26 régions Azure. En règle générale, vous devez rendre votre version d’évaluation disponible dans les régions où vous prévoyez le plus grand nombre de clients, de sorte qu’ils puissent sélectionner la région la plus proche pour des performances optimales. Vous devez vous assurer que votre abonnement a l’autorisation de déployer toutes les ressources nécessaires dans chacune des régions sélectionnées.

- **Instances** – Sélectionnez le type (chaud ou froid) et le nombre d’instances disponibles, qui seront multipliés par le nombre de régions où votre offre sera disponible.

    **Chaud** – Ce type d’instance est déployé et en attente de l’accès par région sélectionnée. Les clients peuvent accéder instantanément aux instances *à chaud* d’une version d’évaluation au lieu d’attendre un déploiement. L’inconvénient est que ces instances sont toujours en cours d’exécution sur votre abonnement Azure, entraînant des coûts de fonctionnement plus importants. Il est vivement recommandé d’avoir au moins une instance *à chaud*, étant donné que la plupart de vos clients ne souhaitent pas attendre la fin des déploiements complets, et il y a donc une chute dans l’utilisation du client si aucune instance *à chaud* n’est disponible.

    **Froid** – Ce type d’instance représente le nombre total d’instances pouvant éventuellement être déployées par région. Les instances à froid nécessitent le modèle Resource Manager entier de la version d’évaluation pour être déployées lorsqu’un client demande la version d’évaluation, les instances *à froid* sont donc plus lentes à charger que les instances *à chaud*. L’inconvénient est que vous avez uniquement à payer pour la durée de la version d’évaluation, elle n’est *pas* toujours en cours d’exécution sur votre abonnement Azure comme c’est le cas avec une instance *à chaud*.

- **Version d’évaluation du modèle Azure Resource Manager** – Chargez le fichier .zip contenant votre modèle Azure Resource Manager.  Apprenez-en plus sur la création d’un modèle Azure Resource Manager dans l’article de démarrage rapide [Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durée de la version d’évaluation** (obligatoire) – Entrez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période. Vous ne pouvez définir la durée qu’en nombre d’heures complètes (par exemple, « 2 » pour 2 heures, « 1,5 » est incorrect).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuration technique pour la version d’évaluation de Dynamics 365

Microsoft peut retirer la complexité de la configuration d’une version d’évaluation en hébergeant et en conservant l’approvisionnement et le déploiement du service à l’aide de ce type de version d’évaluation. La configuration de ce type de version d’évaluation hébergé est la même, peu importe si la version d’évaluation cible un public Business Central, Customer Engagement ou Operations.

- **Nombre maximal de versions d’évaluation simultanées** (obligatoire) – Définissez le nombre maximum de clients pouvant utiliser votre version d’évaluation simultanément. Chaque utilisateur simultané consommera une licence Dynamics 365 tant que la version d’évaluation est active. Veillez donc à disposer de suffisamment de licences pour prendre en charge la limite maximale définie. La valeur recommandée est de 3 à 5.

- **Durée de la version d’évaluation** (obligatoire) – Entrez la durée d’activité de la version d’évaluation en définissant le nombre d’heures. Passée cette durée, la session prendra fin et aucune de vos licences ne sera plus utilisée. La valeur recommandée est de 2 à 24 heures, selon la complexité de votre offre. Vous ne pouvez définir la durée qu’en nombre d’heures complètes (par exemple, « 2 » pour 2 heures, « 1,5 » est incorrect).  L’utilisateur peut demander une nouvelle session s’il n’a pas eu assez de temps et s’il souhaite de nouveau accéder à la version d’évaluation.

- **URL de l’instance** (obligatoire) – URL où le client commencera son évaluation. Il s’agit généralement de l’URL de votre instance Dynamics 365 exécutant votre application et où les exemples de données sont installés (par exemple `https://testdrive.crm.dynamics.com`).

- **URL de l’API web de l’instance**(obligatoire) – Récupérez l’URL de l’API web pour votre instance Dynamics 365 en vous connectant à votre compte Microsoft 365 et en accédant à **Paramètres** \&gt; **Personnalisation** \&gt; **Ressources développeur** \&gt; **API web de l’instance (URL racine du service)** , puis copiez l’URL trouvée ici (par exemple `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Nom du rôle** (obligatoire) – Indiquez le nom du rôle de sécurité que vous avez défini dans votre version d’évaluation personnalisée Dynamics 365. Il sera attribué à l’utilisateur lors de la version d’évaluation (par exemple, test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuration technique pour la version d’évaluation d’applications logiques

Tous les produits personnalisés doivent utiliser ce type de modèle de déploiement de version d’évaluation, qui englobe une variété d’architectures de solution complexes. Pour plus d’informations sur la configuration de versions d’évaluation d’applications logiques, consultez [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) et [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) sur GitHub.

- **Régions** (obligatoire, liste déroulante à sélection unique) – À l’heure actuelle, vous pouvez mettre votre version d’évaluation à disposition dans 26 régions Azure. Les ressources pour votre application logique seront déployées dans la région que vous sélectionnez. Si votre application logique compte des ressources personnalisées stockées dans une région spécifique, assurez-vous que celle-ci est sélectionnée ici. La meilleure façon de s’assurer que des ressources personnalisées sont disponibles pour votre région consiste à déployer entièrement votre application logique localement vers votre abonnement Azure dans le portail et à vérifier qu’elle fonctionne correctement avant de la sélectionner.

- **Nombre maximal de versions d’évaluation simultanées** (obligatoire) – Définissez le nombre maximum de clients pouvant utiliser votre version d’évaluation simultanément. Ces versions d’évaluation sont déjà déployées, permettant ainsi aux clients d’y accéder instantanément sans attendre un déploiement.

- **Durée de la version d’évaluation** (obligatoire) – Entrez la durée d’activité de la version d’évaluation, en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période.

- **Nom du groupe de ressources Azure** (obligatoire) – Entrez le nom du [Groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) dans lequel votre version d’évaluation d’application logique est enregistrée.

- **Nom de l’application logique Azure** (obligatoire) – Entrez le nom de l’application logique qui affecte la version d’évaluation à l’utilisateur. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

- **Déprovisionner le nom d’application logique** (obligatoire) – Entrez le nom de l’application logique qui déprovisionne la version d’évaluation une fois que le client a terminé. Cette application logique doit être enregistrée dans le groupe de ressources Azure ci-dessus.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuration technique non requise pour les versions d’évaluation de Power BI

Les produits souhaitant afficher un visuel interactif Power BI peuvent utiliser un lien hypertexte pour partager un tableau de bord personnalisé et intégré qui sert de version d’évaluation, sans configuration technique supplémentaire. En savoir plus sur la configuration d’applications de modèle [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview).

### <a name="deployment-subscription-details"></a>Détails de l’abonnement de déploiement

Pour déployer la version d’évaluation de votre côté, créez et fournissez un abonnement Azure séparé et unique. (Non obligatoire pour les versions d’évaluation Power BI).

- **ID d’abonnement Azure** (obligatoire pour Azure Resource Manager et les applications logiques) – Entrez l’ID d’abonnement pour accorder l’accès aux services de votre compte Azure pour les rapports d’utilisation des ressources et la facturation. Nous vous recommandons d’envisager la [création d’un abonnement Azure distinct](https://docs.microsoft.com/azure/billing/billing-create-subscription) à utiliser pour les versions d’évaluation si vous n’en avez pas déjà. Pour trouver vos ID d’abonnement Azure en vous connectant au [portail Azure](https://portal.azure.com/) et en accédant à l’onglet **Abonnements** dans le menu de gauche. Sélectionnez l’onglet pour afficher votre ID d’abonnement (par exemple, « a83645ac-1234-5ab6-6789-1h234g764ghty »).

- **ID de locataire Azure AD** (obligatoire) – Entrez votre [ID de locataire](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory (AD). Pour trouver cet ID, connectez-vous au [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, sélectionnez **Propriétés**, puis cherchez l’**ID du répertoire** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e). Vous pouvez aussi chercher l’ID de locataire de votre organisation à partir de l’adresse de votre nom de domaine à l’adresse [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nom de locataire Azure AD** (obligatoire pour Dynamic 365) – Entrez votre nom Azure Active Directory (AD). Pour rechercher ce nom, connectez-vous au [portail Azure](https://portal.azure.com/), et dans le coin supérieur droit, le nom de votre locataire s’affichera sous le nom de votre compte.

- **ID d’application Azure AD** (obligatoire) – Entrez votre [ID d’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Azure Active Directory (AD). Pour trouver cet ID, connectez-vous sur le [Portail Azure](https://portal.azure.com/), sélectionnez l’onglet Active Directory dans le menu de gauche, puis **Inscriptions d’applications**, cherchez le numéro de l’**ID de l’application** (par exemple, 50c464d3-4930-494c-963c-1e951d15360e).

- **Clé secrète client Azure AD** (obligatoire) – Entrez la [clé secrète client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) de votre application Azure AD. Pour trouver cette valeur, connectez-vous au [portail Azure](https://portal.azure.com/). Dans le volet de navigation gauche, sélectionnez l’onglet **Azure Active Directory**,puis **Inscriptions d’applications**, et enfin votre application de version d’évaluation. Ensuite, sélectionnez **Certificats et clés secrètes**, **Nouvelle clé secrète client**, entrez une description, sélectionnez **Jamais** sous **Expiration** puis choisissez **Ajouter**. Veillez à copier la valeur. (Ne quittez pas la page avant d’avoir noté la valeur, vous ne pourriez alors plus obtenir la valeur.)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="test-drive-marketplace-listings"></a>Référencements de versions d'évaluation sur la place de marché

L'option **Référencement sur la place de marché** située sous l’onglet **Version d'évaluation** affiche les langues dans lesquelles votre version d'évaluation est disponible. Actuellement, **Anglais (États-Unis)** est le seul emplacement disponible. Sélectionnez le nom de la langue pour entrer des informations qui décrivent l’expérience de la version d'évaluation.

- **Description** (obligatoire) – Décrivez votre version d’évaluation, ce qui sera présenté, les objectifs que l’utilisateur pourra essayer, les fonctionnalités à explorer et toute autre information qui aidera l’utilisateur à se décider d’acheter ou non votre offre. Jusqu’à 3 000 caractères peuvent être entrés dans ce champ.

- **Informations d’accès** (obligatoire pour les versions d’évaluation Azure Resource Manager et logiques) – Expliquez ce qu’un client doit savoir pour accéder et utiliser la version d’évaluation en question. Détaillez un scénario d’utilisation de votre offre ainsi que tout ce que le client doit savoir pour accéder aux fonctionnalités disponibles dans la version d’évaluation. Jusqu’à 10 000 caractères peuvent être entrés dans ce champ.

- **Manuel de l’utilisateur** (obligatoire) – Exploration approfondie de l’expérience de votre version d’évaluation. Le manuel de l’utilisateur doit couvrir exactement ce que vous souhaitez que le client accomplisse dans la version d’évaluation et sert de référence pour toutes les questions qu’ils peuvent avoir. Le fichier doit être au format PDF et être nommé (255 caractères maximum) après le chargement.

- **Vidéos** (facultatif) – Vous pouvez charger des vidéos sur YouTube ou Vimeo et les référencer ici via un lien et une image miniature (533 x 324 pixels) pour fournir au client des informations complètes qui l’aideront à mieux comprendre la version d’évaluation, notamment la façon d’exploiter les fonctionnalités de l’offre et de comprendre les scénarios qui font ressortir ses avantages.
  - **Nom** (obligatoire)
  - **URL** (obligatoire ; YouTube ou Vimeo uniquement)
  - **Miniature** (cette image doit être au format PNG et 533 x 324 pixels)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="supplemental-content"></a>Contenu supplémentaire

Cette page vous permet de fournir des informations supplémentaires sur votre offre pour que nous puissions la valider. Ces informations ne sont pas montrées aux clients ni publiées sur la Place de marché.

### <a name="validation-assets"></a>Ressources de validation

Chargez un [rapport d’analyse de la personnalisation (CAR)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) dans cette section. Ce rapport est généré en analysant vos modèles de personnalisation et d’extension, en fonction d’un ensemble prédéfini de règles de meilleures pratiques.

Ce fichier doit être au format .xls ou. xlsx. Si vous avez plus d’un rapport, vous pouvez télécharger un fichier .zip contenant tous les rapports.

### <a name="does-solution-include-localizations"></a>La solution comprend-elle des localisations ?

Sélectionnez **Oui** si la solution permet l’utilisation de stratégies et normes locales (par exemple, si elle prend en charge les différentes règles salariales requises par les différents pays/régions). Sinon, sélectionnez **Non**.

### <a name="does-solution-enable-translations"></a>La solution permet-elle les traductions ?

Répondez **Oui** si le texte de votre solution peut être traduit dans d’autres langues. Sinon, sélectionnez **Non**.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="publish"></a>Publish

### <a name="submit-offer-to-preview"></a>Soumettre l’offre en préversion

Une fois que vous avez complété toutes les sections obligatoires de l’offre, sélectionnez **Vérifier et publier** en haut à droite du portail.

S’il s’agit de votre première publication de cette offre, vous pouvez :

- Consultez l’état d’achèvement de chaque section de l’offre.
    - **Non démarrée** – La section n’a pas encore été modifiée et doit être complétée.
    - **Incomplète** – La section comporte des erreurs qui doivent être corrigées ou nécessite un complément d’informations. Revenir à la ou les sections et les mettre à jour.
    - **Complète** – La section est complète, toutes les données obligatoires ont été fournies et aucune erreur n’est présente. Toutes les sections de l’offre doivent être dans l’état Complète avant de pouvoir envoyer l’offre.
- Dans la sections **Notes pour la certification**, fournissez des instructions de test à l’équipe de certification pour veiller à ce que votre application soit testée correctement, en plus de toutes autres notes supplémentaires utiles pour comprendre l’application.
- Envoyez l’offre pour publication en sélectionnant **Envoyer**. Nous vous enverrons un e-mail pour vous faire savoir si une version d’essai de l’offre est disponible pour vérification et approbation. Retourner dans Partner Center et sélectionner **Publier** pour que votre offre soit publiée pour le public (ou au public privé, si l’offre est privée).

## <a name="next-step"></a>Étape suivante

- [Mettre à jour une offre existante dans la Place de marché commerciale](./update-existing-offer.md)
