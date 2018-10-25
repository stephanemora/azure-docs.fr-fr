---
title: Conteneurs | Microsoft Docs
description: Étapes de publication d’une image conteneur.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806381"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>Publication d’une image conteneur dans le portail Cloud Partner
========================================================

Cet article décrit comment publier une nouvelle image conteneur dans le portail Cloud Partner.

Pour publier une nouvelle image conteneur, procédez comme suit.

1. Sélectionnez **Nouvelle offre**, puis **Conteneurs**.

    ![Sélection de l’option Conteneurs pour une nouvelle offre](media/cpp-containers-guide/azure-container-offer.png)

2. Dans l’onglet Paramètres de l’offre, sous Identité de l’offre, entrez **l’ID de l’offre**, **l’ID de l’éditeur** et le **nom**.

    ![Identité de l’offre](media/cpp-containers-guide/containers-offer-settings.png)

3. Dans l’onglet Références SKU, sélectionnez **Nouvelle référence SKU**.
    >[!NOTE]
    >Vous pouvez ajouter plusieurs références SKU.

    ![Invite de nouvelle référence SKU](media/cpp-containers-guide/containers-sku-settings.png)

4. Fournissez des informations sur les références SKU et les conteneurs. Chaque référence SKU correspond à une image conteneur. Une référence SKU comprend deux parties :

    -   Métadonnées de référence SKU
    -   Métadonnées de conteneur

    Les métadonnées de référence SKU contiennent les informations d’affichage des images conteneur.

    ![Métadonnées de référence SKU](media/cpp-containers-guide/containers-sku-details.png)

    Les métadonnées de conteneur contiennent des informations de référence des détails de votre référentiel d’image dans Azure Container Registry (ACR). La Place de marché Microsoft Azure copie ensuite cette image dans le registre public de la place de marché, ce qui la met à la disposition des clients après certification. Toutes les requêtes émanant de l’utilisateur Azure pour consommer une image conteneur sont traitées à partir du registre de conteneurs de la Place de marché.

   ![Métadonnées de conteneur](media/cpp-containers-guide/containers-image-repository.png)

    Les détails de référentiel d’image de la capture d’écran précédente contiennent les champs suivants :

    -   **ID d’abonnement** : ID d’abonnement Azure donnant accès au registre ACR.
    -   **Nom du groupe de ressources** : nom du groupe de ressources du registre ACR.
    -   **Nom du registre** : nom du registre ACR.
    -   **Nom du référentiel** : nom du référentiel. Une fois définie, cette valeur n’est plus modifiable. Ce nom doit être unique afin que aucune autre offre ne porte le même nom dans votre compte.
    -   **Nom d’utilisateur** : nom d’utilisateur associé au registre ACR (nom d’utilisateur administrateur).
    -   **Mot de passe** : mot de passe associé au registre ACR.

    >[!NOTE]
    >Le nom d’utilisateur et le mot de passe sont requis pour s’assurer que les partenaires ont accès au registre ACR décrit dans le processus de publication.

    Lors de la publication d’une image conteneur, vous pouvez également fournir une ou plusieurs balises d’image. Outre une balise d’image, les partenaires peuvent également fournir des codes de hachage SHA. Veillez à ajouter une **balise de test** à votre image afin que vous puissiez identifier l’image pendant le test.

5. Dans l’onglet Place de marché, ajoutez votre contenu propre au marché.

    ![Informations de la Place de marché](media/cpp-containers-guide/containers-marketplace-tab.png)

6. Dans l’onglet Support, entrez les informations du service clientèle et du support technique.

   ![Informations de support](media/cpp-containers-guide/containers-support-tab.png)

7. Sélectionnez **Publier** pour publier l’offre. Une fois que vous avez sélectionné Publier, une chronologie vous montre les étapes de la publication de votre image conteneur.
