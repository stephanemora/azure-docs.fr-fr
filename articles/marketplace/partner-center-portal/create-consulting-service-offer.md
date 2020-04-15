---
title: Créer une offre de services de conseil dans l’Espace partenaires - Place de marché Azure
description: Découvrez comment publier une offre de services de conseil sur la Place de marché Azure ou AppSource à l’aide de l’Espace partenaires.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: f1354ce824bc4e57ab55846c714d4a0a73a46966
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873818"
---
# <a name="create-a-consulting-service-offer"></a>Créer une offre de services de conseil

> [!IMPORTANT]
> Nous allons déplacer la gestion de vos offres de services de conseil du portail Cloud Partner vers l’Espace partenaires. Tant que vos offres ne sont pas migrées, suivez les instructions dans [Offre de service de conseil Azure et Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) pour que le portail Cloud Partner gère vos offres.

Cet article décrit comment publier une offre de service de conseil sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/) ou sur [AppSource](https://appsource.microsoft.com/). Répertoriez les offres de services de conseil basées sur Microsoft [Dynamics 365](https://dynamics.microsoft.com/) et Power Platform sur AppSource. Répertoriez les offres de services de consulting basées sur Microsoft Azure sur la Place de marché Azure.

Pour créer une offre de services de conseil sur la Place de marché Azure ou AppSource, vous devez d’abord [avoir un compte d’éditeur dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), et votre compte doit être inscrit dans le programme de la Place de marché commerciale. Avant de créer votre offre, passez en revue les conditions préalables dans [Conditions préalables à la publication d’un service de conseil](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Avantages liés à la publication

Avantages de la publication sur la Place de marché commerciale :

- Promouvez votre entreprise en utilisant la marque Microsoft.
- Touchez potentiellement plus de 100 millions d'utilisateurs Office 365 et Dynamics 365 sur AppSource, ainsi que plus de 200 000 organisations via la Place de marché Microsoft Azure.
- Recevez des prospects de grande qualité à partir de ces places de marché.
- Bénéficiez d’une promotion de vos services assurée par les équipes de terrain et de télévente de Microsoft

## <a name="create-a-new-offer"></a>Créer une offre

Une fois que vous avez rempli les conditions ci-dessus, suivez les étapes décrites ci-après pour créer une offre de services de conseil.

1. Connectez-vous à [l’Espace partenaires](https://partner.microsoft.com), puis sélectionnez **Tableau de bord** dans le menu supérieur.
2. Dans la barre de navigation de gauche, sélectionnez **Place de marché commerciale**, puis sélectionnez **Vue d’ensemble**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Illustre le menu de la Place de marché commerciale":::

3. Sélectionnez **+ Nouvelle offre**, puis **Service de conseil**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Illustre le bouton permettant de créer une nouvelle offre.":::

4. Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

    - Cet ID est visible pour les clients dans l’adresse web de l’offre de la Place de marché.
    - Utilisez uniquement des lettres minuscules, des chiffres, des tirets et des traits de soulignement, mais pas d’espaces. La longueur du Luid est limitée à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’URL de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
    - L’ID d’offre ne peut pas être modifié une fois que vous avez sélectionné **Créer**.

5. Entrez un **Alias d’offre**. Le nom utilisé pour faire référence à l’offre dans l’Espace partenaires.

    - Ce nom n’est pas utilisé dans la Place de marché. Il est différent du nom de l’offre et des autres valeurs présentées aux clients. Vous pouvez utiliser ce champ pour attribuer un nom à l’offre plus utile pour identifier l’offre en interne ; cette valeur n’est pas présentée aux clients.
    - L’alias d’offre ne peut pas être modifié une fois que vous avez sélectionné **Créer**.

Après avoir entré ces deux valeurs, sélectionnez **Créer** pour accéder à la page **Configuration de l’offre**.

## <a name="offer-setup"></a>Configuration de l’offre

Une fois que vous avez entré un ID d’offre et un alias d’offre, l’Espace partenaires crée une offre temporaire et affiche la page **Configuration de l’offre**. Effectuez les étapes suivantes pour configurer votre offre.

### <a name="connect-lead-management"></a>Connecter la gestion des prospects

Lors de la publication de votre offre sur la Place de marché avec l’Espace partenaires, vous _devez_ la connecter à un système de gestion des relations avec la clientèle (CRM) ou à un système d’automatisation marketing. Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit.

1. Sélectionnez **Connecter** pour spécifier l’emplacement où vous souhaitez que nous puissions envoyer les prospects. L’Espace partenaires prend en charge les systèmes suivants :

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Si votre système de CRM n’est pas répertorié ci-dessus, utilisez une [table Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou un [point de terminaison HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) pour stocker les données des prospects, puis exporter les données vers votre système CRM.

2. Connectez votre offre à la destination de prospect tout en la publiant dans l'Espace partenaires.
3. Vérifiez que la connexion à la destination de prospect est correctement configurée. Une fois que vous avez effectué la publication dans l'Espace partenaires, nous validons la connexion et nous vous envoyons un prospect de test. Lorsque vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acheter vous-même l’offre dans l’environnement en version préliminaire.
4. Assurez-vous que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

Voici des ressources de gestion des prospects supplémentaires :

- [Vue d’ensemble de la gestion des prospects](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Questions fréquentes sur la gestion des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erreurs courantes de configuration des prospects](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- PDF [Vue d’ensemble de la gestion des prospects](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (Assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé)

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Propriétés.

### <a name="properties"></a>Propriétés

Cette page vous permet de définir le produit principal que votre offre de services de conseil couvre le mieux, de définir un type de services de conseil et de choisir les produits applicables.

1. Sélectionnez un **Produit principal** dans la liste déroulante.
2. Sélectionnez un **Type de services de conseil** dans la liste déroulante :

    - **Évaluation** : évaluation de l’environnement d’un client pour déterminer l’applicabilité d’une solution et donner une estimation du coût et de la durée.
    - **Réunion d’information** : Présentation d’une solution ou d’un service de conseil pour susciter l’intérêt du client à l’aide de frameworks, de démonstrations et d’exemples de clients.
    - **Implémentation** : installation complète qui résulte en une solution entièrement fonctionnelle. Limitez-vous à des solutions qui peuvent être implémentées en deux semaines ou moins.
    - **Preuve de concept** : implémentation de portée limitée pour déterminer si une solution répond aux exigences du client.
    - **Atelier** : engagement interactif réalisé dans les locaux du client. Il peut inclure une formation, des réunions d’information, des évaluations ou des démonstrations reposant sur les données ou l’environnement du client.

1. Si vous avez sélectionné un produit principal **Azure**, sélectionnez jusqu’à trois **Zones de solution**. Elles permettent aux clients de la place de marché Azure de trouver votre offre plus facilement. Si vous n'avez pas choisi Azure, ignorez cette étape.
2. Si vous avez sélectionné un produit principal _autre_ qu’Azure, sélectionnez jusqu’à trois **Produits applicables**. Elles permettent aux clients d’AppSource de trouver votre offre plus facilement. Pour plus de détails, consultez [Microsoft AppSource consulting services listing guidelines](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (Instructions de référencement des services de conseil Microsoft AppSource - PDF).
3. Sélectionnez jusqu’à six **Secteurs d’activité** auxquels s’applique votre offre. Cela facilitera la recherche de votre offre par les clients.
4. Ajoutez jusqu’à trois **Compétences** que votre entreprise a obtenu le droit d’afficher sur l’annonce de votre offre de services de conseil. Au moins une compétence est requise, sauf pour Azure Expert MSP et les Azure Networking MSP.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Annonce de l’offre.

## <a name="offer-listing"></a>Annonce de l’offre

Ici, vous allez définir les détails de l’offre qui s’affichent dans la Place de marché. Cela comprend le nom, la description, les images, etc. de l’offre. Veillez à suivre les stratégies détaillées sur la [page de stratégie Microsoft](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) lors de la configuration de cette offre.

> [!NOTE]
> Les détails de l’offre n’ont pas à être en anglais si la description de l’offre commence par l’expression, &quot;This application is available only in [langue non anglaise].&quot; Il est également possible de fournir une URL de lien utile pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de l’annonce de l’offre.

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affiche comme titre de votre offre. Ce champ est prérempli avec le texte que vous avez entré dans la zone **Alias d’offre** lors de la création de l’offre. Vous pouvez modifier ce nom ultérieurement.

Le nom :

- Peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright).
- Ne peut pas compter plus de 50 caractères.
- Ne peut pas inclure d’emojis.

### <a name="search-results-summary"></a>Résumé des résultats de recherche

Fournissez une brève description de votre offre. Ce champ peut comporter jusqu’à 100 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

### <a name="description"></a>Description

Fournissez une description plus longue de votre offre, jusqu’à 3 000 caractères. S’affichera pour les clients dans la vue d’ensemble de la place de marché.

Incluez un ou plusieurs des éléments suivants dans votre description :

- La valeur et les avantages principaux de votre offre
- Catégorie ou associations de secteurs, ou les deux
- Opportunités d’achats dans l’application
- Toutes les informations requises

Voici quelques conseils pour rédiger votre description :

- Décrivez clairement la valeur de votre offre dans les premières phrases de votre description. Incorporez les éléments suivants :
  - Description de l’offre.
  - Le type d’utilisateur qui tire parti de l’offre.
  - Les besoins ou problèmes auxquels l’offre répond.
- Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats de recherche.
- Ne comptez pas sur les fonctionnalités de votre produit pour convaincre. Concentrez-vous plutôt sur ce que votre application offre.
- Essayez d’utiliser le vocabulaire propre au secteur visé, ainsi que des termes décrivant les avantages proposés.

Pour que la description offre soit plus attrayante, utilisez l’éditeur de texte enrichi pour mettre en forme votre description. L’éditeur de texte enrichi vous permet d’ajouter des chiffres, des puces, des caractères gras/en italique et des mises en retrait pour rendre votre description plus lisible.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Illustre l’éditeur de texte enrichi pour écrire la description de l’offre." border="false":::

### <a name="keywords"></a>Mots clés

Entrez jusqu’à trois mots-clés de recherche pertinents pour votre produit principal et votre service de conseil. Ils faciliteront la recherche de votre offre.

### <a name="duration"></a>Duration

Définissez la durée attendue de cet engagement avec votre client.

### <a name="contact-information"></a>Informations de contact

Vous devez fournir le nom, l’adresse e-mail et le numéro de téléphone d’un contact **principal** et d’un contact **secondaire**. Ces informations ne sont pas présentées aux clients. Elles sont disponibles pour Microsoft et peuvent être fournies aux partenaires fournisseurs de solutions Cloud (CSP).

### <a name="supporting-documents"></a>Documents de soutien

Ajoutez jusqu’à trois (mais au moins un) documents PDF venant appuyer votre offre.

### <a name="marketplace-images"></a>Images de la Place de marché

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format .png. Les images floues seront rejetées.

#### <a name="store-logos"></a>Stocker des logos

Fournissez des fichiers .png du logo de votre offre dans chacune des tailles en pixels suivantes :

- **Petit (48 x 48)**
- **Grand (216 x 216)**

Tous les logos sont requis et sont utilisés à différents endroits de l’annonce de la Place de marché.

#### <a name="screenshots-optional"></a>Captures d’écran (facultatif)

Ajoutez jusqu’à cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chacune doit avoir une taille de 1280 x 720 pixels et être au format .png.

#### <a name="videos-optional"></a>Vidéos (facultatif)

Ajoutez jusqu’à quatre vidéos qui illustrent votre offre. Entrez le nom de la vidéo, son adresse web (URL) et une image miniature .png de 1280 x 720 pixels de la vidéo.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Tarification et disponibilité.

## <a name="pricing-and-availability"></a>Tarification et disponibilité

Ici, vous allez définir des éléments tels que la tarification, le marché et une clé privée.

1. **Marché** : Définissez le marché dans lequel votre offre sera disponible. Vous ne pouvez sélectionner qu’un seul marché par offre.
    1. Pour les marchés États-Unis et Canada, sélectionnez **Modifier les États** (ou **provinces**) pour spécifier les emplacements auxquels votre offre sera disponible.
2. **Public de la version préliminaire** : Configurez la **Clé de masquage** utilisée pour définir le public privé de votre offre.
3. **Prix** : Indiquez si votre offre est une offre **Gratuite** ou **Payante**.

    > [!NOTE]
    > Les offres de services de conseil ne couvrent que l’annonce. Toutes les transactions se produisent directement, en dehors de la Place de marché commerciale.

4. Pour une offre payante, spécifiez les paramètres **Prix et devise** et indiquez si le prix est **Fixe** ou si c’est une **Estimation**. Si c’est une Estimation, vous devez spécifier dans la description les facteurs qui auront une incidence sur le prix.
5. Sélectionnez **Enregistrer le brouillon**.

## <a name="review-and-publish"></a>Passer en revue et publier

Une fois que vous avez rempli toutes les sections requises de l’offre, vous pouvez envoyer votre pour révision et publication.

1. Lorsque vous êtes prêt à publier votre offre de services de conseil, cliquez sur **Passer en revue et publier**.
2. Passez en revue les détails sur la page d’envoi finale.
3. Si nécessaire, écrivez une note à l’équipe de certification si vous pensez que les détails de votre offre nécessitent une explication.
4. Quand vous êtes prêt, sélectionnez **Envoyer**.
5. La page **Vue d’ensemble de l’offre** affiche l’étape de publication actuelle de votre offre.

Pour plus d’informations sur la durée pendant laquelle vous pouvez vous attendre à ce que votre offre reste dans chaque étape de publication, consultez [Vérifier l’état de publication de votre offre de Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Mettre à jour vos offres de services de conseil existantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
