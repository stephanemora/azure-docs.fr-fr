---
title: Mettre à jour une offre existante pour Azure Marketplace
description: Mettre à jour une offre existante pour Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806189"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Mettre à jour une offre existante pour Azure Marketplace 
==============================================

Il existe différents types de mises à jour que vous pouvez appliquer à votre offre lorsqu’elle est publiée.

1.  Ajout d’un nouveau \"package\" à une référence SKU existante
2.  Ajout de nouvelles référence SKU à une offre existante
3.  Mise à jour des métadonnées marketplace d’une offre/référence SKU

Vous devez mettre à jour votre offre dans le portail Cloud Partner puis la republier. Cet article vous présente en détail les différents aspects de la mise à jour de votre offre d’Azure Application.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Modifications non autorisées apportées à la référence SKU/l’offre d’Azure Application 
--------------------------------------------------

Certains attributs d’une offre d’Azure Application/référence SKU de machine virtuelle ne peuvent pas être modifiés une fois que l’offre est publiée dans Azure Marketplace.

1.  ID d’offre et ID de l’éditeur de l’offre.
2.  ID de référence (SKU) de références (SKU) existantes.
3.  Mettez à jour un package qui a été publié.

<a name="adding-a-new-package-to-an-existing-sku"></a>Ajout d’un nouveau package à une référence SKU existante 
---------------------------------------

L’éditeur peut être enclin à ajouter une nouvelle version du package afin de mettre à jour un package existant. Pour ce faire, il est possible de charger un nouveau package avec un autre numéro de version.

1.  Se connecter au [Portail Cloud Partner](http://cloudpartner.azure.com)
2.  Dans Toutes les offres, recherchez l’offre que vous souhaitez mettre à jour
3.  Sur le formulaire des références SKU, cliquez sur la référence SKU pour laquelle vous souhaitez mettre à jour le package
4.  Cliquez sur \"Nouveau Package\" et fournissez une nouvelle version
5.  Téléchargez un nouveau fichier zip contenant le fichier de modèle mis à jour
6.  Cliquez sur Publier pour lancer le workflow de publication afin de publier votre nouvelle référence SKU.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Ajout d’une nouvelle référence SKU à une offre existante
-------------------------------------

Vous pouvez choisir de proposer une nouvelle référence (SKU) pour votre offre existante. Pour cela, suivez les étapes ci-dessous.

1.  Connectez-vous au [portail Microsoft Cloud Partner](http://cloudpartner.azure.com)
2.  Dans Toutes les offres, recherchez l’offre que vous souhaitez mettre à jour
3.  Sur le formulaire des références SKU, cliquez sur Ajouter une nouvelle référence SKU et fournissez un ID de SKU dans la fenêtre contextuelle.
4.  Suivez le reste des étapes comme spécifié [ici](./cloud-partner-portal-managed-app-publish.md).
5.  Cliquez sur Publier pour lancer le workflow de publication afin de publier votre nouvelle référence SKU.

<a name="updating-offer-marketplace-metadata"></a>Mise à jour des métadonnées de la Place de marché d’une offre 
-----------------------------------

Dans certains scénarios, vous devez mettre à jour les métadonnées marketplace associées à votre offre, par exemple vos logos de société. Pour ce faire, procédez comme suit.

1.  Se connecter au Portail Cloud Partner
2.  Dans Toutes les offres, recherchez l’offre que vous souhaitez mettre à jour
3.  Accédez au formulaire Marketplace et suivez les instructions affichées [ici](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging) pour apporter des modifications.
4.  Cliquez sur Publier pour lancer le workflow de publication afin de publier vos modifications.
