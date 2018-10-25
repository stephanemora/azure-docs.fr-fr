---
title: Créer une offre de machine virtuelle | Microsoft Docs
description: Publiez une machine virtuelle sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 3b046022990e95e65ed02880bd3fefbd78bcad28
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387352"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Publier une machine virtuelle sur la Place de marché Microsoft Azure

Cet article présente les étapes à suivre pour publier une offre de machine virtuelle sur la Place de marché Azure.

## <a name="prerequisites"></a>Prérequis

Les prérequis techniques et non techniques suivants s’appliquent à la publication d’une machine virtuelle sur la Place de marché Azure

### <a name="technical"></a>Techniques

-   [Prérequis techniques pour créer une image de machine virtuelle pour la Place de marché Azure](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Créer et charger un disque dur virtuel (VHD) Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Créer et tester une machine virtuelle Linux à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Créer et charger un disque dur virtuel (VHD) Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Créer et tester une machine virtuelle Windows à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Comment résoudre les problèmes courants rencontrés lors de la création d’un disque dur virtuel](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Recommandations de sécurité pour les images Place de marché Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>Non techniques (critères de l’entreprise)

 -   Votre entreprise (ou sa filiale) est située dans un pays expéditeur pris en charge par la Place de marché Azure.

-   Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché Azure.

-   Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.

-   Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.

-   Vous fournissez un contenu qui répond aux critères définis pour que votre offre soit répertoriée sur la Place de marché Azure et sur le portail de gestion Azure.

-   Vous acceptez les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de marché Azure.

-   Vous vous engagez à respecter les [Conditions d’utilisation](https://azure.microsoft.com/support/legal/website-terms-of-use/), la [Déclaration de confidentialité Microsoft](http://www.microsoft.com/privacystatement/default.aspx) et le [Contrat relatif au programme Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="before-you-begin"></a>Avant de commencer

Une fois que vous avez réuni tous les prérequis, vous pouvez commencer à créer votre offre de modèle de solution. Avant de commencer, examinez les informations ci-dessous concernant l’offre et la référence SKU.

**Offre**

Une offre Azure Application correspond à une classe d’offre de produit d’un éditeur. Si vous voulez mettre à disposition un nouveau type de solution/application sur la Place de marché Azure, le meilleur moyen est de créer une nouvelle offre. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché Microsoft Azure.

**Référence (SKU)**

Une référence (SKU) est la plus petite unité achetable d’une offre. À l’intérieur d’une même classe de produit (offre), les références SKU vous permettent de faire la distinction entre les différentes fonctionnalités prises en charge. Par exemple, l’offre est managée ou non managée et différents modèles de facturation sont pris en charge.

Ajoutez plusieurs références SKU dans les scénarios suivants :
- Vous pouvez prendre en charge différents modèles de facturation, tels que BYOL (apportez votre propre licence) ou PAYG (payez au fur et à mesure).
- Chaque référence SKU prend en charge un ensemble différent de fonctionnalités et chaque ensemble de fonctionnalités n’affiche pas le même tarif.

Une référence SKU apparaît sous l’offre parente sur la Place de marché Azure, tandis qu’elle se présente comme une entité achetable en tant que telle sur le portail Azure.

## <a name="to-create-a-new-offer"></a>Pour créer une offre

1.  Connectez-vous au [portail Cloud Partner](http://cloudpartner.azure.com/).

2.  Dans la barre de navigation de gauche, sélectionnez **+ Nouvelle offre**, puis **Machines Virtuelles**.

    ![Nouvelle offre pour machines virtuelles](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  Sous **Nouvelle offre**, sélectionnez **Éditeur**.

![Nouvelle offre Éditeur](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  Sous **Éditeur**, vous allez fournir des informations dans les vues suivantes :
    - Paramètres de l’offre
    - Références (SKU)
    - Marketplace
    - Support Chaque vue contient un ensemble de champs que vous devez remplir. Les champs obligatoires sont indiqués par un astérisque rouge (\*).

## <a name="to-configure-offer-settings"></a>Pour configurer les paramètres de l’offre

1. Configurez les champs **Identité de l’offre** ci-dessous dans les paramètres de l’offre.

    **ID de l’offre**

     Identificateur unique de l’offre au sein d’un profil d’éditeur. Cet ID est visible dans les URL de produits, les modèles Azure Resource Manager et les rapports de facturation. Vous ne pouvez utiliser que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret et ne peut pas comporter plus de 50 caractères. Par exemple, si l’éditeur **contoso** publie une offre avec l’ID d’offre **sample-vm**, il se présente dans la Place de marché Azure comme suit : **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
    >[!Note]
    >Ce champ est verrouillé une fois que l’offre est en ligne.

    **ID de l’éditeur**

    Liste déroulante pour le profil d’éditeur. Choisissez le profil sous lequel vous voulez publier l’offre. 
    >[!Note]
    >Ce champ est verrouillé une fois que l’offre est en ligne.

    **Nom**

    Nom d’affichage de votre offre. Ce nom apparaît sur la Place de marché Azure et sur le portail Azure. Il ne peut pas comporter plus de 50 caractères. Suivez les conseils ci-dessous pour le nom de l’offre :
    -  Incluez un nom de marque reconnaissable pour votre produit. 
    - N’incluez pas ici le nom de votre entreprise, sauf si l’offre est commercialisée de cette façon.
    - Si vous commercialisez cette offre sur votre propre site web, veillez à ce que le nom soit identique à celui qui apparaît sur votre site web.

2. Sélectionnez **Enregistrer** pour finaliser les paramètres de l’offre.

## <a name="to-create-a-sku"></a>Pour créer une référence SKU

1. Sélectionnez **Références**. 
2. Sélectionnez **Ajouter une référence** pour entrer un ID de référence SKU. L’ID de référence SKU correspond à l’identificateur unique d’une référence SKU au sein d’une offre. Cet ID est visible dans les URL de produits, les modèles Azure Resource Manager et les rapports de facturation. L’ID de référence SKU :
    - Ne peut pas comporter plus de 50 caractères.
    - Ne peut être constitué que de caractères alphanumériques en minuscules ou de tirets (-).
    - L’ID ne peut pas se terminer par un tiret.

    ![Ajouter une référence SKU](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>Configurer les détails d’une référence SKU

Après avoir ajouté une référence SKU, celle-ci apparaît dans la liste des références SKU dans la vue Références. Pour afficher les détails d’une référence SKU, sélectionnez son nom. Vous pouvez utiliser la vue détaillée pour ajouter des informations dans les champs suivants.

### <a name="hide-this-sku"></a>Masquer cette référence (SKU)

Ce paramètre permet de gérer la visibilité de la référence SKU. Si « Masquer cette référence » est désactivé, la référence SKU est visible sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/). Vous pouvez choisir de masquer la référence (SKU) si vous souhaitez qu’elle soit disponible via des modèles de solution, mais pas en tant qu’achat individuel.

### <a name="cloud-availability"></a>Disponibilité dans le cloud

Ce champ vous permet de définir la disponibilité de votre référence (SKU) dans les différents clouds Azure.

**Azure public**

Cette référence SKU peut être déployée pour les clients dans toutes les régions Azure publiques intégrées à la Place de marché.

**Azure Government Cloud**

Cette référence peut être déployée dans l’Azure Government Cloud. Avant de publier sur [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), nous recommandons aux éditeurs de tester et valider que leur solution fonctionne comme prévu. Pour effectuer une copie intermédiaire et tester, [demandez un compte d’essai](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>Microsoft Azure Government est un cloud communautaire pour le secteur public, accessible à des clients d’administrations fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’à des partenaires éligibles pour servir ces entités.

### <a name="countryregion-availability"></a>Disponibilité par pays/région

Ce champ identifie les régions dans lesquelles votre référence SKU sera disponible à l’achat. Vous devez considérer soigneusement celles dans lesquelles vous rendrez vos références disponibles. Certains pays sont classés « Microsoft Tax Remitted Country ».

-   Dans les pays « Microsoft Tax Remitted Country », Microsoft collecte des taxes auprès des clients et les reverse à l’État.

-   Dans les autres pays, les partenaires sont chargés de collecter les taxes et de les reverser à leur État. Si vous choisissez de vendre dans ces pays, vous devez avoir la capacité de calculer ces taxes et de les y reverser.

![Sélectionner la disponibilité par pays/région](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>Tarifs

Deux modèles tarifaires sont actuellement pris en charge.

#### <a name="bring-your-own-license-byol"></a>BYOL (apportez votre propre licence)

Vous gérez la licence du logiciel en cours d’exécution sur la machine virtuelle. Microsoft facture uniquement les coûts d’infrastructure.

#### <a name="usage-based-monthly-billed-sku"></a>Référence (SKU) facturée mensuellement sur la base de l’utilisation

Les clients sont facturés à l’heure sur la base des tarifs définis par les éditeurs en fonction des tailles de machines virtuelles. Dans le cas du modèle de **facturation horaire** des références, le prix total est la somme du coût logiciel facturé par l’éditeur et du coût d’infrastructure facturé par Microsoft. Ce coût total s’affiche au client sous la forme d’un tarif horaire et d’un prix mensuel au moment où il envisage l’achat. Dans ce cas, la facturation est effectuée mensuellement.

Dans le modèle basé sur l’utilisation, vous fixez des paramètres supplémentaires.

**Évaluation gratuite**

Vous pouvez spécifier si vous souhaitez proposer un essai gratuit à vos clients.
Ici, le client n’est pas facturé pour le coût du logiciel pendant les 30/90 premiers jours (en fonction de l’option sélectionnée) après le déploiement de la machine virtuelle. À l’issue de la période d’essai gratuit, le client est facturé à l’heure en fonction des tarifs définis par les éditeurs dans le modèle horaire.

**Tarification par cœur**

Vous pouvez définir une tarification par cœur pour votre référence (SKU). Dans ce cas, vous devez simplement entrer un prix de base pour un seul cœur. Ensuite, nous calculons automatiquement le prix pour le reste des cœurs. Entrez les prix en USD sur le portail. Nous calculons automatiquement les prix pour les autres régions. Vous pouvez vérifier les prix dans les autres régions en **exportant les données de tarification**.

![Tarification par cœur](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**Tarification distincte**

Vous pouvez définir la tarification individuellement pour chaque ensemble de cœurs si vous souhaitez tarifer chaque cœur séparément.

![Tarification distincte](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**Exporter/Importer la tarification**

Vous avez la possibilité d’exporter la tarification configurée via le portail pour apporter des modifications dans l’interface Excel. Cela vous permet également de vérifier la tarification par région et la tarification dans les devises locales.
L’option d’**exportation de la tarification** vous permet de télécharger un fichier Excel dans lequel les détails de la tarification sont renseignés. Vous pouvez modifier ceux-ci dans Excel, puis utiliser l’option d’**importation de la tarification** pour importer les modifications apportées.
La tarification importée s’affiche également sur le portail.

Dans cette tarification Excel, les prix pour les différentes régions sont indiqués dans la devise locale. Le taux de change que nous utilisons est actualisé quotidiennement.

![Exportation/importation de la tarification avec des exemples de taux de change](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   Les prix ne peuvent plus être modifiés une fois que l’offre est en ligne. Cependant, vous pouvez toujours ajouter ou supprimer des régions desservies.
>-   Ce prix est facturé à l’utilisateur et s’ajoute au [tarif des machines virtuelles Azure\'](http://aka.ms/vmpricingdetails).
>-   Les prix sont définis pour toutes les régions en devise locale en utilisant les taux de change disponibles au moment de leur établissement.
>-   Pour définir ou afficher individuellement le prix d’une région déterminée, exportez la feuille de calcul des tarifs, puis importez-la avec les tarifs personnalisés.

## <a name="vm-images"></a>Images de machine virtuelle

La section suivante est celles consacrée aux Images de machine virtuelle. Avant de passer à cette section, vous devez avoir préparé le disque dur virtuel que vous souhaitez publier. Voici quelques liens pour vous aider à créer votre disque dur virtuel :

-   [Prérequis techniques pour créer une image de machine virtuelle pour la Place de marché Azure](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Création et chargement d’un disque dur virtuel Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Créer et tester une machine virtuelle Linux à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Création et chargement d’un disque dur virtuel Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Créer et tester une machine virtuelle Windows à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Comment résoudre les problèmes courants rencontrés lors de la création d’un disque dur virtuel](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

Une fois votre disque dur virtuel prêt, vous pouvez commencer à compléter cette section.
Voici quelques détails pour certains champs.

### <a name="recommended-vm-sizes"></a>Tailles de machines virtuelles recommandées

Sélectionnez jusqu’à six tailles de machine virtuelle recommandées. Ces recommandations sont présentées à l’utilisateur sur la Place de marché Microsoft Azure et dans le panneau Niveau tarifaire sur le portail Azure s’il décide d’acheter et de déployer votre image. **Il s’agit uniquement de recommandations. Le client peut sélectionner n’importe quelle taille de machine virtuelle prenant en charge les disques spécifiés dans votre image.**  La capture d’écran suivante montre les tailles de machines virtuelles recommandées qui sont présentées aux clients sur le portail Azure.


![Tailles de machines virtuelles recommandées](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>Ouvrir des ports

Spécifiez les ports que vous voulez ouvrir et rendre disponibles. Ces ports sont ouverts pendant ce déploiement de machines virtuelles.

### <a name="adding-vm-images"></a>Ajout d’images de machine virtuelle

L’étape suivante consiste à ajouter une image de machine virtuelle pour votre référence SKU. Vous pouvez ajouter jusqu’à 8 versions disque par référence (SKU). Seul le numéro de version disque le plus élevé pour une référence (SKU) particulière apparaît sur la Place de marché Microsoft Azure. Les autres sont visibles via des API.

Sous **Version du disque**, sélectionnez **+ Nouvelle version**. Les champs ci-dessous s’affichent ; vous devez les compléter.

#### <a name="vm-image-version"></a>Version image de machine virtuelle

La version image de machine virtuelle doit suivre le format [version sémantique](http://semver.org/). Les versions doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Les images contenues dans des références SKU différentes peuvent avoir différentes versions majeures et mineures. Les versions au sein d’une même référence SKU ne doivent impliquer que des modifications incrémentielles, qui augmentent la version du correctif (Z de X.Y.Z).

#### <a name="os-vhd-url"></a>URL DU DISQUE DUR VIRTUEL DU SYSTÈME D’EXPLOITATION

Entrez l’[URI de signature d’accès partagé](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) créé pour le disque dur virtuel du système d’exploitation.

Si des disques de données sont associés à cette référence SKU, vous pouvez les ajouter en sélectionnant le lien **+ Nouveau disque de données**. Cette action affiche des champs supplémentaires à compléter.

#### <a name="lun-vhd-url"></a>URL DU DISQUE DUR VIRTUEL DU NUMÉRO D’UNITÉ LOGIQUE (LUN)

Entrez l’[URI de signature d’accès partagé](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) pour votre disque de données.

#### <a name="lun-number"></a>Numéro de LUN

Attribuez un numéro à ce LUN. Ce numéro est réservé à ce disque de données dans cette référence SKU.

>[!Note]
>Une fois que vous avez publié une référence SKU avec une version de machine virtuelle spécifique et des disques de données, ces éléments sont verrouillés et ne peuvent plus être modifiés. Pour les versions de machine virtuelle supplémentaires éventuellement ajoutées à la référence SKU, le nombre de disques de données à prendre en charge ne peut pas changer.

#### <a name="common-sas-url-issues--fixes"></a>Problèmes communs avec les URL SAP et résolution

| Problème                                                                 | Message                                                                           | Correctif                                                           |  Lien vers la documentation                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| Échec lors de la copie d’images : « ? » est introuvable dans l’URL SAP                | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAP fournie.       | Mettez à jour l’URL SAP avec les outils recommandés                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Échec lors de la copie d’images : les paramètres « st » et « se » ne sont pas présents dans l’URL SAP   | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAP fournie.        | Mettez à jour l’URL SAS en ajoutant les dates de début et de fin             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Échec lors de la copie d’images : « sp=rl » n’est pas présent dans l’URL SAS                    | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAP fournie         | Mettez à jour l’URL SAP avec les autorisations définies sur « Lecture » et « Liste »     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Échec lors de la copie d’images : l’URL SAP contient des espaces blancs dans le nom du disque dur virtuel     | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAP fournie.        | Mettez à jour l’URL SAS sans espaces blancs                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Échec lors de la copie d’images : erreur d’autorisation d’URL SAP               | Échec : copie d’images. Impossible de télécharger l’objet blob en raison d’une erreur d’autorisation     | Générez à nouveau l’URL SAP                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Pour configurer la Place de marché

Utilisez la vue Place de marché pour configurer les champs qui s’affichent pour l’offre sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID d’abonnement pour version préliminaire

Il s’agit de la liste des ID d’abonnement Azure dont vous voulez qu’ils aient accès à l’offre dès qu’elle est publiée. Ces abonnements autorisés vous permettent de tester l’offre préliminaire avant sa publication. Le portail Cloud Partner vous permet d’autoriser jusqu’à 100 abonnements.

### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste fournie jusqu’à 5 catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

Les exemples suivants présentent les informations de place de marché qui s’affichent sur la Place de marché Azure et le portail Azure.

**Place de marché Azure**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Portail Azure**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>Instructions concernant le logo

Suivez ces instructions pour les logos chargés sur le portail Cloud Partner :

-   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.

-   Les couleurs de thème du portail Azure sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan de vos logos. Utilisez des couleurs qui mettent vos logos en vue sur le portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples.

    >[!Note] 
    >Si vous utilisez un arrière-plan transparent, assurez-vous que le logo/texte n’est pas blanc, noir ou bleu.

-   N'utilisez pas d’arrière-plan dégradé sur le logo.

-   Évitez de placer du texte sur le logo. Cela vaut aussi pour le nom de votre entreprise ou de votre marque. L’apparence de votre logo doit être *plate* et éviter les dégradés.

-   Le logo ne doit pas être étiré.

#### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas charger de bannière. En revanche, une fois que le logo est chargé, il ne peut plus être supprimé. Le partenaire doit suivre les instructions de la Place de marché Azure pour les icônes Bannière.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Recommandations concernant l’icône Bannière

-   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en utilisant une police de couleur blanche. Évitez d’utiliser une couleur claire dans l’arrière-plan. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes Bannière.

-   Le nom d’affichage de l’éditeur, le titre du plan, le résumé long de l’offre et le bouton Créer sont incorporés par programmation à l’intérieur de la bannière quand l’offre est publiée. N’entrez pas de texte au moment de concevoir la bannière. Laissez un espace vide à droite du logo. Cet espace doit faire 415 x 100 pixels et être décalé de 370 px à partir de la gauche.

![Exemple d’icône de bannière](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>Gestion des prospects

Pour configurer les paramètres de gestion des prospects de l’offre, suivez [ces instructions](./cloud-partner-portal-get-customer-leads.md).

## <a name="to-configure-support"></a>Pour configurer le support

Utilisez la vue Support pour fournir les informations suivantes :

- Coordonnées du support de votre entreprise, par exemple le service ingénierie.
- Coordonnées du services clientèle.

## <a name="to-publish-the-offer"></a>Pour publier l’offre

L’étape finale consiste à publier l’offre. Suivez [ces instructions pour mettre votre offre en ligne](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md).
