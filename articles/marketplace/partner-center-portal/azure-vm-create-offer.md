---
title: Créer une offre de machine virtuelle Azure - Place de marché Azure
description: Découvrez comment créer une offre de machine virtuelle sur la Place de marché commerciale.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869057"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Créer une offre de machine virtuelle Azure

> [!IMPORTANT]
> Nous allons déplacer la gestion de vos offres de machines virtuelles Azure du Portail Cloud Partner vers l’Espace partenaires. Tant que vos offres ne sont pas migrées, poursuivez les instructions de l’article [Créer une offre de machine virtuelle](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) dans le Portail Cloud Partner pour gérer vos offres.

Cet article explique comment créer et publier une offre de machine virtuelle Azure sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/). Il s’applique aux machines virtuelles Windows et Linux qui contiennent un système d’exploitation, un disque dur virtuel (VHD) et jusqu’à 16 disques de données.

## <a name="introduction"></a>Introduction

### <a name="publishing-benefits"></a>Avantages liés à la publication

La publication sur la Place de marché Azure présente les avantages suivants :

- Promouvez votre entreprise en utilisant la marque Microsoft
- Touchez plus de 100 millions d’utilisateurs Office 365 et Dynamics 365, ainsi que plus de 200 000 organisations via la Place de marché Azure
- Obtenez des prospects de grande qualité à partir de ces places de marché
- Bénéficiez d’une promotion de vos services assurée par les équipes de terrain et de télévente de Microsoft

### <a name="before-you-begin"></a>Avant de commencer

Si vous ne l’avez pas encore fait, consultez le [Guide de publication de l’offre de machine virtuelle](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) et ces ressources dédiées à la machine virtuelle Azure :

- Guides de démarrage rapide
  - [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/)
  - [Modèles de démarrage rapide Azure sur GitHub](https://github.com/azure/azure-quickstart-templates)
- Tutoriels
  - [Machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Machines virtuelles Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Exemples
  - [Exemples Azure CLI pour les machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell pour les machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Exemples Azure CLI pour machines virtuelles Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell pour machines virtuelles Windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Notions de base des connaissances techniques

Concevoir, créer et tester ces ressources demande du temps et des connaissances techniques sur la plateforme Azure et les technologies utilisées pour créer l’offre.

Votre équipe d’ingénierie doit maîtriser les technologies Microsoft suivantes :

- Connaissances de base des [services Azure](https://azure.microsoft.com/services/)
- Comment [concevoir et créer des applications Azure](https://azure.microsoft.com/solutions/architecture/)
- Expérience de travail avec les [machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), [Stockage Azure](https://azure.microsoft.com/services/?filter=storage#storage) et [Mise en réseau Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Créer une offre de machine virtuelle Azure

Avant de pouvoir créer une offre de machine virtuelle Azure, vous devez disposer d’un compte de Place de marché commerciale dans l’Espace partenaires. Si vous n’en avez pas encore créé un, consultez [Créer un compte de Place de marché commerciale dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home) dans le menu supérieur, puis sélectionnez **Tableau de bord**.
2. Dans la barre de navigation de gauche, sélectionnez **Place de marché commerciale**, puis **Vue d’ensemble**.
3. Sur la **page de présentation**, sélectionnez **+Nouvelle offre**, puis **Machine virtuelle Azure**. La boîte de dialogue **Nouvelle offre** s’affiche.

    ![Affiche la page de présentation de l’Espace partenaires avec le bouton Nouvelle offre et l’offre de machine virtuelle Azure sélectionnée.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>ID et alias de l’offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché ainsi que dans Azure PowerShell et Azure CLI, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1** ici, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires. Ce nom n’est pas utilisé dans la Place de marché et est différent du nom de l’offre et des autres informations présentées aux clients.

Sélectionnez **Créer** pour générer l’offre et continuer.

## <a name="offer-setup"></a>Configuration de l’offre

### <a name="test-drive"></a>Test drive

Configurez une démonstration (version d'évaluation) permettant aux clients d’évaluer votre offre avant de l’acheter. Pour créer un environnement de démonstration permettant aux clients d’évaluer votre offre pendant une période fixe, consultez [Évaluer votre offre sur la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Pour activer une version d'évaluation, cochez la case **Activer une version d'évaluation**. Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

Ressources supplémentaires pour version d’évaluation :

- [Meilleures pratiques techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Bonnes pratiques marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Vue d’ensemble des versions d'évaluation](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé).

### <a name="lead-management"></a>Gestion des prospects

Lors de la publication de votre offre sur la Place de marché commerciale avec l’Espace partenaires, connectez-vous à votre système de gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit. La connexion à un système CRM est nécessaire si vous activez une **version d'évaluation** (voir la section précédente). Sinon, elle est facultative.

1. Sélectionnez une destination de prospect afin de diriger les prospects vers le système de votre choix. L’Espace partenaires prend en charge les systèmes CRM suivants :
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Si votre système de CRM n’est pas listé ci-dessus, utilisez une [table Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou un [point de terminaison HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) pour stocker les données des prospects. Ensuite, exportez les données vers votre système CRM.

2. Connectez votre offre à la destination de prospect tout en la publiant dans l’Espace partenaires.
3. Vérifiez que la connexion à la destination de prospect est correctement configurée. Une fois que vous avez effectué la publication dans l’Espace partenaires, nous validons la connexion et nous vous envoyons un prospect de test. Quand vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant de déployer vous-même l’offre dans l’environnement en version préliminaire.
4. Assurez-vous que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="properties"></a>Propriétés

C’est dans cette page que vous définissez les catégories et secteurs utilisés pour grouper votre offre selon les Places de marché, la version de votre application et les contrats légaux associés.

### <a name="categories"></a>Catégories

Sélectionnez au minimum une et au maximum cinq catégories. Ces catégories servent à placer votre offre dans les zones de recherche appropriées sur la place de marché. Dans la description de l’offre, expliquez comment votre offre prend en charge ces catégories. Les offres de machines virtuelles apparaissent sous la catégorie de **Calcul** dans la Place de marché Azure.

### <a name="legal"></a>Informations juridiques

Vous devez fournir les conditions générales de l’offre. Deux options s'offrent à vous :

- Utiliser vos propres conditions générales
- Utiliser le contrat Standard pour la Place de marché commerciale Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Utiliser vos propres conditions générales

Pour fournir vos propres conditions générales, entrez jusqu’à 10 000 caractères dans la zone **Conditions générales**. Si vous avez besoin d’une description plus longue, entrez une adresse web unique pointant vers l’emplacement où se trouvent vos conditions générales. Ceci affichera un lien actif aux clients.

Les clients doivent accepter ces conditions avant de pouvoir essayer votre application.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Utiliser le contrat Standard pour la Place de marché commerciale Microsoft

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard pour la place de marché commerciale. Lorsque vous proposez votre logiciel dans le cadre du contrat Standard, les clients ne doivent lire et accepter ce contrat qu’une seule fois, et vous n’avez pas besoin d’élaborer des conditions générales personnalisées.

Utilisez le contrat Standard en cochant la case **Utiliser le contrat Standard pour la Place de marché commercial Microsoft**, puis sélectionnez **Accepter** dans la fenêtre contextuelle (vous devrez peut-être faire défiler l’écran vers le haut pour afficher cette fenêtre).

![Affiche la page de présentation de l’Espace partenaires avec le bouton Nouvelle offre et l’offre de services de conseil sélectionnée.](media/use-standard-contract.png)

> [!NOTE]
> Après avoir publié une offre à l’aide du contrat Standard pour la Place de marché commerciale, vous ne pouvez pas utiliser vos propres conditions générales. Proposez votre solution avec le contrat Standard **ou** dans le cadre de vos propres conditions générales.

Pour en savoir plus sur le contrat Standard, consultez [Contrat standard pour la Place de marché commerciale de Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Vous pouvez télécharger le fichier PDF [Contrat Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assurez-vous que le bloqueur de fenêtres publicitaires est désactivé).

##### <a name="standard-contract-amendments"></a>Contrat Standard | Modifications

Les modifications du contrat Standard vous permettent de sélectionner le contrat Standard par souci de simplicité et d’élaborer les conditions pour votre produit ou entreprise. Les clients sont invités à examiner les modifications apportées au contrat, puis à accepter le contrat Standard de Microsoft. Deux types de modification sont disponibles : universelle et personnalisée.

**Modifications universelles** ; ces modifications sont appliquées de manière universelle au contrat Standard de tous les clients. Elles s’affichent pour chaque client de l’offre dans le flux d’achat. Les clients doivent accepter les conditions du contrat Standard et la ou les modifications avant de pouvoir utiliser votre offre. Vous pouvez fournir un seul amendement universel par offre. Vous pouvez entrer un nombre illimité de caractères dans cette zone. Ces termes sont affichés pour les clients dans AppSource, la Place de marché Azure et/ou le portail Azure pendant le processus de découverte et d’achat.

**Modifications personnalisées** : il s’agit de modifications spéciales apportées au contrat Standard qui ciblent certains clients via les ID de locataires Azure. Vous pouvez choisir le locataire que vous souhaitez cibler. Seuls les clients du locataire recevront les conditions personnalisées dans le flux d’achat de l’offre. Les clients doivent accepter les conditions du contrat Standard et la ou les modifications avant de pouvoir utiliser votre offre.

Commencez par sélectionner **Ajouter des conditions d’amendement personnalisées (10 max)** . Vous pouvez fournir jusqu’à dix conditions d’amendement personnalisées par offre.

- **Conditions d’amendement personnalisées** : saisissez vos propres conditions d’amendement dans la zone Conditions d’amendement personnalisées. Vous pouvez entrer un nombre illimité de caractères. Seuls les clients des ID de locataire que vous spécifiez pour ces conditions personnalisées pourront consulter ces conditions dans le flow d’achat de l’offre dans le portail Azure.
- **ID de locataire** (requis) : chaque amendement personnalisé peut être ciblé vers jusqu’à 20 ID de locataire. Si vous ajoutez un amendement personnalisé, vous devez fournir au moins un ID de locataire, qui identifie votre client dans Azure. Vous pouvez demander à votre client de rechercher cet ID dans la section Propriétés du portail Azure. La valeur de l’ID de répertoire est l’ID de locataire (par exemple, 50c464d3-4930-494c-963C-1e951d15360e). Vous pouvez également rechercher l’ID de locataire de l’organisation de votre client à l’aide de son adresse Web de nom de domaine sur [Qu’est-ce que mon Microsoft Azure et mon ID de locataire Office 365 ?](https://www.whatismytenantid.com/).
- **Description** (facultatif) : fournissez une description claire de l’ID de locataire, qui vous aide à identifier le client que vous ciblez avec cet amendement.

> [!NOTE]
> Ces deux types de modifications s’ajoutent l’un à l’autre. Les clients ciblés par les modifications personnalisées reçoivent aussi la modification universelle apportée au contrat Standard lors de l’achat.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="offer-listing"></a>Annonce de l’offre

Cette page vous permet de définir les détails de l’offre, notamment le nom de l’offre, la description, les liens et les contacts.

> [!NOTE]
> Il n’est pas nécessaire que le contenu du référencement de l’offre (par exemple, la description, les documents, les captures d’écran et les conditions d’utilisation) soit en anglais, tant que sa description commence par l’expression : « This application is available only in [langue autre que l’anglais]. » Vous pouvez également fournir une _adresse Web de lien utile_ pour offrir du contenu dans une langue autre que celle utilisée dans le contenu de la liste des offres.

### <a name="marketplace-details"></a>Détails de la Place de marché

#### <a name="name"></a>Nom

Le nom que vous entrez ici s’affiche auprès des clients comme titre de votre référencement d’offre. Ce champ est prérempli avec le texte que vous avez entré dans la zone **Alias d’offre** lors de la création de l’offre. Vous pouvez changer ce nom ultérieurement.

Le nom :

- Peut être une marque déposée (et vous pouvez inclure des symboles de marque et de copyright)
- Ne peut pas compter plus de 50 caractères
- Ne peut pas inclure d’emojis.

#### <a name="search-results-summary"></a>Résumé des résultats de recherche

Brève description de votre offre. Ce champ peut comporter jusqu’à 100 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

#### <a name="long-summary"></a>Résumé long

Fournissez une description plus détaillée de votre offre. Ce champ peut comporter jusqu’à 256 caractères, et est utilisé dans les résultats de recherche de la Place de marché.

#### <a name="description"></a>Description

Fournissez une description détaillée de votre offre, jusqu’à 3 000 caractères. S’affichera pour les clients dans la vue d’ensemble de la Place de marché commerciale.

Incluez un ou plusieurs des éléments suivants dans votre description :

- La valeur et les avantages principaux de votre offre
- Catégorie ou associations de secteurs, ou les deux
- Opportunités d’achats dans l’application
- Toutes les informations requises

Voici quelques conseils pour rédiger votre description :

- Décrivez clairement la proposition de valeur de votre offre dans les premières phrases de votre description. Incorporez les éléments suivants :
  - Description de votre offre.
  - Le type d’utilisateur qui tire parti de votre offre.
  - Les besoins ou problèmes auxquels l’offre répond.
- Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats du moteur de recherche.
- Ne comptez pas sur les fonctionnalités de votre offre pour convaincre. Concentrez-vous plutôt sur ce que votre application offre.
- Utilisez le vocabulaire propre au secteur ou des termes décrivant les avantages proposés.

Pour que la description de votre offre soit plus attrayante, utilisez l’éditeur de texte enrichi pour mettre en forme votre description. L’éditeur de texte enrichi vous permet d’ajouter des chiffres, des puces, des caractères gras/en italique et des mises en retrait pour rendre votre description plus lisible.

![Affiche la page de présentation de l’Espace partenaires avec le bouton Nouvelle offre et l’offre de services de conseil sélectionnée.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Lien de la politique de confidentialité

Entrez l’adresse web (URL) de la politique de confidentialité de votre organisation. Veillez à ce que votre offre respecte les lois et les réglementations en matière de confidentialité. Vous devez également publier une politique de confidentialité valide sur votre site Web.

### <a name="useful-links"></a>Liens utiles

Fournissez des documents en ligne complémentaires à propos de votre offre. Pour ajouter un lien, sélectionnez **+ Ajouter un lien**, puis complétez les champs suivants :

- **Nom** : les clients verront le nom sur la page des détails.
- **Lien (URL)**  : entrez un lien pour que les clients voient votre document en ligne.

### <a name="customer-support-links"></a>Liens de support clientèle

Indiquez le site Web permettant aux clients de contacter votre support technique.

- Site Web du support Azure Global
- Site Web du support Azure Government

### <a name="partner-support-contact"></a>Contact du support technique du partenaire

Fournissez les coordonnées des partenaires Microsoft à utiliser lorsque vos clients ouvrent un ticket de support. Ces informations n’apparaîtront pas dans la place de marché.

- Nom
- E-mail
- Téléphone

### <a name="engineering-contact"></a>Contact ingénierie

Fournissez les coordonnées que Microsoft doit utiliser en cas de problème avec votre offre, y compris les problèmes de certification. Ces informations n’apparaîtront pas dans la place de marché.

- Nom
- E-mail
- Téléphone

### <a name="marketplace-media"></a>Médias de la Place de marché

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format PNG. Votre demande sera rejetée si les images sont floues.

>[!Note]
>Si vous rencontrez un problème de chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

#### <a name="marketplace-logos"></a>Logos dans la Place de marché

Fournissez des fichiers PNG du logo de votre offre dans les quatre tailles en pixels suivantes :

- **Petit** (48 x 48)
- **Moyen** (90 x 90)
- **Grand** (216 x 216)
- **Large** (255 x 115)

Les quatre logos sont requis et sont utilisés à différents endroits de l’annonce de la Place de marché.

#### <a name="screenshots"></a>Captures d’écran.

Ajoutez jusqu’à cinq captures d’écran qui illustrent le fonctionnement de votre offre. Chaque capture d’écran doit avoir une taille de 1280 x 720 pixels et être au format PNG. Vous devez également ajouter une légende qui décrit votre capture d’écran.

#### <a name="videos"></a>Videos

Ajoutez jusqu’à cinq vidéos qui illustrent votre offre. Elles doivent être hébergées sur un service vidéo externe. Entrez le nom de chaque vidéo, l’adresse web et une image miniature PNG de 1280 x 720 pixels de la vidéo.

Pour obtenir des ressources supplémentaires concernant la place de marché, consultez [Meilleures pratiques pour les annonces d’offre d’une place de marché](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="preview"></a>PRÉVERSION

Sous l’onglet Préversion, choisissez de restreindre le **public de préversion** en vue de valider votre offre avant de la publier plus largement en ligne sur la Place de marché.

> [!IMPORTANT]
> Après avoir vérifié votre offre en Préversion, sélectionnez **Publier** pour publier votre offre sur la place de marché commerciale.

Le public de votre préversion est identifié par des ID (GUID) d’abonnements Azure, avec une description facultative de chacun. Aucun de ces champs ne peut être vu par les clients. Vous trouverez votre ID d’abonnement Azure dans la page **Abonnements** du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, soit séparément (jusqu’à 10), soit en chargeant un fichier CSV (jusqu’à 100). En ajoutant ces ID d’abonnement, vous définissez qui peut afficher un aperçu de votre offre avant qu’elle ne soit publiée en ligne. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de test de changements ou de mises à jour futurs de l’offre.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de préversion peut accéder à votre offre _avant_ sa publication sur les places de marché. Il peut voir et valider tous les plans, y compris les plans qui seront disponibles uniquement pour un public privé une fois que votre offre sera entièrement publiée sur la Place de marché. Un public privé (défini dans l’onglet **Tarification et disponibilité** du plan) dispose d’un accès exclusif à un plan particulier.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, Vue d’ensemble du plan.

## <a name="plan-overview"></a>Vue d’ensemble du plan

Vous pouvez proposer différentes options de plan dans la même offre au sein de l’Espace partenaires. Ces plans étaient auparavant appelés références SKU. Une offre requiert au moins un plan, qui peut différer en termes de public de monétisation, de régions Azure, de fonctionnalités ou d’images de machines virtuelles.

Une fois que vous avez créé vos plans, l’onglet **Vue d’ensemble du plan** affiche les informations suivantes :

- Noms des plans
- Modèles de licences
- Audience (publique ou privée)
- État actuel de la publication
- Actions disponibles

Les actions disponibles dans Vue d’ensemble du plan varient selon l’état actuel de votre plan. Ils comprennent :

- **Supprimer le brouillon** : si l’état du plan est Brouillon
- **Arrêter la vente du plan** ou **Synchroniser le public privé** : si l’état du plan est Publié

### <a name="create-new-plan"></a>Créer un plan

Sélectionnez **+ Créer un plan** en haut de la page. La boîte de dialogue **Nouveau plan** s’affiche.

Dans la zone **ID du plan**, créez un ID de plan unique pour chaque plan contenu dans cette offre. Cet ID sera visible par les clients dans l’adresse web du produit. Utilisez uniquement des lettres minuscules et des chiffres, des tirets ou des traits de soulignement, dans la limite de 50 caractères.

> [!NOTE]
> L’ID de plan ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Dans la zone **Nom du plan**, entrez un nom pour ce plan. Les clients voient ce nom quand ils décident du plan à sélectionner dans votre offre. Créez un nom unique qui identifie clairement les différences de chaque plan. Par exemple, vous pouvez utiliser **Windows Server** avec des plans **Paiement à l'utilisation**, **BYOL (apportez votre propre licence)** , **Avancé** et **Entreprise**.

Sélectionnez **Create** (Créer).

### <a name="plan-setup"></a>Configuration du plan

Définissez la configuration générale du type de plan, si le plan réutilisera la configuration technique d’un autre plan, et dans quelles régions Azure le plan sera disponible. Les options que vous choisissez ici déterminent les champs affichés dans les autres onglets du même plan.

#### <a name="reuse-technical-configuration"></a>Réutiliser la configuration technique

Si vous avez plusieurs plans du même type et que les packages qu’ils utilisent sont identiques, vous pouvez sélectionner l’option pour que **ce plan réutilise la configuration technique d’un autre plan**. Cette option vous permet de sélectionner l’un des autres plans du même type pour cette offre et de réutiliser sa configuration technique.

> [!NOTE]
> Si vous réutilisez la configuration technique d’un autre plan, l’onglet **Configuration technique** ne s’affiche plus pour ce plan. En effet, tous les paramètres de la configuration technique de l’autre plan, y compris les mises à jour effectuées ultérieurement, seront également appliqués à ce plan. Ce paramètre n’est plus modifiable après la publication du plan.

#### <a name="azure-regions"></a>Régions Azure

Votre plan doit être rendu disponible dans au moins une région Azure.

Sélectionnez l’option **Azure Global** pour proposer votre plan aux clients dans toutes les régions Azure publiques intégrées à la Place de marché commerciale. Pour plus d’informations, consultez [Prise en charge de la disponibilité géographique et des devises](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Sélectionnez l’option **Azure Government** pour proposer votre plan dans la région [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). Cette région fournit un accès contrôlé aux clients d’entités gouvernementales fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’aux partenaires éligibles pour les servir. En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires. Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement).

Avant de le publier dans [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), testez et validez votre plan dans l’environnement, car certains points de terminaison peuvent être différents. Pour configurer et tester votre plan, demandez un compte d’essai depuis la page [Microsoft Azure Government Trial](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Une fois votre plan publié et disponible dans une région Azure spécifique, vous ne pouvez pas supprimer cette région.

#### <a name="azure-government-certifications"></a>Certifications Azure Government

Cette option est visible uniquement si vous sélectionnez **Azure Government** sous **Régions Azure**.

Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales. Par exemple, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS. Afin de renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens décrivant ces certifications. Ces liens peuvent être des liens vers votre liste de plans directement sur le programme, ou des liens vers des descriptions de votre conformité avec ces certifications sur vos propres sites web. Ces liens sont visibles uniquement par les clients Azure Government.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="plan-listing"></a>Liste des plans

C’est ici que vous configurez les détails de la liste du plan. Cet onglet affiche des informations spécifiques à chaque plan au sein de la même offre.

#### <a name="plan-name"></a>Nom du plan

Ce champ est pré-rempli avec le nom que vous avez donné à votre plan quand vous l’avez créé. Ce nom apparaît dans la place de marché en tant que titre de ce plan et est limité à 100 caractères.

#### <a name="plan-summary"></a>Résumé du plan

Fournissez un bref résumé de votre plan (pas de l’offre). Ce résumé est limité à 100 caractères.

#### <a name="plan-description"></a>Description du plan

Décrivez ce qui rend ce plan logiciel unique, ainsi que les différences entre les plans de votre offre. Ne décrivez pas l’offre, mais simplement le plan. La description du plan peut contenir jusqu’à 2 000 caractères.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="pricing-and-availability"></a>Tarification et disponibilité

Dans cet onglet, vous allez configurer les éléments suivants :

- Les marchés dans lesquels ce plan sera disponible
- Le tarif horaire
- Vous pouvez indiquer si vous voulez que le plan soit visible à tous ou uniquement à des utilisateurs spécifiques (un public privé)

#### <a name="markets"></a>Marchés

Chaque plan doit être disponible sur au moins un marché. Cochez la case pour chaque marché où ce plan doit être disponible à l’achat (les utilisateurs de ces marchés peuvent toujours déployer l’offre dans toutes les régions Azure sélectionnées dans **[Configuration du plan](#plan-setup)** ). Le bouton **Tax remitted** (reversant les taxes) indique les pays dans lesquels Microsoft reverse les taxes en votre nom. La publication en Chine se limite aux plans **gratuits** ou de type **Bring your own license** (BYOL).

Si vous avez déjà fixé des prix pour votre plan en dollar américain (USD) et ajouté un autre emplacement de marché, le prix de ce nouveau marché sera calculé selon le taux de change en vigueur. Vérifiez toujours le prix de chaque place de marché avant toute publication. Vérifiez les prix à l’aide du lien **Exporter les prix (xlsx)** après avoir enregistré vos changements.

Lorsque vous supprimez un marché, les clients de ce marché utilisant des déploiements actifs ne pourront pas créer de nouveaux déploiements ou mettre à l’échelle leurs déploiements existants. Les déploiements existants ne seront pas affectés.

#### <a name="pricing"></a>Tarifs

**Modèle de licence** ; sélectionnez **Usage-based monthly billed plan** (Plan avec facturation mensuelle à l’utilisation) afin de configurer les prix de ce plan, ou **Bring your own license** (BYOL (apportez votre propre licence)) pour permettre aux clients d’utiliser ce plan avec leur licence existante.

Pour un plan avec facturation mensuelle à l’utilisation, utilisez l’une des trois options de saisie des tarifs suivantes :

- **Par base** : indiquez le prix par base en dollars américains (USD). Nous calculerons le prix par taille de base et le convertirons dans les devises locales en appliquant le taux de change actuel.
- **Par taille de base** : indiquez les prix par taille de base en USD. Nous convertirons les prix dans les devises locales en appliquant le taux de change actuel.
- **Par marché et taille de base** : indiquez les prix de chaque taille de base pour tous les marchés. Vous pouvez importer des prix à partir d’une feuille de calcul.

> [!NOTE]
> Enregistrez les modifications de la tarification pour autoriser l’exportation des données de tarification. Une fois qu’un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement. Vérifiez ces prix avant la publication en exportant la feuille de calcul de tarification et en vérifiant le prix de chaque marché.

#### <a name="free-trial"></a>Essai gratuit

Vous pouvez proposer à vos clients une version d’évaluation gratuite d’un mois ou de trois mois.

#### <a name="visibility"></a>Visibilité

Vous pouvez concevoir chaque plan pour qu’il soit visible par tous les utilisateurs ou uniquement pour un public présélectionné. Définissez l’appartenance à ce public restreint à l’aide des ID d’abonnement Azure.

**Public** : votre plan peut être consulté par tout le monde.

**Public privé** : votre plan ne peut être consulté que par un public présélectionné. Une fois votre plan publié en tant que plan privé, vous pouvez mettre à jour le public visé ou rendre le plan public. Une fois rendu public, le plan doit rester public ; vous ne pouvez plus le rendre privé.

**Public restreint (ID d’abonnements Azure)**  : définissez le public qui aura accès à ce plan privé à l’aide d’ID d’abonnements Azure. Le cas échéant, incluez une description de chaque ID d’abonnement Azure que vous avez attribué. Ajoutez jusqu’à 10 ID d’abonnements manuellement, ou 20 000 ID si vous importez une feuille de calcul CSV. Les ID d’abonnements Azure sont représentés par des GUID, où les lettres doivent être en minuscules.

> [!NOTE]
> Un public privé ou restreint est différent du public de préversion que vous avez défini sous l’onglet **Préversion**. Un public de préversion peut accéder à votre offre _avant_ sa publication sur la place de marché. Tandis que la sélection d’un public privé ne s’applique qu’à un plan spécifique, le public de préversion peut voir tous les plans (privés ou non) dans le cadre de la validation.

#### <a name="hide-plan"></a>Masquer le plan

Si votre comptez utiliser votre machine virtuelle uniquement de manière indirecte quand elle est référencée par un autre modèle de solution ou une application managée, cochez cette case pour publier votre machine virtuelle tout en la masquant pour les clients qui essaient de la rechercher et d’y accéder directement.

> [!NOTE]
> Les plans masqués ne prennent pas en charge les liens de préversion.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="technical-configuration"></a>Configuration technique

Fournissez les images et autres propriétés techniques associées à ce plan. Pour plus d’informations, consultez [Créer une ressource technique de machine virtuelle Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Cet onglet ne s’affiche pas si vous avez configuré ce plan pour réutiliser les packages d’un autre plan sous l’onglet **Configuration du plan**.

#### <a name="operating-system"></a>Système d’exploitation

- **Famille de systèmes d'exploitation** : sélectionnez le système d'exploitation **Windows** ou **Linux**
- **Mise en production** ou **Fournisseur** : choisissez la mise en production Windows ou le fournisseur Linux
- **Nom convivial du système d’exploitation** : choisissez un nom de système d’exploitation convivial. Ce nom est visible par les clients

#### <a name="recommended-vm-sizes"></a>Tailles de machines virtuelles recommandées

Sélectionnez jusqu’à six tailles de machine virtuelle recommandées à afficher dans la Place de marché Azure.

#### <a name="open-ports"></a>Ouvrir les ports

Ouvrez des ports publics ou privés sur une machine virtuelle déployée.

#### <a name="storage-option-for-deployment"></a>Option de stockage pour le déploiement

**Option de déploiement de disque** : sélectionnez le type de déploiement de disque que vos utilisateurs peuvent utiliser lors de l’utilisation de la machine virtuelle. Microsoft recommande de limiter le déploiement aux disques managés uniquement.

#### <a name="properties"></a>Propriétés

**Prendre en charge la mise en réseau accélérée** : indiquez si votre machine virtuelle prend en charge la [mise en réseau accélérée](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Images de machine virtuelle

Fournissez une version de disque et l’URI SAS pour les images de machine virtuelle. Ajoutez jusqu’à 16 disques de données pour chaque image de machine virtuelle. Fournissez une seule nouvelle version d’image par plan à chaque envoi. Lorsqu’une image a été publiée, vous ne pouvez pas la modifier, mais vous pouvez la supprimer. La suppression d’une version empêchera les utilisateurs nouveaux et existants de déployer une nouvelle instance de la version supprimée.

- **Version du disque** représente la version de l’image que vous fournissez.
- **URI SAS** représente l’emplacement dans Stockage Azure où vous avez stocké le disque dur virtuel du système d’exploitation.
- Les images de disque de données sont également des URI SAS VHD stockés dans leur stockage Azure.
- Ajoutez une seule image par envoi dans un plan.

Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données requis par la solution. Les clients ne suppriment pas les disques qui font partie d’une image lors du déploiement, mais peuvent toujours ajouter des disques pendant ou après le déploiement.

Sélectionnez **Enregistrer le brouillon** avant de continuer, puis revenez à la **vue d’ensemble du plan**.

## <a name="resell-through-csps"></a>Revendre via des fournisseurs de solutions cloud

Développez la portée de votre offre en la mettant à la disposition des partenaires du programme [fournisseur de solutions cloud](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Tous les abonnements BYOL (apportez votre propre licence) sont automatiquement souscrits ; vous pouvez choisir de souscrire des abonnements non-BYOL.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="test-drive"></a>Test drive

Configurez une démonstration (version d'évaluation) permettant aux clients d’évaluer votre offre avant de l’acheter. Pour créer un environnement de démonstration permettant aux clients d’évaluer votre offre pendant une période fixe, consultez [Évaluer votre offre sur la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Pour activer une version d'évaluation, cochez la case Activer une version d'évaluation dans l’onglet [Configuration de l’offre](#test-drive). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

Ressources supplémentaires pour version d’évaluation :

- Meilleures pratiques techniques
- Meilleures pratiques marketing
- Vue d’ensemble des versions d'évaluation (PDF ; assurez-vous que votre bloqueur de fenêtres publicitaires est désactivé).

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="review-and-publish"></a>Vérifier et publier

Une fois que vous avez rempli toutes les sections requises de l’offre, vous pouvez l’envoyer pour révision et publication.

Dans le coin supérieur droit du portail, sélectionnez **Vérifier et publier**.

Dans cette page, vous pouvez :

- Consultez l’état d’achèvement de chaque section de l’offre.
  - **Non démarrée** : la section n’a pas été démarrée et doit être complétée.
  - **Incomplète** : la section comporte des erreurs qui doivent être corrigées ou nécessite plus d’informations de votre part. Consultez les sections plus haut dans ce document pour plus de détails sur la mise à jour de cette section.
  - **Complète** : la section est complète et ne comporte aucune erreur. Toutes les sections de l’offre doivent être complètes pour que vous puissiez envoyer l’offre.
- **Notes pour la certification** : fournissez des instructions de test à l’équipe de certification pour vous assurer que votre application est correctement testée. Fournissez toutes les notes supplémentaires utiles pour comprendre votre demande.

Pour envoyer l’offre à des fins de publication, sélectionnez **Vérifier et publier**.

Nous vous enverrons un e-mail pour vous faire savoir si une préversion de l’offre est disponible pour vérification et approbation. Pour que votre offre soit publiée pour le public (ou pour un public privé, si elle est privée), accédez à l’Espace partenaires, recherchez la page de **présentation** de votre offre, puis sélectionnez **Mise en service**. L’état de votre offre apparaîtra en haut de la page lorsque la publication est en cours.

### <a name="errors-and-review-feedback"></a>Erreurs et commentaires de révision

L’étape de **validation manuelle** du processus de publication représente une révision complète de votre offre et de ses ressources techniques associées. Si ce processus détecte des erreurs dans offre, vous recevrez un rapport de certification détaillant les problèmes. Il vous suffit alors d’apporter les corrections nécessaires puis de republier votre offre.

## <a name="offer-overview"></a>Vue d’ensemble de l’offre

La page **Vue d’ensemble de l’offre** affiche une représentation visuelle des étapes nécessaires à la publication de cette offre (à la fois terminées et en cours) et indique la durée d’exécution de chaque étape.

Cette page contient des liens permettant d’effectuer des opérations sur cette offre en fonction de la sélection que vous effectuez. Par exemple :

- Si l’offre est un brouillon : [Supprimer le brouillon de l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Si l’offre est publiée : [Arrêter de vendre l’offre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Si l’offre est disponible en préversion : [Mise en service](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Si vous n’avez pas complété Déconnexion de l’éditeur : [Annuler la publication](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Exemples de la Place de marché

Ces exemples montrent comment l’offre apparaît dans la Place de marché Azure.

### <a name="azure-marketplace-offer-details"></a>Détails de l’offre dans la Place de marché Azure

![Exemple d’écran des détails de l’offre dans la Place de marché Azure](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Résultats de recherche de la Place de marché Azure

![Exemple d’écran des détails de la recherche dans la Place de marché Azure](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Détails du plan dans la Place de marché Azure

![Exemple d’écran des détails du plan dans la Place de marché Azure](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Détails de l’offre dans le portail Azure

![Exemple d’écran des détails de l’offre dans le portail Azure](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Résultats de la recherche du Portail Azure

![Exemple d’écran des résultats de la recherche dans le portail Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Détails du plan dans le portail Azure

![Exemple d’écran des détails du plan dans le portail Azure](media/avm-create6.png)

## <a name="next-step"></a>Étape suivante

- [Mettre à jour une offre existante dans la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
