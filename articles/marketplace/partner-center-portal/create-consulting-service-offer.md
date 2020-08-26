---
title: Créer une offre de services de conseil – Place de marché commerciale Microsoft
description: Découvrez comment publier une offre de services de conseil sur Microsoft AppSource ou sur la Place de marché Azure à l’aide de l’Espace partenaires.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: f8ddbc66b8e7775fa35d2407bf3aa8614fe8e835
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606160"
---
# <a name="create-a-consulting-service-offer"></a>Créer une offre de services de conseil

Cet article décrit comment publier une offre de services de conseil sur la [Microsoft AppSource](https://appsource.microsoft.com/) ou sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/). Répertoriez les offres de services de conseil basées sur Microsoft [Dynamics 365](https://dynamics.microsoft.com/) et Power Platform sur AppSource. Répertoriez les offres de services de consulting basées sur Microsoft Azure sur la Place de marché Azure. Avant de commencer, si ce n’est déjà fait, [créez un compte de la Place de marché commerciale dans l’Espace partenaires](create-account.md). Vérifiez que votre compte est inscrit dans le programme de Place de marché commerciale.

Avant de créer votre offre, passez en revue les conditions préalables dans [Conditions préalables à la publication d’un service de conseil](consulting-service-prerequisites.md).

## <a name="publishing-benefits"></a>Avantages liés à la publication

Avantages de la publication sur le marketplace commercial :

- Promouvez votre entreprise en utilisant la marque Microsoft.
- Touchez potentiellement plus de 100 millions d’utilisateurs Office 365 et Dynamics 365 sur AppSource, ainsi que plus de 200 000 organisations via la Place de marché Microsoft Azure.
- Recevez des prospects de grande qualité à partir de ces places de marché.
- Bénéficiez d’une promotion de vos services assurée par les équipes de terrain et de télévente de Microsoft

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Sur la page de présentation, sélectionnez **+ Nouvelle offre** > **Service de Conseil**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Après la publication d’une offre, les modifications qui lui ont été apportées dans l’Espace partenaires n’apparaissent dans les magasins en ligne qu’après republication de l’offre. Veillez à toujours effectuer une nouvelle publication après avoir apporté des modifications.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible pour les clients dans l’adresse web de l’offre de la Place de marché.
- Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.
- L’alias d’offre ne peut pas être modifié une fois que vous avez sélectionné **Créer**.

Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-setup"></a>Configuration de l’offre

### <a name="connect-lead-management"></a>Connecter la gestion des prospects

Lors de la publication de votre offre sur la Place de marché avec l’Espace partenaires, vous _devez_ la connecter à un système de gestion des relations avec la clientèle (CRM) ou à un système d’automatisation marketing. Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit.

1. Sélectionnez **Connecter** pour spécifier l’emplacement où vous souhaitez que nous puissions envoyer les prospects. L’Espace partenaires prend en charge les systèmes suivants :

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Si votre système de CRM n’est pas listé ci-dessus, utilisez une [table Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou un [point de terminaison HTTPS](commercial-marketplace-lead-management-instructions-https.md) pour stocker les données des prospects, puis exportez les données vers votre système CRM.

2. Connectez votre offre à la destination de prospect tout en la publiant dans l’Espace partenaires.
3. Vérifiez que la connexion à la destination de prospect est correctement configurée. Une fois que vous avez effectué la publication dans l’Espace partenaires, nous validons la connexion et nous vous envoyons un prospect de test. Quand vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acheter vous-même l’offre dans l’environnement en version préliminaire.
4. Assurez-vous que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

Voici des ressources de gestion des prospects supplémentaires :

- [Vue d’ensemble de la gestion des prospects](commercial-marketplace-get-customer-leads.md)
- [Questions fréquentes sur la gestion des prospects](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [Erreurs courantes de configuration des prospects](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- PDF [Vue d’ensemble de la gestion des prospects](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (Assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="properties"></a>Propriétés

Cette page vous permet de définir le produit principal que votre offre de services de conseil couvre le mieux, de définir un type de services de conseil et de choisir les produits applicables.

1. Sélectionnez un **Produit principal** dans la liste déroulante.
2. Sélectionnez un **Type de services de conseil** dans la liste déroulante :

    - **Évaluation** : évaluation de l’environnement d’un client pour déterminer l’applicabilité d’une solution et donner une estimation du coût et de la durée.
    - **Réunion d’information** : Présentation d’une solution ou d’un service de conseil pour susciter l’intérêt du client à l’aide de frameworks, de démonstrations et d’exemples de clients.
    - **Implémentation** : installation complète qui résulte en une solution entièrement fonctionnelle. Limitez-vous à des solutions qui peuvent être implémentées en deux semaines ou moins.
    - **Preuve de concept** : implémentation de portée limitée pour déterminer si une solution répond aux exigences du client.
    - **Atelier** : engagement interactif réalisé dans les locaux d’un client. Il peut inclure une formation, des réunions d’information, des évaluations ou des démonstrations reposant sur les données ou l’environnement du client.

3. Si vous avez sélectionné **Azure** comme produit principal, sélectionnez jusqu’à trois **zones de solution**. Elles permettent aux clients de la place de marché Azure de trouver votre offre plus facilement. Si vous n’avez pas choisi Azure, ignorez cette étape.

    - Analytics
    - Modernisation des applications
    - Archivage
    - IA + Machine Learning
    - Sauvegarde
    - Données volumineuses (« Big Data »)
    - Plateforme de données
    - Gestion de centres de données
    - DevOps
    - Récupération d’urgence
    - Identité
    - Internet des Objets
    - Migration
    - Mise en réseau
    - Sécurité
    - Stockage

1. Si vous avez sélectionné **Azure** comme produit principal, vous avez la possibilité de sélectionner jusqu’à six **secteurs d’activité**. Elles permettent aux clients de la place de marché Azure de trouver votre offre plus facilement. Consultez la liste complète des secteurs d’activité dans [Bonnes pratiques pour le référencement des offres](../gtm-offer-listing-best-practices.md). Si vous n’avez pas choisi Azure, ignorez cette étape.
1. Si vous avez sélectionné un produit principal *autre* qu’Azure, sélectionnez jusqu’à trois **Produits applicables**. Elles permettent aux clients d’AppSource de trouver votre offre plus facilement. Pour plus de détails, consultez [Microsoft AppSource consulting services listing guidelines](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (Instructions de référencement des services de conseil Microsoft AppSource - PDF).
1. Si vous avez sélectionné un produit principal *autre* qu’Azure, vous avez la possibilité de sélectionner jusqu’à deux **secteurs d’activité** et deux **secteurs verticaux** pour chaque secteur d’activité. Elles permettent aux clients d’AppSource de trouver votre offre plus facilement. Consultez la liste complète des secteurs d’activité et secteurs verticaux dans [Bonnes pratiques pour le référencement des offres](../gtm-offer-listing-best-practices.md).
1. Ajoutez jusqu’à trois **Compétences** que votre entreprise a acquises afin de les afficher dans la description de votre offre de services de conseil. Au moins une compétence est requise, sauf pour les fournisseurs de services gérés Azure Expert et Azure Networking.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="offer-listing"></a>Annonce de l’offre

Ici, vous allez définir les détails de l’offre qui s’affichent dans la Place de marché. Cela comprend le nom, la description, les images, etc. de l’offre. Veillez à suivre les stratégies décrites dans la [page des stratégies de certification de la Place de marché commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) lors de la configuration de cette offre.

> [!NOTE]
> Les détails de l’offre ne doivent pas nécessairement être rédigés en anglais si la description de l’offre commence par la phrase &quot;This application is available only in [langue non anglaise]&quot;. Il est également possible de fournir un lien utile pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de l’annonce de l’offre.

Voici un exemple de la façon dont les informations de l’offre s’affichent dans la Place de marché Azure (les prix répertoriés sont fournis à titre d’exemple uniquement et ne reflètent pas les coûts réels) :

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Illustre la façon dont cette offre apparaît dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Logo
2. Price
3. Domaines fonctionnels
4. Secteurs d’activité
5. Nom de l’offre
6. Résumé
7. Description
8. Captures d’écran/vidéos

<br>Voici un exemple de la façon dont les informations de l’offre s’affichent dans Microsoft AppSource (les prix répertoriés sont fournis à titre d’exemple uniquement et ne reflètent pas les coûts réels) :

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Illustre la façon dont cette offre s’affiche dans Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Logo
2. Price
3. Products
4. Secteurs d’activité
5. Nom de l’offre
6. Résumé
7. Description
8. Captures d’écran/vidéos
9. Documents

### <a name="name"></a>Nom

Le nom que vous entrez ici s’affiche comme titre de votre offre. Ce champ est prérempli avec le texte que vous avez entré dans la zone **Alias d’offre** lors de la création de l’offre. Vous pouvez changer ce nom ultérieurement.

Le nom :

- Peut être une marque déposée (et vous pouvez inclure des symboles de marque ou de copyright).
- Ne peut pas compter plus de 50 caractères.
- Ne peut pas inclure d’emojis.

### <a name="search-results-summary"></a>Résumé des résultats de recherche

Fournissez une brève description de votre offre. Ce champ peut comporter jusqu’à 100 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

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

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>Si vous rencontrez un problème lors du chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

#### <a name="store-logos"></a>Stocker des logos

Fournissez les fichiers .png du logo de votre offre dans chacune des tailles en pixels suivantes :

- **Petit (48 x 48)**
- **Grand (216 x 216)**

Tous les logos sont requis et sont utilisés à différents endroits de l’annonce de la Place de marché.

-   Le petit logo (48 x 48 pixels) s’affiche dans les résultats de recherche de la place de marché Azure et dans la page principale de Microsoft AppSource et les pages de résultats de recherche. 
-   Le grand logo (entre 216 x 216 et 350 x 350 pixels) apparaît sur la page de votre liste d’offres dans la Place de marché Azure et Microsoft AppSource.

#### <a name="screenshots-optional"></a>Captures d’écran (facultatif)

Ajoutez jusqu’à cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chacune doit avoir une taille de 1280 x 720 pixels et être au format .png.

#### <a name="videos-optional"></a>Vidéos (facultatif)

Ajoutez jusqu’à quatre vidéos qui illustrent votre offre. Entrez le nom de la vidéo, son adresse web (URL) et une image miniature .png de 1280 x 720 pixels de la vidéo.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="pricing-and-availability"></a>Tarification et disponibilité

Ici, vous allez définir des éléments tels que la tarification, le marché et une clé privée.

1. **Marché** : Définissez le marché dans lequel votre offre sera disponible. Vous ne pouvez sélectionner qu’un seul marché par offre.
    1. Pour les marchés États-Unis et Canada, sélectionnez **Modifier les États** (ou **provinces**) pour spécifier les emplacements auxquels votre offre sera disponible.
2. **Public de la version préliminaire** : Configurez la **Clé de masquage** utilisée pour définir le public privé de votre offre.
3. **Prix** : Indiquez si votre offre est une offre **Gratuite** ou **Payante**.

    > [!NOTE]
    > Les offres de services de conseil ne couvrent que l’annonce. Toutes les transactions se produisent directement, en dehors de la Place de marché commerciale.

4. Pour une offre payante, spécifiez les paramètres **Prix et devise** et indiquez si le prix est **Fixe** ou si c’est une **Estimation**. Si c’est une Estimation, vous devez spécifier dans la description les facteurs qui auront une incidence sur le prix.
5. Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="review-and-publish"></a>Vérifier et publier

Une fois que vous avez rempli toutes les sections requises de l’offre, vous pouvez envoyer votre pour révision et publication.

1. Lorsque vous êtes prêt à publier votre offre de services de conseil, cliquez sur **Passer en revue et publier**.
2. Passez en revue les détails sur la page d’envoi finale.
3. Si nécessaire, écrivez une note à l’équipe de certification si vous pensez que les détails de votre offre nécessitent une explication.
4. Quand vous êtes prêt, sélectionnez **Envoyer**.
5. La page **Vue d’ensemble de l’offre** affiche l’étape de publication actuelle de votre offre.

Pour plus d’informations sur la durée pendant laquelle vous pouvez vous attendre à ce que votre offre reste dans chaque étape de publication, consultez [Vérifier l’état de publication de votre offre de Place de marché commerciale](publishing-status.md).

## <a name="update-your-existing-consulting-service-offers"></a>Mettre à jour vos offres de services de conseil existantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](update-existing-offer.md)
