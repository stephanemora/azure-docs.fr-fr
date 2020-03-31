---
title: Onglet Références de machine virtuelle dans le Portail Microsoft Cloud Partner pour la Place de marché Microsoft Azure
description: Décrit l’onglet Références utilisé pour créer une offre de machine virtuelle dans Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 49f1de5128325b2884ea76b010727be45f1b195d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288884"
---
# <a name="virtual-machine-skus-tab"></a>Onglet Références de machine virtuelle

L’onglet **Références** de la page **Nouvelle offre** permet de créer une ou plusieurs références SKU et de les associer à votre nouvelle offre.  Différentes références SKU permettent de différencier une solution par des ensembles de fonctionnalités, des types d’images de machine virtuelle, le débit ou l’évolutivité, les modèles de facturation ou une autre caractéristique.


## <a name="create-a-sku"></a>Créer une référence (SKU)

Au départ, une nouvelle offre n’étant associée à aucune référence SKU, vous allez créer une en cliquant sur **New SKU (Nouvelle référence)** .

![Bouton New SKU (Nouvelle référence) sur l’onglet Nouvelle offre pour les machines virtuelles](./media/publishvm_005.png)

<br/>

La boîte de dialogue **New SKU (Nouvelle référence)** s’affiche.  Entrez l’identificateur de la nouvelle référence SKU, puis cliquez sur **OK**. (Voir ci-dessous pour les conventions d’affectation de noms d’identificateur.)  L’onglet **Références** affiche désormais les champs modifiables.    Un astérisque (*) en regard du nom du champ indique que ce champ est obligatoire.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Onglet SKU du formulaire Nouvelle offre pour les machines virtuelles](./media/publishvm_006.png)

Le tableau suivant décrit l’objectif, le contenu et le format de ces champs.  Les champs obligatoires sont indiqués par un astérisque (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
|  *Paramètres de référence SKU*   |    |
| **ID de SKU\***       | Identificateur de cette référence SKU.  Ce nom compte au maximum 50 caractères (minuscules, caractères alphanumériques ou tirets), mais ne peut pas se terminer par un tiret.  Il n’est plus modifiable, une fois l’offre publiée.  |
|  *SKU Details (Détails de SKU)*   |  |
| **Titre\***        | Nom convivial de l’offre à afficher dans la place de marché. Longueur maximale de 50 caractères. |
| **Résumé\***      | Brève description de l’offre à afficher dans la place de marché. Longueur maximale de 100 caractères. |
| **Description\***  | Description présentant l’offre plus en détail.  <!-- TD: max len/guidance? 3k characters -->  |
| **Masquer cette référence (SKU)\*** | Indique si la référence SKU doit être visible aux clients dans la place de marché.  Vous pouvez choisir de masquer la référence SKU si vous souhaitez qu’elle ne soit disponible que via des modèles de solution, mais pas en tant qu’achat individuel.  Elle peut également être utile pour les tests initiaux ou pour les offres temporaires ou saisonnières. |
| **Disponibilité dans le cloud\*** | Détermine les clouds sur lesquels la référence SKU doit être disponible.  La valeur par défaut est la version publique d’Azure.  Microsoft Azure Government est un cloud communautaire pour le secteur public, avec accès contrôlé pour les administrations fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’à leurs partenaires certifiés.  Pour plus d’informations sur le cloud Microsoft Azure Government, consultez [Welcome to Azure Government (Bienvenue dans Microsoft Azure Government)](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **S’agit-il d’une référence SKU privée ?\*** | Indique si la référence SKU est privée ou publique. La valeur par défaut est **Non** (publique).  Pour plus d’informations, consultez [Références SKU privées](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Disponibilité par pays/région\*** | Détermine les pays ou régions du monde dans lesquels votre référence SKU pourra être achetée. Sélectionnez au moins un pays/une région. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Tarification*   |  |
| **License Model (Modèle de licence)\***| Modèle de facturation standard à utiliser.  Si vous sélectionnez **Usage-based monthly billed SKU (SKU facturée mensuellement selon l’utilisation)** , une section s’ouvre et vous permet de spécifier les détails de la tarification par cœur et si vous souhaitez proposer une période d’essai gratuite.  Cette section vous permet également d’exporter et d’importer ce planning de tarification au format Excel. Pour plus d’informations, consultez [Options de facturation sur la Place de marché Azure](../../billing-options-azure-marketplace.md). | 
|  *Images de machine virtuelle*   |  |
| **Famille de systèmes d’exploitation\*** | Indique si la machine virtuelle de la solution est Windows ou Linux. |
| **Select Operating System Type (Sélectionner le type de système d’exploitation)** | Fournisseur ou version du système d’exploitation spécifié. |
| **Nom convivial du système d’exploitation\*** | Nom du système d’exploitation à afficher aux clients.  |
| **Tailles de machines virtuelles recommandées\*** | Permet de sélectionner jusqu’à six tailles de machine virtuelle recommandées dans une liste normalisée.  Cette liste est transmise au Portail Azure et aux places de marché Microsoft.  La première taille de machine virtuelle valide dans cette liste (pour cet abonnement client, région, zone, etc.) est définie comme la valeur par défaut pour ce client potentiel.  L’utilisateur peut remplacer cette taille par n’importe quelle taille compatible avec l’image de la solution. | 
| **Ports ouverts**| Ports à ouvrir et protocole pour prendre en charge pour la référence SKU.  Ces configurations doivent correspondre au réseau virtuel que vous avez configuré pour le réseau de la machine virtuelle de la solution. Ces paramètres entrent en vigueur lors du déploiement de la machine virtuelle. Toutefois, vous pouvez modifier les paramètres de port après avoir publié une référence SKU. Pour plus d’informations, consultez [Guide d’ouverture de ports vers une machine virtuelle avec le portail Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Les mappages de réseau par défaut suivants sont ajoutés à toutes les machines virtuelles. &emsp; Windows : 3389 -> 3389 TCP, 5986 -> 5986 TCP ; &emsp; Linux : 22 -> 22, TCP (SSH). |
| **Disk Version (Version du disque)**  | Machine virtuelle de solution associée, spécifiée par le numéro de version de disque et l’URL de disque. La version de disque doit être au format de [version sémantique](https://semver.org/) : `<major>.<minor>.<patch>`.  L’URL est l’URI de signature d’accès partagé créé pour le disque dur virtuel du système d’exploitation.  Vous pouvez ajouter jusqu’à 8 versions disque par référence SKU, mais seul le numéro de version de disque le plus élevé pour une référence SKU s’affichera dans la Place de marché Microsoft Azure. Les autres versions ne seront visibles que dans les API.  <!--TD: Add more specific link to API --> <br/> La section **New data disk (Nouveau disque de données)** vous permet d’associer jusqu’à 15 disques de données à votre machine virtuelle.  Lorsque vous publiez une référence SKU avec une version de machine virtuelle spécifique et des disques de données associés, cette configuration n’est plus modifiable.  Si des versions de machine virtuelle supplémentaires sont ajoutées à la référence SKU, elles doivent également prendre en charge le même nombre de disques de données. <br/> Si vous n’avez créé votre ou vos images de machine virtuelle Azure, vous pouvez mettre à jour ce champ ultérieurement.  Pour plus d’informations sur la création de la ressource de machine virtuelle associée, consultez la section [Create VM technical assets (Créer des ressources techniques pour une offre de machine virtuelle)](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Cliquez sur **Enregistrer** pour enregistrer votre progression. Dans l’onglet suivant, vous allez spécifier si votre offre prend en charge [Test Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Autres considérations relatives à la tarification

Le modèle de tarification décrit ci-dessus est une description de base.  Il est en cours de modification et peut être affecté par les réglementations fiscales régionales ou locales ainsi que par les stratégies de tarification de Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Nouvelles tailles de cœurs ajoutées le 02/07/2019

Les éditeurs de machines virtuelles ont été avertis le 2 juillet 2019 de l’ajout de nouveaux tarifs pour les nouvelles tailles de machines virtuelles Azure (en fonction du nombre de cœurs).  Les nouveaux tarifs concernent les tailles de cœurs suivantes : 10, 44, 48, 60, 120, 208 et 416.  Pour les machines virtuelles existantes, de nouveaux tarifs pour ces tailles de cœurs ont été calculés automatiquement en fonction des tarifs actuels.  Les éditeurs ont jusqu’au 1er août 2019 pour passer en revue les prix supplémentaires et apporter les modifications souhaitées.  Après cette date, s’ils n’ont pas encore été republiés par l’éditeur, les prix automatiquement calculés pour ces nouvelles tailles de cœurs prendront effet.


### <a name="simplified-currency-pricing"></a>Tarification simplifiée en devise

Depuis le 1er septembre 2018, une nouvelle section intitulée **Simplified Currency Pricing (Tarification simplifiée en devise)** est ajoutée dans le portail. Microsoft simplifie les activités de la Place de marché Microsoft Azure en améliorant la prévisibilité de la tarification et des prélèvements de vos clients dans le monde entier. Pour ce faire, nous réduisons le nombre de devises dans lesquelles nous facturons vos clients.  Pour plus d’informations, consultez [Update an existing VM offer on Azure Marketplace (Mettre à jour une offre de machine virtuelle existante sur la Place de marché Microsoft Azure)](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Informations complémentaires sur les taxes et les prix

* Microsoft classe certains pays/régions dans la catégorie *Tax Remitted Countries (Pays reversant les taxes)* .  Dans ces pays/régions, Microsoft collecte les taxes auprès des clients et les reverse au gouvernement.  Dans d’autres pays/régions, les partenaires sont chargés de collecter les taxes auprès de leurs clients et de les reverser à leur État. Si vous choisissez de vendre dans ces pays/régions, vous devez avoir la capacité de calculer ces taxes et de les y régler.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Les prix ne sont pas modifiables une fois l’offre publiée. Cependant, vous pouvez toujours ajouter ou supprimer des régions desservies. 
* Microsoft facture au client les frais d’utilisation standard des machines virtuelles Azure en plus de vos frais de référence SKU planifiés.
* Les prix sont définis pour toutes les régions en devise locale sur la base des taux de change disponibles au moment de leur établissement.  <!-- TD: Meaning? - Offer created, published, other? -->
* Pour définir le prix de chaque région, exportez la feuille de calcul de tarification, appliquez la tarification personnalisée, puis importez-la. 


## <a name="next-steps"></a>Étapes suivantes

Si vous le souhaitez, vous pourrez spécifier les informations [Test Drive](./cpp-test-drive-tab.md) si vous prenez en charge cette fonctionnalité ; sinon, fournissez les données [Place de marché](./cpp-marketplace-tab.md) de votre offre.
