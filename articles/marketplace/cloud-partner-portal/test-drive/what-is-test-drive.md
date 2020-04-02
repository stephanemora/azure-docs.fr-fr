---
title: Qu'est-ce qu'une version d'évaluation ? | Place de marché Azure
description: Description de la fonctionnalité Version d’évaluation de la Place de marché
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bdfadf359195464c4024b28c5e597c571305481a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278227"
---
<a name="what-is-test-drive"></a>Qu'est-ce qu'une version d'évaluation ?
===================

Une version d'évaluation vous permet de présenter votre offre à vos clients potentiels en leur donnant la possibilité de l'\'essayer avant de l'acheter\', ce qui se traduit par une hausse du taux de conversion et par la génération de prospects hautement qualifiés.

Après avoir fourni leurs coordonnées, les clients peuvent accéder à votre version d\'évaluation prédéfinie : une version d'évaluation pratique et auto-guidée qui présente, dans un scénario d'implémentation réel, les principales fonctionnalités et les avantages de votre produit.

Une version d'évaluation permet de donner vie à votre produit et de générer des prospects hautement qualifiés.

<a name="how-does-a-test-drive-work"></a>Comment fonctionne une version d'évaluation ?
---------------------------

Un client potentiel découvre votre application sur la Place de marché, se connecte et accepte les conditions d'utilisation. À ce stade, le client reçoit votre environnement préconfiguré afin de l'essayer pendant un nombre d'heures défini, et vous disposez d'un prospect pertinent auquel vous pouvez donner suite.

![Étape 1. Affichage de l'offre sur la Place de marché](./media/what-is-test-drive/step1.png)

![Étape 2. Écran de connexion à l'offre de la Place de marché](./media/what-is-test-drive/step1andahalf.png)

![Étape 3. Écran du contrat d'éditeur de l'offre de la Place de marché](./media/what-is-test-drive/step2.png)

![Étape 4. Écran de configuration de la version d'évaluation](./media/what-is-test-drive/step3.png)

Et voici comment se présente une offre qui a besoin de temps pour se déployer :

![Offre de la Place de marché en cours de déploiement](./media/what-is-test-drive/step4.png)

![Écran indiquant que la version d'évaluation de la Place de marché est prête](./media/what-is-test-drive/step5.png)

![Écran indiquant que la version d'évaluation de la Place de marché est terminée](./media/what-is-test-drive/step6.png)

Quelle que soit la complexité votre application, votre version d'évaluation Microsoft vous permet de lui donner vie pour la présenter au client. Nous vous proposons actuellement trois types de versions d'évaluation en fonction du type de produit, du scénario et de la place de marché où vous vous trouvez.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)** : Une version d’évaluation Azure ARM est un modèle de déploiement contenant toutes les ressources Azure qui constituent une solution générée par l’éditeur. Les produits adaptés à ce scénario sont ceux qui utilisent uniquement des ressources Azure.
- **[Application logique](./logic-app-test-drive.md)** : Une version d’évaluation d’application logique est un modèle de déploiement conçu pour englober toutes les architectures de solutions complexes. Les applications Dynamics et les produits personnalisés doivent tous utiliser ce type de version d'évaluation.
- **Power BI** : Une version d’évaluation Power BI est un lien incorporé vers un tableau de bord personnalisé. Tous les produits dotés d'un visuel Power BI interactif doivent utiliser ce type de version d'évaluation.
    Il vous suffit de charger votre URL Power BI incorporée.

<a name="what-goes-on-in-the-background"></a>Que se passe-t-il en arrière-plan ?
-------------------------------

Le service Version d'évaluation a été conçu pour prendre en charge et servir vos clients en continu sans intervention manuelle de votre part. En tant qu'éditeur, votre travail consiste à gérer et configurer les paramètres de la version d'évaluation à partir du portail Cloud Partner. Vos clients bénéficieront ensuite directement de ces paramètres.

En effet, après avoir configuré votre version d'évaluation, chacune de ses instances devient une instance gérée déployée à la demande du client. Une fois attribuée, l'instance de la version d'évaluation est utilisable pendant la durée définie, puis elle est supprimée afin de céder la place à un autre client.

<a name="next-steps"></a>Étapes suivantes
----------

Maintenant que vous savez ce qu'est une version d'évaluation, reportez-vous au type de version d'évaluation que vous souhaitez publier pour en savoir plus sur les champs requis.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**
- **[Application logique](./logic-app-test-drive.md)**

Si vous avez d'autres questions, si vous cherchez des conseils de résolution des problèmes ou si vous souhaitez améliorer votre version d'évaluation, consultez l'article [Forum aux questions, résolution des problèmes et meilleures pratiques](./marketing-and-best-practices.md).
