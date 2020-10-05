---
title: Créer une offre de machine virtuelle Azure sur la Place de marché Azure
description: Découvrez comment publier une offre de machine virtuelle sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 08/07/2020
ms.openlocfilehash: b9bacb352b3559c33a4a2ef92dc0c3af6ebbc7d6
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029700"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Créer une offre de machine virtuelle Azure sur la Place de marché Azure

Cet article explique comment créer et publier une offre de machine virtuelle Azure sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/). Il s’applique aux machines virtuelles Windows et Linux qui contiennent un système d’exploitation, un disque dur virtuel (VHD) et jusqu’à 16 disques de données. 

Avant de commencer, [créez un compte Place de marché commerciale dans l’Espace partenaires](create-account.md). Vérifiez que votre compte est inscrit dans le programme de Place de marché commerciale.

## <a name="introduction"></a>Introduction

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Avantages de la publication sur la Place de marché Azure

Quand vous publiez vos offres sur la Place de marché Microsoft Azure, vous pouvez :

- Promouvoir votre entreprise avec l’aide de la marque Microsoft.
- Toucher plus de 100 millions d’utilisateurs Microsoft 365 et Dynamics 365, ainsi que plus de 200 000 organisations.
- Obtenez des prospects de grande qualité à partir de ces places de marché.
- Bénéficier d’une promotion de vos services assurée par les équipes de vente sur le terrain et de télévente de Microsoft.

### <a name="before-you-begin"></a>Avant de commencer

Si vous ne l’avez pas encore fait, consultez le [Guide de publication de l’offre de machine virtuelle](../marketplace-virtual-machines.md) et ces ressources dédiées à Machine virtuelle Azure :

- Guides de démarrage rapide
  - [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/)
  - [Modèles de démarrage rapide Azure sur GitHub](https://github.com/azure/azure-quickstart-templates)
- Tutoriels
  - [Machines virtuelles Linux](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Machines virtuelles Windows](../../virtual-machines/windows/tutorial-manage-vm.md)
- Exemples
  - [Exemples d’interface de ligne de commande Azure pour machines virtuelles Linux](../../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell pour les machines virtuelles Linux](../../virtual-machines/linux/powershell-samples.md)
  - [Exemples d’interface de ligne de commande Azure pour machines virtuelles Windows](../../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell pour machines virtuelles Windows](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>Notions de base des connaissances techniques

Le processus de conception, de création et de test des offres prend du temps et nécessite une expertise en ce qui concerne la plateforme Azure et les technologies utilisées pour créer votre offre.

Votre équipe d’ingénierie doit avoir une connaissance élémentaire des technologies Microsoft suivantes :

- [Services Azure](https://azure.microsoft.com/services/)
- [Conception et architecture des applications Azure](https://azure.microsoft.com/solutions/architecture/)
- [Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage#storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking#networking).

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le volet gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Dans la page **Vue d’ensemble**, sélectionnez **Nouvelle offre** > **Machine virtuelle Azure**.

    ![Capture d’écran montrant les options du menu du volet gauche et le bouton « Nouvelle offre ».](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Une fois votre offre publiée, les modifications que vous y apportez dans l’Espace partenaires apparaissent sur la Place de marché Azure uniquement après la republication de l’offre. Veillez à toujours republier une offre après y avoir apporté des modifications.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché Azure ainsi que dans Azure PowerShell et Azure CLI, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. L’ID peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. L’alias d’offre est le nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé sur la Place de marché Azure. Il est différent du nom de l’offre et des autres valeurs présentées aux clients.

Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-setup"></a>Configuration de l’offre

### <a name="test-drive"></a>Version d’évaluation

Une *version d’évaluation* est un excellent moyen de présenter votre offre aux clients potentiels. Elle leur donne la possibilité d’« essayer avant d’acheter », ce qui peut vous aider à augmenter vos conversions et à générer des prospects hautement qualifiés. Pour plus d’informations, consultez [Qu’est-ce qu’une version d’évaluation ?](../what-is-test-drive.md).

Pour activer une version d’évaluation pour une durée déterminée, cochez la case **Activer une version d’évaluation**. Pour supprimer la version d’évaluation de votre offre, décochez la case.

Ressources supplémentaires pour version d’évaluation :

- [Meilleures pratiques techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Bonnes pratiques marketing](../what-is-test-drive.md)
- [Téléchargez le fichier PDF de vue d’ensemble de version d’évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (vérifiez que le bloqueur de fenêtres publicitaires est désactivé).

### <a name="customer-leads"></a>Prospects

Quand vous publiez votre offre sur la Place de marché commerciale avec l’Espace partenaires, connectez-la à votre système de gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit. La connexion à un CRM est nécessaire si vous souhaitez activer une version d’évaluation (voir la section précédente). Dans le cas contraire, la connexion à un CRM est facultative.

1. Sélectionnez une destination de prospect afin de diriger les prospects vers le système de votre choix. L’Espace partenaires prend en charge les systèmes CRM suivants :
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Si votre système CRM n’est pas listé ici, utilisez le [stockage Table Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou un [point de terminaison HTTPS](commercial-marketplace-lead-management-instructions-https.md) pour stocker vos données de prospects. Ensuite, exportez les données vers votre système CRM.

1. Connectez votre offre à la destination de prospect lors de la publication dans l’Espace partenaires.
1. Vérifiez que la connexion à la destination de prospect est correctement configurée. Une fois que vous avez effectué la publication dans l’Espace partenaires, Microsoft valide la connexion et vous envoie un prospect de test. Quand vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant de déployer vous-même l’offre dans l’environnement en préversion.
1. Vérifiez que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

1. Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="properties"></a>Propriétés

Dans la page **Propriétés**, vous définissez les catégories utilisées pour grouper votre offre sur la Place de marché, la version de votre application et les contrats légaux associés.

### <a name="category"></a>Category

Sélectionnez les catégories et les sous-catégories pour placer votre offre dans les zones de recherche appropriées sur la Place de marché. Veillez à décrire l’adéquation de votre offre à ces catégories dans la description de l’offre. Sélectionnez :

- Au moins une et jusqu’à deux catégories, y compris une catégorie principale et une catégorie secondaire (facultatif).
- Jusqu’à deux sous-catégories pour chaque catégorie principale et/ou secondaire. Si aucune sous-catégorie n’est applicable à votre offre, sélectionnez **Non applicable**.

Consultez la liste complète des catégories et sous-catégories dans [Bonnes pratiques pour le référencement des offres](../gtm-offer-listing-best-practices.md). Les offres de machines virtuelles apparaissent toujours sous la catégorie **Calcul** sur la Place de marché Azure.

### <a name="legal"></a>Informations juridiques

Vous devez indiquer les Conditions générales de l’offre à vos clients. Deux options s'offrent à vous :

- **Utiliser vos propres conditions générales**

   Pour fournir vos propres conditions générales, entrez jusqu’à 10 000 caractères dans la zone **Conditions générales**. Si vous avez besoin d’une description plus longue, entrez une adresse web unique pour vos conditions générales. Elle sera présentée aux clients sous forme de lien actif.

   Vos clients doivent accepter ces conditions avant de pouvoir essayer votre application.

- **Utiliser le contrat Standard pour la Place de marché commerciale Microsoft**

   Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard pour la place de marché commerciale. Quand vous proposez votre logiciel dans le cadre du contrat Standard, les clients ne doivent lire et accepter ce contrat qu’une seule fois, et vous n’avez pas besoin d’élaborer des conditions générales personnalisées.

   Utilisez le contrat Standard en cochant la case **Utiliser le contrat Standard pour la Place de marché commercial Microsoft** puis, dans la fenêtre contextuelle, sélectionnez **Accepter** (vous devrez peut-être faire défiler l’écran vers le haut pour afficher cette fenêtre).

   ![Capture d’écran montrant le volet Informations juridiques dans l’Espace partenaires avec la case à cocher « Utiliser le contrat Standard pour la Place de marché commercial Microsoft ».](media/use-standard-contract.png)

  > [!NOTE]
  > Après avoir publié une offre à l’aide du contrat Standard pour la Place de marché commerciale, vous ne pouvez pas utiliser vos propres conditions générales. Vous pouvez proposer une solution avec le contrat Standard ou avec vos propres conditions générales.

  Pour plus d’informations, consultez [Contrat standard pour la Place de marché commerciale de Microsoft](../standard-contract.md). Téléchargez le fichier PDF de [contrat Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (vérifiez que le bloqueur de fenêtres publicitaires est désactivé).

  **Avenants au contrat Standard**

  Les avenants au contrat Standard vous permettent de sélectionner le contrat Standard par souci pratique et d’établir les conditions pour votre produit ou entreprise. Les clients doivent examiner les avenants apportés au contrat uniquement s’ils ont déjà lu et accepté le contrat Standard de Microsoft. Il existe deux types d’avenants :

  * **Avenants universels** : Ces modifications sont appliquées de manière universelle au contrat Standard de tous les clients. Elles s’affichent pour chaque client de l’offre dans le flux d’achat. Les clients doivent accepter les conditions du contrat Standard et les avenants avant de pouvoir utiliser votre offre. Vous pouvez fournir un seul amendement universel par offre. Vous pouvez entrer un nombre illimité de caractères dans cette zone. Ces termes sont présentés aux clients dans AppSource, la Place de marché Azure et/ou le portail Azure pendant le flux de découverte et d’achat.

  * **Avenants personnalisés** : il s’agit d’avenants spéciaux apportés au contrat Standard qui ciblent certains clients par le biais des ID de locataires Azure. Vous pouvez choisir le locataire que vous souhaitez cibler. Seuls les clients du locataire reçoivent les termes des avenants personnalisés dans le flux d’achat de l’offre. Les clients doivent accepter les conditions du contrat Standard et les avenants avant de pouvoir utiliser votre offre.

    1. Commencez par sélectionner **Ajouter des conditions d’amendement personnalisées (10 max)** . Vous pouvez fournir jusqu’à dix conditions d’amendement personnalisées par offre. Effectuez les actions suivantes :

       a. Entrez vos propres termes d’avenant dans la zone **Termes d’avenant personnalisé**. Vous pouvez entrer un nombre illimité de caractères. Seuls les clients des ID de locataire que vous spécifiez pour ces termes personnalisés les verront dans le flux d’achat de l’offre dans le portail Azure.

       b. (Obligatoire) Spécifiez les **ID de locataire**. Chaque amendement personnalisé peut être ciblé vers jusqu’à 20 ID de locataire. Si vous ajoutez un amendement personnalisé, vous devez fournir au moins un ID de locataire, qui identifie votre client dans Azure. Votre client peut le trouver pour vous dans Azure en sélectionnant **Azure Active Directory** > **Propriétés**. La valeur de l’ID de répertoire est l’ID de locataire (par exemple, 50c464d3-4930-494c-963C-1e951d15360e). Vous pouvez également rechercher l’ID de locataire de l’organisation de votre client à l’aide de son adresse web de nom de domaine sur [Quel est mon ID de locataire Microsoft Azure et Microsoft 365 ?](https://www.whatismytenantid.com/)

       c. (Facultatif) : fournissez une **Description** claire de l’ID de locataire, qui vous aide à identifier le client que vous ciblez avec cet avenant.

        > [!NOTE]
        > Ces deux types d’avenant sont associés l’un à l’autre. Les clients ciblés par les avenants personnalisés reçoivent aussi les avenants universels apportés au contrat Standard lors de l’achat.

    1. Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="offer-listing"></a>Annonce de l’offre

Dans la page **Annonce de l’offre**, vous définissez les détails de l’offre, notamment le nom de l’offre, la description, les liens et les contacts.

> [!NOTE]
> Il n’est pas nécessaire que le contenu de l’annonce de l’offre (par exemple la description, les documents, les captures d’écran et les conditions d’utilisation) soit en anglais, tant que sa description commence par l’expression : « This application is available only in \<non-English language>. » Vous pouvez également fournir une URL vers un site qui offre du contenu dans une langue autre que celle utilisée dans le contenu de l’annonce de l’offre.

### <a name="marketplace-details"></a>Détails de la Place de marché

#### <a name="name"></a>Nom

Le nom que vous entrez ici est présenté aux clients comme titre de votre annonce d’offre. Ce champ est renseigné automatiquement avec le nom que vous avez entré dans la zone **Alias d’offre** lors de la création de l’offre. Vous pouvez changer ce nom ultérieurement. Le nom :

- Peut être une marque déposée. Vous pouvez inclure les symboles de marque et de copyright.
- Ne peut pas contenir plus de 50 caractères.
- Ne peut pas inclure d’emojis.

#### <a name="search-results-summary"></a>Résumé des résultats de recherche

Entrez une courte description de votre offre, qui sera affichée dans les résultats de la recherche sur la Place de marché. Elle peut comporter jusqu’à 100 caractères.

#### <a name="long-summary"></a>Résumé long

Entrez une description plus longue de votre offre, qui sera affichée dans les résultats de la recherche sur la Place de marché. Elle peut comporter jusqu’à 256 caractères.

#### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Lien de la politique de confidentialité

Entrez l’adresse web (URL) de la politique de confidentialité de votre organisation. Veillez à ce que votre offre respecte les lois et les réglementations en matière de confidentialité. Vous devez également publier une politique de confidentialité valide sur votre site Web.

### <a name="useful-links"></a>Liens utiles

Fournissez des documents en ligne complémentaires à propos de votre offre. Pour ajouter un lien, sélectionnez **Ajouter un lien**, puis renseignez les champs suivants :

- **Name** : les clients verront le nom dans la page des détails.
- **Lien (URL)**  : entrez un lien qui permet aux clients de voir votre document en ligne.

### <a name="customer-support-links"></a>Liens de support clientèle

Indiquez le site Web permettant aux clients de contacter votre support technique.

- Site Web du support Azure Global
- Site Web du support Azure Government

### <a name="partner-support-contact"></a>Contact du support technique du partenaire

Fournissez les coordonnées des partenaires Microsoft à utiliser lorsque vos clients ouvrent un ticket de support. Ces informations ne sont pas listées sur la Place de marché Azure.

- Nom
- E-mail
- Téléphone

### <a name="engineering-contact"></a>Contact ingénierie

Fournissez les coordonnées que Microsoft doit utiliser en cas de problème avec votre offre, y compris les problèmes de certification. Ces informations ne sont pas listées sur la Place de marché Azure.

- Nom
- E-mail
- Téléphone

### <a name="azure-marketplace-media"></a>Médias de la Place de marché Azure

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format PNG. Votre demande sera rejetée si les images sont floues.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Si vous rencontrez un problème pendant le chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

#### <a name="azure-marketplace-logos"></a>Logos de la Place de marché Azure

Fournissez un fichier PNG pour le logo de **grande taille**. L'Espace partenaires l'utilisera pour créer un logo **Petit** et **Moyen**. Plus tard, vous pourrez éventuellement les remplacer par d'autres images.

- **Grand** (de 216 x 216 à 350 x 350 px, obligatoire)
- **Moyen** (90 x 90 px, facultatif)
- **Petit** (48 x 48 px, facultatif)

Ces logos sont utilisés à différents emplacements dans la liste :

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez jusqu’à cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chaque capture d’écran doit avoir une taille de 1280 &times; 720 pixels et être au format PNG. Chaque capture d’écran doit inclure une légende.

#### <a name="videos"></a>Vidéos

Ajoutez jusqu’à cinq vidéos qui illustrent votre offre. Les vidéos doivent être hébergées sur un service vidéo externe. Entrez le nom, l’adresse web et une image miniature PNG de 1280 &times; 720 pixels de chaque vidéo.

Pour obtenir des ressources supplémentaires concernant la place de marché, consultez [Meilleures pratiques pour les annonces d’offre d’une place de marché](../gtm-offer-listing-best-practices.md).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="preview"></a>PRÉVERSION

Sélectionnez l’onglet **Préversion**, puis sélectionnez un **public de préversion** restreint en vue de valider votre offre avant de la publier plus largement en ligne sur la Place de marché commerciale.

> [!IMPORTANT]
> Après avoir vérifié votre offre dans le volet **Préversion**, sélectionnez **Mise en service** pour publier votre offre sur la Place de marché commerciale.

Le public de votre préversion est identifié par des ID (GUID) d’abonnements Azure, avec une description facultative de chacun. Aucun de ces champs ne peut être vu par les clients. Vous pouvez trouver votre ID d’abonnement Azure dans la page **Abonnements** du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, soit séparément (jusqu’à 10 ID), soit en chargeant un fichier CSV (jusqu’à 100 ID). En ajoutant ces ID d’abonnement, vous définissez qui peut afficher un aperçu de votre offre avant qu’elle ne soit publiée en ligne. Si votre offre est déjà publiée, vous pouvez toujours définir un public de préversion afin de tester des changements ou des mises à jour de l’offre.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de préversion peut accéder à votre offre _avant_ sa publication sur la Place de marché . Il peut voir et valider tous les plans, notamment ceux qui seront accessibles uniquement à un public privé une fois que votre offre sera entièrement publiée sur la Place de marché Azure. Un public privé (défini dans le volet **Tarification et disponibilité** du plan) dispose d’un accès exclusif à un plan particulier.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante.

## <a name="plan-overview"></a>Vue d’ensemble du plan

Vous pouvez proposer différentes options de plan dans la même offre au sein de l’Espace partenaires. Une offre nécessite au moins un plan (précédemment appelé SKU), qui peut varier en termes de public de monétisation, de région Azure, de fonctionnalités ou d’images de machines virtuelles.

Vous pouvez créer jusqu’à 100 plans pour chaque offre : jusqu’à 45 peuvent être privés. En savoir plus sur les offres privées dans les [offres du marketplace commercial de Microsoft](../private-offers.md).

Une fois que vous avez créé vos plans, sélectionnez l’onglet **Vue d’ensemble du plan** pour afficher :

- Noms des plans
- Modèles de licences
- Audience (publique ou privée)
- État actuel de la publication
- Actions disponibles

Les actions disponibles dans le volet **Vue d’ensemble du plan** varient en fonction de l’état actuel de votre plan.

- Si l’état du plan est Brouillon, sélectionnez **Supprimer le brouillon**.
- Si l’état du plan est Publié, sélectionnez **Arrêter la vente du plan** ou **Synchroniser le public privé**.

### <a name="create-a-new-plan"></a>Créer un plan

Sélectionnez **Créer un plan** en haut de la page. La boîte de dialogue **Nouveau plan** s’affiche.

Dans la zone **ID du plan**, créez un ID de plan unique pour chaque plan contenu dans cette offre. Cet ID sera visible par les clients dans l’adresse web du produit. Utilisez uniquement des lettres minuscules et des chiffres, des tirets ou des traits de soulignement, dans la limite de 50 caractères.

> [!NOTE]
> L’ID de plan ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Dans la zone **Nom du plan**, entrez un nom pour ce plan. Les clients voient ce nom quand ils décident du plan à sélectionner dans votre offre. Créez un nom unique qui identifie clairement les différences entre chaque plan. Par exemple, vous pouvez entrer **Windows Server** avec des plans *Paiement à l’utilisation*, *BYOL (apportez votre propre licence)* , *Avancé* et *Entreprise*.

Sélectionnez **Create** (Créer).

### <a name="plan-setup"></a>Configuration du plan

Définissez la configuration générale du type de plan, indiquez si le plan réutilisera la configuration technique d’un autre plan, et identifiez les régions Azure où le plan doit être disponible. Les options que vous choisissez ici déterminent les champs affichés dans les autres volets du même plan.

#### <a name="reuse-a-technical-configuration"></a>Réutiliser une configuration technique

Si vous avez plusieurs plans du même type et que les packages qu’ils utilisent sont identiques, vous pouvez sélectionner l’option pour que **ce plan réutilise la configuration technique d’un autre plan**. Cette option vous permet de sélectionner l’un des autres plans du même type pour cette offre et de réutiliser sa configuration technique.

> [!NOTE]
> Si vous réutilisez la configuration technique d’un autre plan, l’onglet **Configuration technique** ne s’affiche plus pour ce plan. Les paramètres de la configuration technique de l’autre plan, y compris les mises à jour effectuées ultérieurement, seront également appliqués à ce plan. Ce paramètre n’est plus modifiable après la publication du plan.

#### <a name="azure-regions"></a>Régions Azure

Votre plan doit être rendu disponible dans au moins une région Azure.

Sélectionnez l’option **Azure Global** pour mettre votre plan à la disposition des clients de toutes les régions Azure qui disposent d’une intégration de la Place de marché commerciale. Pour plus d’informations, consultez [Disponibilité géographique et prise en charge des devises](../marketplace-geo-availability-currencies.md).

Sélectionnez l’option **Azure Government** pour proposer votre plan dans la région [Azure Government](../../azure-government/documentation-government-welcome.md). Cette région fournit un accès contrôlé aux clients d’entités gouvernementales fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’aux partenaires éligibles pour les servir. En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires. Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement).

Avant de le publier dans [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md), testez et validez votre plan dans l’environnement, car certains points de terminaison peuvent être différents. Pour configurer et tester votre plan, demandez un compte d’essai à partir de la page [Microsoft Azure Government Trial](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Une fois votre plan publié et disponible dans une région Azure spécifique, vous ne pouvez pas supprimer cette région.

#### <a name="azure-government-certifications"></a>Certifications Azure Government

Cette option est visible uniquement si vous avez sélectionné **Azure Government** comme région Azure dans la section précédente.

Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales. Par exemple, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS. Afin de renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens décrivant ces certifications. Ces liens peuvent être des liens vers votre liste de plans directement sur le programme, ou des liens vers des descriptions de votre conformité avec ces certifications sur vos propres sites web. Ces liens sont visibles uniquement par les clients Azure Government.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="plan-listing"></a>Liste des plans

Dans cette section, vous allez configurer les détails de l’annonce du plan. Ce volet affiche des informations spécifiques qui peuvent différer des autres plans de la même offre.

#### <a name="plan-name"></a>Nom du plan

Ce champ est renseigné automatiquement avec le nom que vous avez donné à votre plan au moment de sa création. Ce nom apparaît sur la Place de marché Azure comme titre de ce plan. Il est limité à 100 caractères.

#### <a name="plan-summary"></a>Résumé du plan

Fournissez un bref résumé de votre plan (pas de l’offre). Ce résumé est limité à 100 caractères.

#### <a name="plan-description"></a>Description du plan

Décrivez ce qui rend ce plan logiciel unique, ainsi que les différences entre les plans de votre offre. Décrivez uniquement le plan (pas l’offre). La description du plan peut contenir jusqu’à 2 000 caractères.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="pricing-and-availability"></a>Tarification et disponibilité

Dans ce volet, vous configurez :

- Les marchés où ce plan est disponible. Chaque plan doit être disponible sur au moins un [marché](../marketplace-geo-availability-currencies.md).
- Le tarif horaire.
- Si vous voulez que le plan soit visible à tous ou uniquement à des utilisateurs spécifiques (un public privé).

#### <a name="markets"></a>Marchés

Chaque plan doit être disponible sur au moins un marché. Cochez la case de chaque lieu de marché où ce plan doit être disponible à l’achat. (Les utilisateurs de ces marchés peuvent toujours déployer l’offre dans toutes les régions Azure sélectionnées dans la section [« Configuration du plan »](#plan-setup).) Le bouton **Impôt versé** indique les pays/régions dans lesquels Microsoft reverse les taxes en votre nom. La publication en Chine se limite aux plans *gratuits* ou de type *Apportez votre propre licence* (BYOL).

Si vous avez déjà fixé des prix pour votre plan en dollar américain (USD) et ajouté un autre emplacement de marché, le prix de ce nouveau marché est calculé selon le taux de change en vigueur. Vérifiez toujours le prix de chaque Place de marché avant toute publication. Passez en revue la tarification en sélectionnant **Exporter les prix (xlsx)** après avoir enregistré vos modifications.

Quand vous supprimez un marché, les clients de ce marché utilisant des déploiements actifs ne pourront pas créer de nouveaux déploiements ou mettre à l’échelle leurs déploiements existants. Les déploiements existants ne sont pas affectés.

#### <a name="pricing"></a>Tarifs

Pour le **Modèle de licence**, sélectionnez **Plan facturé mensuellement selon l’utilisation** afin de configurer les prix de ce plan, ou **BYOL (apportez votre propre licence)** pour permettre aux clients d’utiliser ce plan avec leur licence existante.

Pour un plan facturé mensuellement selon l’utilisation, utilisez l’une des trois options d’entrée de prix suivantes :

- **Par cœur** : indiquez le prix par cœur en USD. Microsoft calcule le prix par taille de cœur et le convertit dans les devises locales en appliquant le taux de change actuel.
- **Par taille de cœur** : indiquez le prix par taille de cœur en USD. Microsoft calcule le prix et le convertit dans les devises locales en appliquant le taux de change actuel.
- **Par marché et taille de cœur** : indiquez le prix de chaque taille de cœur pour tous les marchés. Vous pouvez importer les prix à partir d’une feuille de calcul.

> [!NOTE]
> Enregistrez les modifications de la tarification pour autoriser l’exportation des données de tarification. Une fois qu’un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement. Pour vérifier que les prix sont corrects avant de les publier, exportez la feuille de calcul de prix et passez en revue les prix sur chaque marché.

#### <a name="free-trial"></a>Version d’évaluation gratuite

Vous pouvez proposer à vos clients un *essai gratuit* d’un mois ou de trois mois.

#### <a name="visibility"></a>Visibilité

Vous pouvez concevoir chaque plan pour qu’il soit visible par tous les utilisateurs ou uniquement par un public présélectionné. Définissez l’appartenance à ce public restreint à l’aide des ID d’abonnement Azure.

**Public** : votre plan peut être consulté par tout le monde.

**Public privé** : votre plan ne peut être consulté que par un public présélectionné. Une fois votre plan publié en tant que plan privé, vous pouvez mettre à jour le public visé ou rendre le plan public. Une fois que vous avez rendu un plan public, il doit rester public. Vous ne pouvez pas le reconvertir en plan privé.

> [!NOTE]
> Un public privé ou restreint est différent du public de préversion que vous avez défini dans le volet **Préversion**. Un public de préversion peut accéder à votre offre _avant_ sa publication sur la Place de marché Azure. La sélection d’un public privé ne s’applique qu’à un plan spécifique, tandis que le public de préversion peut voir tous les plans privés et publics dans le cadre de la validation.

**Audience restreinte (ID d’abonnement Azure)**  : définissez le public qui aura accès à ce plan privé au moyen d’ID d’abonnements Azure. Le cas échéant, incluez une description de chaque ID d’abonnement Azure que vous avez attribué. Ajoutez jusqu’à 10 ID d’abonnements manuellement, ou jusqu’à 20 000 ID si vous importez une feuille de calcul CSV. Les ID d’abonnements Azure sont représentés par des GUID, où toutes les lettres doivent être en minuscules.

>[!Note]
>Les offres privées ne sont pas prises en charge avec les abonnements Azure souscrits via un revendeur participant au programme des fournisseurs de solutions cloud (CSP).

#### <a name="hide-a-plan"></a>Masquer un plan

Si vous comptez utiliser votre machine virtuelle uniquement de manière indirecte quand elle est référencée par un autre modèle de solution ou une application managée, cochez cette case pour publier la machine virtuelle tout en la masquant aux yeux des clients susceptibles de la rechercher ou d’y accéder directement.

> [!NOTE]
> Les plans masqués ne prennent pas en charge les liens de préversion.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="technical-configuration"></a>Configuration technique

Fournissez les images et autres propriétés techniques associées à ce plan. Pour plus d’informations, consultez [Créer une offre de conteneur Azure](create-azure-container-technical-assets.md).

> [!NOTE]
> L’onglet **Configuration technique** ne s’affiche pas si vous avez configuré ce plan pour réutiliser les packages d’un autre plan sous l’onglet **Configuration du plan**.

#### <a name="operating-system"></a>Système d’exploitation

Dans le volet **Système d’exploitation**, effectuez les étapes suivantes :

- Pour **Famille de systèmes d’exploitation**, sélectionnez le système d’exploitation **Windows** ou **Linux**.
- Pour **Mise en production** ou **Fournisseur**, sélectionnez la mise en production Windows ou le fournisseur Linux.
- Pour **Nom convivial du système d’exploitation**, entrez un nom de système d’exploitation convivial. Ce nom est visible par les clients.

#### <a name="recommended-vm-sizes"></a>Tailles de machines virtuelles recommandées

Sélectionnez jusqu’à six tailles de machine virtuelle recommandées à afficher sur la Place de marché Azure.

#### <a name="open-ports"></a>Ouvrir les ports

Ouvrez des ports publics ou privés sur une machine virtuelle déployée.

#### <a name="storage-option-for-deployment"></a>Option de stockage pour le déploiement

Pour **Option de déploiement de disque**, sélectionnez le type de déploiement de disque que vos clients peuvent utiliser pour la machine virtuelle. Microsoft recommande de limiter le déploiement aux **disques managés** uniquement.

#### <a name="properties"></a>Propriétés

Pour **Prendre en charge l’accélération réseau**, indiquez si votre machine virtuelle prend en charge l’[accélération réseau](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Images de machine virtuelle

Spécifiez une version de disque et l’URI de signature d’accès partagé (SAS) pour les images de machine virtuelle. Ajoutez jusqu’à 16 disques de données pour chaque image de machine virtuelle. Spécifiez une seule nouvelle version d’image par plan à chaque envoi. Quand une image a été publiée, vous ne pouvez pas la modifier, mais vous pouvez la supprimer. La suppression d’une version empêche les utilisateurs nouveaux et existants de déployer une nouvelle instance de la version supprimée.

- **Version du disque** : version de l’image que vous fournissez.
- **URI SAS** : emplacement dans votre compte Stockage Azure où vous avez stocké le disque dur virtuel du système d’exploitation. Pour obtenir l’URI SAS, consultez [Obtenir l’URI de la signature d’accès partagé pour vos images de machine virtuelle](get-sas-uri.md).
- Les images de disque de données sont également des URI de signature d’accès partagé VHD qui sont stockés dans leurs comptes de stockage Azure.
- Ajoutez une seule image par envoi dans un plan.

Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données dont la solution a besoin. Pendant le déploiement, les clients ne peuvent pas supprimer les disques qui font partie d’une image, mais ils peuvent toujours ajouter des disques pendant ou après le déploiement.

Sélectionnez **Enregistrer le brouillon** avant de continuer, puis revenez à la **vue d’ensemble du plan**.

## <a name="resell-through-csps"></a>Revendre via des fournisseurs de solutions cloud

Développez la portée de votre offre en la mettant à la disposition des partenaires du programme [Fournisseur de solutions Cloud](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Tous les plans BYOL (apportez votre propre licence) rejoignent automatiquement le programme. Vous pouvez également choisir de joindre vos plans non BYOL.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="test-drive"></a>Version d’évaluation

Configurez une démonstration, ou *version d’évaluation*, qui permet aux clients d’essayer votre offre pendant un laps de temps fixe avant de l’acheter. Pour créer un environnement de démonstration pour vos clients, consultez [Versions d’évaluation d’offres sur la Place de marché commerciale](test-drive.md).

Pour activer une version d’évaluation, cochez la case **Activer une version d’évaluation** dans le volet **Configuration de l’offre**. Pour supprimer la version d’évaluation de votre offre, décochez la case.

Ressources supplémentaires pour version d’évaluation :

- [Bonnes pratiques marketing](../what-is-test-drive.md)
- [Meilleures pratiques techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- Fichier PDF de [vue d’ensemble de version d’évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (vérifiez que le bloqueur de fenêtres publicitaires est désactivé)

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="review-and-publish"></a>Vérifier et publier

Une fois que vous avez rempli toutes les sections requises de l’offre, vous pouvez l’envoyer pour révision et publication.

Dans l’angle supérieur droit, sélectionnez **Vérifier et publier**.

Dans ce volet, vous pouvez :

- Afficher le statut d’achèvement de chaque section de l’offre :

  - **Non démarré** : la section n’a pas été démarrée et doit être complétée.
  - **Caractère incomplet** : la section comporte des erreurs qui doivent être corrigées ou nécessite plus d’informations de votre part. Pour obtenir des instructions sur la mise à jour des informations incomplètes, consultez les sections précédentes de cet article.
  - **Complet** : la section est complète et ne comporte aucune erreur. Toutes les sections de l’offre doivent être complètes pour que vous puissiez envoyer l’offre.
- Fournissez des **Notes pour la certification** à l’équipe de certification afin de vous assurer que votre application est testée correctement. Incluez des instructions de test et toutes les notes supplémentaires susceptibles d’aider l’équipe à comprendre votre application.

Pour envoyer l’offre à des fins de publication, sélectionnez **Vérifier et publier**.

Microsoft enverra un e-mail pour vous faire savoir si une préversion de l’offre est disponible pour vérification et approbation. Pour que votre offre soit publiée pour le public (ou pour un public privé, si elle est privée), accédez à l’Espace partenaires, recherchez la page **Vue d’ensemble** de votre offre, puis sélectionnez **Mise en service**. L’état de votre offre apparaît en haut de la page quand la publication est en cours.

### <a name="errors-and-review-feedback"></a>Erreurs et commentaires de révision

L’étape de **validation manuelle** du processus de publication représente une révision complète de votre offre et de ses ressources techniques associées. Si ce processus détecte des erreurs dans votre offre, vous recevrez un rapport de certification détaillant les problèmes. Il vous suffit alors d’apporter les corrections nécessaires puis de republier votre offre.

## <a name="offer-overview"></a>Vue d’ensemble de l’offre

La page **Vue d’ensemble de l’offre** affiche une représentation visuelle des étapes (à la fois terminées et en cours) nécessaires à la publication de votre offre, ainsi que la durée nécessaire pour effectuer chaque étape.

Elle contient également des liens pour vous aider à gérer l’offre, en fonction de son état :

- Si l’offre est un brouillon : [Supprimer un brouillon d’offre](update-existing-offer.md#delete-a-draft-offer)
- Si l’offre est active : [Arrêter la vente d’une offre](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Si l’offre est disponible en préversion : [Mise en service](publishing-status.md#publisher-approval)
- Si vous n’avez pas effectué la déconnexion de l’éditeur : [Annuler la publication](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>Exemples de la Place de marché

Voici un exemple de la façon dont les informations s’affichent dans la Place de marché Azure :

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Illustre la façon dont cette offre apparaît dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Logo de grande taille
2. Price
3. Catégories
4. Conditions générales
5. Adresse de la politique de confidentialité (lien)
6. Nom de l’offre
7. Description
8. Liens utiles
9. Captures d’écran/vidéos

<br>Voici un exemple de la façon dont les informations de l’offre s’affichent dans les résultats de la recherche de la Place de marché Azure :

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Illustre la façon dont cette offre apparaît dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Logo de petite taille
2. Nom de l’offre
3. Résumé des résultats de recherche
4. Version d’évaluation

<br>Voici un exemple de détails du plan dans la Place de marché Azure :

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Illustre la façon dont cette offre apparaît dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Nom et résumé du plan
2. Tailles de machines virtuelles recommandées
3. Tarification du plan

<br>Voici un exemple de la façon dont les informations de l’offre s’affichent dans le portail Azure :

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Illustre la façon dont cette offre apparaît dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Nom
2. Description
3. Liens utiles
4. Captures d’écran/vidéos

<br>Voici un exemple de la façon dont les informations de l’offre s’affichent dans les résultats de la recherche du portail Azure :

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Illustre la façon dont cette offre apparaît dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Logo de petite taille
2. Nom de l’offre
3. Résumé des résultats de recherche

<br>Voici un exemple de détails du plan dans le portail Azure :

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Illustre la façon dont cette offre apparaît dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Nom du plan
2. Description du plan

## <a name="next-step"></a>Étape suivante

- [Mettre à jour une offre existante dans la Place de marché commerciale](update-existing-offer.md)
