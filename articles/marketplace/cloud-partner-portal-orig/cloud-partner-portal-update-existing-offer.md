---
title: Mettre à jour une offre existante pour Azure Marketplace | Microsoft Docs
description: Explique comment mettre à jour une offre de la Place de marché Microsoft Azure existante à l’aide du Portail Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
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
ms.openlocfilehash: bfe2b0c70c8b912f6489ed461f5bcf6f233ed60d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806145"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Mettre à jour une offre existante pour Azure Marketplace
==============================================

Il existe différents types de mises à jour que vous pouvez appliquer à votre offre une fois qu’elle est publiée.   Le [Portail Cloud Partner](https://cloudpartner.azure.com/) comporte plusieurs fonctionnalités vous permettant de modifier une offre afin de vous assurer de l’exactitude des modifications, notamment :

-  Ajout d’une nouvelle version de l’image de la machine virtuelle (VM) à une référence (SKU) existante
-  Modification des régions dans lesquelles une référence (SKU) est disponible
-  Ajout de nouvelles références (SKU)
-  Mise à jour des métadonnées de la Place de marché pour les offres ou les références (SKU) 
-  Mise à jour de la tarification des offres avec paiement à l’utilisation
-  Comparaison des fonctionnalités
-  Historique des fonctionnalités

Après avoir modifié une offre ou une référence (SKU), celle-ci doit être publiée à nouveau avant que les modifications ne soient mises en ligne.
Cet article vous présente en détail les différents aspects de la mise à jour de votre offre de machine virtuelle.


<a name="unpermitted-changes-to-vm-offersku"></a>Modifications non autorisées apportées à l’offre/référence (SKU) de machine virtuelle
-----------------------------------

Certains attributs d’une offre ou référence (SKU) de machine virtuelle ne peuvent pas être modifiés une fois que l’offre est publiée dans la Place de marché Microsoft Azure.

-  ID d’offre et ID de l’éditeur de l’offre.
-  ID de référence (SKU) de références (SKU) existantes.
-  Nombre de disques de données de références (SKU) existantes.
-  Modifications du modèle de facturation/licence apportées à des références (SKU) existantes.


<a name="updating-the-vm-image-version-for-a-sku"></a>Mise à jour de la version image de machine virtuelle pour une référence (SKU)
---------------------------------------

L’image de machine virtuelle pour une référence de votre offre peut avoir été mise à jour avec des fonctionnalités supplémentaires, des correctifs de sécurité, etc. Dans de tels scénarios, vous pouvez mettre à jour l’image de machine virtuelle associée à votre référence (SKU). Suivez les étapes suivantes pour mettre à jour une image de machine virtuelle. 

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3.  Sous le formulaire des **références (SKU)**, cliquez sur la référence (SKU) pour laquelle vous souhaitez mettre à jour l’image de machine virtuelle.
4.  Sous **Version disque**, cliquez sur **+Nouvelle version disque** pour ajouter une nouvelle image de machine virtuelle.
5.  Fournissez la **version disque** des nouvelles images de machine virtuelle. La version disque doit suivre le format [version sémantique](http://semver.org/). Les versions doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Assurez-vous que la nouvelle version que vous fournissez est supérieure aux versions antérieures ; sinon le nouveau numéro de version ne s’affichera pas dans le Portail Azure ou dans Azure marketplace lors de la republication.
6.  Dans le champ **URL de disque dur virtuel de système d’exploitation**, saisissez l’URI de signature d’accès partagé (SAS) créé pour le disque dur virtuel du système d’exploitation. Notez que le nombre de disques de données ne peut pas varier entre différentes versions de la référence (SKU). Si des versions précédentes avaient des disques de données configurés, cette nouvelle version doit également avoir des disques de données configurés.
7.  Cliquez sur **Publier** pour lancer le workflow de publication afin que la nouvelle version de la machine virtuelle soit publiée sur Azure marketplace et le portail Azure.


<a name="changing-regions-a-sku-is-available-in"></a>Modification des régions dans lesquelles une référence (SKU) est disponible
--------------------------------------

Au fil du temps, vous pouvez proposer votre offre/référence (SKU) dans d’autres régions.
Vous pouvez également arrêter la prise en charge de l’offre/référence (SKU) dans une région donnée.
Pour implémenter ces modifications, suivez les étapes ci-dessous.

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3.  Sous le formulaire des **références (SKU)**, cliquez sur la référence (SKU) pour laquelle vous souhaitez mettre à jour la disponibilité de la région.
4.  Cliquez sur le bouton **Sélectionner des pays** sous le champ **Disponibilité par pays/région**.
5.  Dans la fenêtre contextuelle, ajoutez ou supprimez les régions souhaitées pour cette référence (SKU).
6.  Cliquez sur **Publier** pour lancer le workflow de publication afin de mettre à jour la disponibilité des régions pour vos références (SKU).

Si une référence (SKU) est proposée dans une nouvelle région, vous devez spécifier les tarifs pour cette région particulière en **exportant les données de tarification**. Si vous ajoutez une région qui était disponible auparavant, vous ne pourrez pas mettre à jour son prix car les modifications de prix ne sont pas autorisées.


<a name="adding-a-new-sku"></a>Ajout d’une nouvelle référence (SKU)
----------------

Pour qu’une nouvelle référence (SKU) soit disponible pour votre offre existante, suivez les étapes ci-dessous.

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3.  Sous le formulaire des **références (SKU)**, cliquez sur **Ajouter une nouvelle référence (SKU)** et fournissez un **ID de SKU** dans la fenêtre contextuelle.
4.  Suivez les étapes détaillées restantes dans [Publier une machine virtuelle sur la Place de marché Microsoft Azure](./cloud-partner-portal-publish-virtual-machine.md).
5.  Cliquez sur **Publier** pour lancer le workflow de publication afin de publier votre nouvelle référence (SKU).


<a name="updating-offer-marketplace-metadata"></a>Mise à jour des métadonnées marketplace de l’offre.
------------------------------------

Dans certains scénarios, vous devez mettre à jour les métadonnées marketplace associées à votre offre, par exemple vos logos de société. Suivez les étapes suivantes pour mettre à jour les métadonnées de votre offre.

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3.  Accédez au formulaire **Marketplace** et suivez les instructions affichées ici pour apporter des modifications.
4.  Cliquez sur **Publier** pour lancer le workflow de publication afin de publier vos modifications.


<a name="updating-pricing-on-published-offers"></a>Mise à jour de la tarification des offres publiées
------------------------------------

Une fois que votre offre avec paiement à l’utilisation est publiée, vous ne pouvez pas augmenter le prix d’une référence (SKU) existante, mais vous pouvez créer une nouvelle référence (SKU) avec la même offre, la supprimer et publier à nouveau votre offre. Nous vous permettons également d’abaisser la tarification des offres déjà publiées. Pour réduire le prix de votre offre :

1.  Cliquez sur la référence (SKU) pour laquelle vous souhaitez abaisser la tarification.
2.  Si vous avez configuré la tarification dans l’interface graphique utilisateur 1x1, vous pouvez modifier le prix directement dans l’interface utilisateur. Si vous configurez la tarification par le biais d’un tableur d’importation/exportation, vous ne pouvez diminuer les prix qu’avec la fonction Importation/exportation.
3.  Cliquez sur **Enregistrer**.
4.  Publiez à nouveau votre offre et mettez-la en ligne.

Les nouveaux clients pourront voir la tarification une fois qu’elle sera complètement en ligne sur le site, et ils paieront le nouveau prix avec réduction.

Les clients actuels bénéficieront de la réduction de prix de façon rétroactive depuis le début du cycle de facturation de l’entrée en vigueur de la réduction de prix.
S’ils ont déjà été facturés pour le cycle correspondant à la réduction du prix, ils recevront un remboursement au cours du prochain cycle de facturation afin de tenir compte de la réduction.


<a name="simplified-currency-pricing"></a>Tarification simplifiée des devises
---------------------------

À compter de septembre 2018, vous pourrez consulter une nouvelle section intitulée **Tarification simplifiée des devises**. Microsoft simplifie les activités de la Place de marché Microsoft Azure en améliorant la prévisibilité de la tarification et des prélèvements de vos clients dans le monde entier. Pour ce faire, nous réduisons le nombre de devises dans lesquelles nous facturons vos clients.

La nouvelle section prendra en compte la tarification dans ces nouvelles devises. Lorsque tous les clients seront passés aux devises de ce nouvel accord, la section de tarification initiale sera supprimée et seule la section « Tarification simplifiée des devises » sera conservée.

Vous aurez jusqu’au 1er novembre 2018 pour fixer un nouveau prix dans les régions où l’accord modifie la devise. Vous ne pourrez pas augmenter le prix dans les régions où l’accord ne modifie pas la devise. Voici les régions dans lesquelles la devise est modifiée :

| Pays                  | Code ISO2 | Devise de facturation simplifiée |
|--------------------------|-----------|-----------------------------|
| **Algérie**              | DZ        | USD                         |
| **Argentine**            | AR        | USD                         |
| **Bahreïn**              | BH        | USD                         |
| **Bélarus**              | BY        | USD                         |
| **Brésil**               | BR        | BRL                         |
| **Bulgarie**             | BG        | EUR                         |
| **Chili**                | CL        | USD                         |
| **Colombie**             | CO        | USD                         |
| **Costa Rica**           | CR        | USD                         |
| **Croatie**              | HR        | EUR                         |
| **République tchèque**       | CZ        | EUR                         |
| **Égypte**                | EG        | USD                         |
| **Guatemala**            | GT        | USD                         |
| **Hong Kong (R.A.S.)**            | HK        | USD                         |
| **Hongrie**              | HU        | EUR                         |
| **Islande**              | IS        | EUR                         |
| **Indonésie**            | ID        | USD                         |
| **Israël**               | IL        | USD                         |
| **Jordanie**               | JO        | USD                         |
| **Kazakhstan**           | KZ        | USD                         |
| **Kenya**                | KE        | USD                         |
| **Koweït**               | KW        | USD                         |
| **Liechtenstein**        | LI        | EUR                         |
| **Macédoine, ancienne république yougoslave de**     | MK        | USD                         |
| **Malaisie**             | MY        | USD                         |
| **Mexique**               | MX        | USD                         |
| **Monténégro**           | ME        | USD                         |
| **Maroc**              | MA        | USD                         |
| **Nigéria**              | NG        | USD                         |
| **Oman**                 | OM        | USD                         |
| **Pakistan**             | PK        | USD                         |
| **Paraguay**             | PY        | USD                         |
| **Pérou**                 | PE        | USD                         |
| **Philippines**          | PH        | USD                         |
| **Pologne\***             | PL        | EUR                         |
| **Qatar**                | QA        | USD                         |
| **Roumanie**              | RO        | EUR                         |
| **Arabie Saoudite**         | SA        | USD                         |
| **Serbie**               | RS        | USD                         |
| **Singapour**            | SG        | USD                         |
| **Afrique du Sud**         | ZA        | USD                         |
| **Thaïlande**             | MJ        | USD                         |
| **Trinité-et-Tobago**  | TT        | USD                         |
| **Tunisie**              | TN        | USD                         |
| **Turquie**               | TR        | USD                         |
| **Ukraine**              | UA        | USD                         |
| **Émirats Arabes Unis** | AE        | USD                         |
| **Uruguay**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> Si vous publiez votre offre à l’aide d’API, une nouvelle section de l’offre nommée **JSON** peut s’afficher. Elle indiquera `virtualMachinePricingV2` ou `monthlyPricingV2`, selon le type d’offre.

Pour toute question concernant cette modification, contactez le support de la Place de marché Microsoft Azure.


<a name="compare-feature"></a>Fonction Comparer
---------------

Lorsque vous apportez des modifications à une offre déjà publiée, vous pouvez utiliser la fonction *Comparer* pour les vérifier. Pour utiliser la fonction Comparer :

1.  À tout moment dans le processus de modification, vous pouvez cliquer sur le bouton **Comparer** de votre offre.

2.  Affichez côte à côte les versions des ressources marketing et des métadonnées.


<a name="history-of-publishing-actions"></a>Historique des actions de publication
-----------------------------

Pour afficher tout l’historique des activités de publication, cliquez sur l’onglet **Historique** dans la barre de navigation sur le côté gauche du Portail Cloud Partner. Vous pouvez consulter ici les actions horodatées qui ont été effectuées pendant la durée de vie de vos offres sur la Place de marché Microsoft Azure.
