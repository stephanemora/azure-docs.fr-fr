---
title: Créer une offre Dynamics 365 Business Central – Marketplace commercial de Microsoft
description: Découvrez les étapes et les considérations relatives à la création d’une offre Dynamics 365 Business Central dans la place de marché commerciale Microsoft dans l’Espace partenaires. Vous pouvez répertorier ou vendre votre offre sur la Place de marché Azure ou via le programme Fournisseur de solutions Cloud (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: 1e2af29779c93175ccc4c7f6964862b79febd8e6
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527086"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Créer une offre Dynamics 365 Business Central

Cet article explique comment créer une offre Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) est un système de planification des ressources d’entreprise (ERP) qui gère un vaste éventail de processus d’entreprise, notamment en lien avec les finances, les opérations, la chaîne logistique, la gestion de la relation client et le commerce électronique. Les packages Premium prennent également en charge le modèle de déploiement classique et la fabrication. Toutes les offres pour Dynamics 365 Business Central doivent passer par notre processus de certification.

Avant de commencer, et si vous ne l’avez pas déjà fait, [créez un compte Marketplace commercial dans l’Espace partenaires](create-account.md). Vérifiez que votre compte est inscrit dans le programme du marketplace commercial.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Marketplace commercial** > **Vue d’ensemble**.
3. Dans la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Dynamics 365 Business Central**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Après la publication d’une offre, les modifications qui lui ont été apportées dans l’Espace partenaires n’apparaissent dans les magasins en ligne qu’après republication de l’offre. Veillez à toujours effectuer une nouvelle publication après avoir apporté des modifications.

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

Répertoriez votre offre en tant qu’essai gratuit pour les clients en fournissant une URL valide (qui commence par `http` ou `https`) où ils peuvent accéder obtenir un essai.  Par exemple : `https://contoso.com/trial/my-app`. Les versions d’évaluation gratuites sont créées, gérées et configurées par votre service et n’ont pas d’abonnements gérés par Microsoft.

> [!NOTE]
> Les jetons que votre application recevra via votre lien d’évaluation peuvent uniquement être utilisés pour obtenir des informations utilisateur par le biais de Azure Active Directory (Azure AD) pour automatiser la création de comptes dans votre application. Les comptes Microsoft ne sont pas pris en charge pour l’authentification à l’aide de ce jeton.

#### <a name="contact-me"></a>Me contacter

Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle il a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Prospects](#customer-leads).

### <a name="test-drive"></a>Test drive

À ce stade, les offres Dynamics 365 Business Central ne prennent pas en charge les versions d'évaluation. Pour supprimer les versions d'évaluation de votre offre, désactivez la case à cocher **Activer une version d’évaluation**.

### <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Pour plus d’informations, consultez [Vue d’ensemble de la gestion des prospects](./commercial-marketplace-get-customer-leads.md).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="properties"></a>Propriétés

Cette page vous permet de définir les catégories et les secteurs d’activité auxquels votre offre est destinée sur la Place de marché, la version de votre application et les contrats juridiques accompagnant votre offre.

### <a name="category"></a>Category

Sélectionnez les catégories et les sous-catégories pour placer votre offre dans les zones de recherche appropriées sur la Place de marché. Veillez à décrire l’adéquation de votre offre à ces catégories dans la description de l’offre. Sélectionnez :

- Au moins une et jusqu’à deux catégories, y compris une catégorie principale et une catégorie secondaire (facultatif).
- Jusqu’à deux sous-catégories pour chaque catégorie principale et/ou secondaire. Si aucune sous-catégorie n’est applicable à votre offre, sélectionnez **Non applicable**.

Consultez la liste complète des catégories et sous-catégories dans [Bonnes pratiques pour le référencement des offres](../gtm-offer-listing-best-practices.md).

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

Voici un exemple de la façon dont les informations de l’offre s’affichent dans Microsoft AppSource (les prix listés sont fournis à titre d’exemple uniquement et ne reflètent pas les coûts réels) :

:::image type="content" source="media/example-d365-business-central.png" alt-text="Illustre la façon dont cette offre s’affiche dans Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Logo
2. Products
3. Catégories
4. Adresse du support technique (lien)
5. Conditions d’utilisation
6. Politique de confidentialité
7. Nom de l’offre
8. Résumé
9. Description
10. Captures d’écran/vidéos

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affichera auprès des clients comme titre de votre référencement d’offre. Ce champ est prérempli avec le texte que vous avez entré pour **Alias d’offre** lorsque vous avez créé l’offre, mais vous pouvez modifier cette valeur. Ce nom peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright). Le nom ne peut pas comporter plus de 50 caractères et ne peut pas contenir d’emoji.

### <a name="short-description"></a>Description courte

Fournissez une brève description de votre offre, jusqu’à 100 caractères. Cette description pourra être utilisée dans les résultats de recherche de la place de marché.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

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

Fournissez un fichier PNG pour le logo de **grande** taille. L’Espace partenaires s’en servira pour créer un logo de **petite taille**. Si vous le souhaitez, vous pouvez par la suite le remplacer par une autre image.

- **Grande taille** (de 216 x 216 à 350 x 350 px, obligatoire)
- **Petite taille** (48 x 48 px, facultatif)

Ces logos sont utilisés à différents emplacements dans la liste :

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Si vous rencontrez un problème de chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service `https://upload.xboxlive.com` utilisé par l’Espace partenaires.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez des captures d’écran qui illustrent le fonctionnement de votre offre. Au moins trois captures d’écran sont requises, et vous pouvez en ajouter jusqu’à cinq. Toutes les captures d’écran doivent être au format 1280 x 720 pixels.

#### <a name="videos"></a>Vidéos

Vous pouvez éventuellement ajouter jusqu’à cinq vidéos qui illustrent votre offre. Ces vidéos doivent être hébergées sur YouTube et/ou Vimeo. Pour chaque vidéo, entrez son nom, son URL et une image miniature correspondante (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Ressources d’annonce supplémentaires de place de marché

[Meilleures pratiques pour les annonces d’offre d’une place de marché](../gtm-offer-listing-best-practices.md)

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

Cette page vous permet de configurer une démonstration (« version d’évaluation ») qui permet aux clients d’évaluer votre offre avant de l’acheter. Pour en savoir, consultez [Qu’est-ce qu’un test drive ?](../what-is-test-drive.md).

Pour activer une version d’évaluation, cochez la case **Activer une version d’évaluation** sous l’onglet [Configuration de l’offre](#test-drive). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

Une fois que vous avez fini de configurer votre test drive, sélectionnez **Enregistrer le brouillon** avant de continuer.

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
- Envoyez l’offre pour publication en sélectionnant **Envoyer**. Nous vous enverrons un e-mail si une version d’essai de l’offre est disponible pour vérification et approbation. Revenez à l’Espace partenaires et sélectionnez **Go-Live** pour l’offre de publication sur le public.

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](./update-existing-offer.md)
