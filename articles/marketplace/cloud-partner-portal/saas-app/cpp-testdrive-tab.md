---
title: Configuration d’une version d’évaluation d’une offre d’application Azure SaaS | Microsoft Docs
description: Configurez la version d’évaluation d’une offre d’application SaaS sur la Place de marché Azure.
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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: e97e79ef3077431d8fc627cd634a67b0a01b6f8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993110"
---
# <a name="saas-application-test-drive-tab"></a>Onglet Version d’évaluation de l’application SaaS

Utilisez l’onglet Version d’évaluation pour offrir une option d’évaluation à vos clients.

## <a name="test-drive-benefits"></a>Avantages de la version d’évaluation

Nous vous recommandons de proposer une option d’évaluation à vos clients pour leur permettre d'acheter votre application en toute confiance. Parmi les options d’évaluation disponibles, la version d’évaluation est la plus efficace pour générer des prospects pertinents et les transformer en clients.

La version d’évaluation permet aux clients d’essayer les principales fonctionnalités de votre produit et d’en apprécier les avantages grâce à un scénario d’implémentation réel.

## <a name="how-a-test-drive-works"></a>Fonctionnement d’une version d’évaluation

Un client potentiel accède à votre application après avoir lancé une recherche sur la Place de marché. Le client se connecte et accepte les conditions d’utilisation. À ce stade, le client reçoit votre environnement préconfiguré afin de l'essayer pendant un nombre d'heures défini, et vous disposez d'un prospect pertinent auquel vous pouvez donner suite. Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’une version d’évaluation ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="publishing-steps"></a>Étapes de publication

Les principales étapes de publication à suivre pour ajouter une version d'évaluation sont les suivantes :

1. Définir votre scénario de version d'évaluation
2. Créer et/ou modifier votre modèle Azure Resource Manager
3. Créer le manuel pas à pas de votre version d’évaluation
4. Republier votre offre

## <a name="setting-up-a-test-drive"></a>Configuration d’une version d’évaluation

Quatre types de versions d’évaluation sont disponibles en fonction du type de produit, du scénario et de la place de marché où vous vous trouvez.

|  **Type**          |  **Description**  |  **Instructions de configuration**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Une version d’évaluation Azure ARM est un modèle de déploiement contenant toutes les ressources Azure qui constituent une solution générée par l’éditeur. Les produits adaptés à ce type de version d’évaluation sont ceux qui utilisent uniquement des ressources Azure.               |       [Version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       Hébergée             |       Une version d’évaluation hébergée simplifie la configuration par l’hébergement Microsoft et assure la maintenance du service qui effectue l’approvisionnement et le déprovisionnement de l’utilisateur de la version d’évaluation.             |         [Version d’évaluation hébergée](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      Application logique              |       Une version d’évaluation d’application logique est un modèle de déploiement conçu pour englober toutes les architectures de solutions complexes. Les applications Dynamics et les produits personnalisés doivent tous utiliser ce type de version d'évaluation.            |      [Version d’évaluation d’une application logique](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Une version d’évaluation Power BI est composée d’un lien incorporé vers un tableau de bord personnalisé. Tous les produits dotés d'un visuel Power BI interactif doivent utiliser ce type de version d’évaluation. Il vous suffit de charger votre URL Power BI incorporée.          |        [Version d’évaluation de Power BI](#Power-BI-test-drive)           |

### <a name="power-bi-test-drive"></a>Version d’évaluation de Power BI

Utilisez les étapes suivantes pour configurer une version d’évaluation.

1. Sous Nouvelle offre, sélectionnez **Version d’évaluation**.
2. Sous Version d’évaluation, sélectionnez **Oui**.

   ![Activer une version d’évaluation](./media/saas-enable-test-drive.png)

   Lorsque vous activez une version d’évaluation, les formulaires Détails et Configuration technique apparaissent, comme indiqué dans la capture d’écran suivante.

   ![Formulaire de configuration de la version d’évaluation](./media/saas-test-drive-yes.png)

3. Sous **Détails**, renseignez les champs suivants :
  
   - Description : décrivez votre version d’évaluation et ce que les utilisateurs peuvent en faire. Vous pouvez utiliser des balises HTML de base pour mettre cette description en forme.
   - Manuel de l’utilisateur : chargez un manuel de l’utilisateur que vos clients peuvent utiliser lors de l’utilisation de la version d’évaluation. Il doit s’agir d’un fichier PDF.
   - Démonstration vidéo de la version d’évaluation (facultatif) : vous pouvez fournir une vidéo (YouTube ou Vimeo) que vos clients pourront visionner avant de prendre la version d’évaluation. Fournissez l’URL de la vidéo.

4. Sous **Configuration technique**, renseignez les champs suivants :

   - Type de version d’évaluation : sélectionnez **Power BI** dans la liste déroulante.
   - Lien vers le tableau de bord Power BI : fournissez un lien vers le tableau de bord.

5. Lorsque vous avez terminé la configuration de la version d’évaluation, sélectionnez **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

[Onglet des informations sur les vitrines](./cpp-storefront-tab.md)