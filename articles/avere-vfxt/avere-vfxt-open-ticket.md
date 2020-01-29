---
title: Guide pratique pour obtenir un support sur Avere vFXT pour Azure
description: Explique comment ouvrir un ticket de support au sujet d’Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153305"
---
# <a name="get-help-with-your-system"></a>Obtenir de l’aide avec votre système

Si vous avez besoin d’aide sur Avere vFXT pour Azure, voici les différents moyens d’obtenir du support :

* **Problème avec Avere vFXT** : utilisez le portail Azure afin d’ouvrir un ticket de support pour Avere vFXT, comme expliqué [ci-dessous](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota** : si vous avez un problème lié aux quotas, [faite une demande d’augmentation de quota](#request-a-quota-increase).
* **Documentation et exemples** : si vous avez constaté des erreurs dans la documentation ou les exemples, accédez au bas de la page jusqu’à la section **Commentaires** pour voir les problèmes déjà signalés ou en signaler un nouveau.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Ouvrir un ticket de support pour Avere vFXT

Si vous rencontrez des problèmes lors du déploiement ou de l’utilisation d’Avere vFXT, demandez de l’aide via le portail Azure.

Suivez ces étapes pour que votre ticket de support soit étiqueté avec une ressource du cluster. L’étiquetage du ticket nous permet de l’acheminer vers la ressource de support adaptée.

1. Dans [https://portal.azure.com](https://portal.azure.com), sélectionnez **Groupes de ressources**. Accédez au groupe de ressources qui contient le cluster vFXT où le problème s’est produit, puis cliquez sur une des machines virtuelles du cluster Avere.

    ![Capture d’écran du panneau « Vue d’ensemble » du groupe de ressources dans le portail Azure, avec une machine virtuelle entourée](media/avere-vfxt-ticket-vm.png)

1. Dans la page de la machine virtuelle, accédez au bas du panneau gauche, puis cliquez sur **Nouvelle demande de support**.

    ![Capture d’écran de la page de la machine virtuelle de la capture d’écran précédente. Dans le bas du menu de gauche, l’option « Nouvelle demande de support » est entourée.](media/avere-vfxt-ticket-request.png)

1. Sur la première page de la demande de support, choisissez le type de problème et vérifiez que l’abonnement approprié est sélectionné.

   Sous **Service**, cliquez sur **Tous les services** puis, sous **Stockage**, choisissez **Avere vFXT**.

   Ajoutez un court résumé, puis sélectionnez le type de problème.

    ![Capture d’écran d’une nouvelle demande de support dans le portail Azure L’onglet Informations de base est sélectionné. Les éléments affichés à l’écran sont Type de problème, Abonnement, Service, Résumé et Type de problème.](media/ticket-basics.png)

   Cliquez sur **Suivant** pour continuer.

1. La deuxième page du formulaire de support contient des suggestions pour résoudre le problème en fonction de votre description résumée. Si vous avez néanmoins encore besoin de créer un ticket de support, cliquez sur le bouton **Suivant** dans le bas.

   ![Capture d’écran de la nouvelle demande de support avec l’onglet Solutions sélectionné. Un champ texte au milieu a le titre « Solution recommandée » et explique les solutions possibles.](media/ticket-solutions.png)

1. Dans la troisième page, spécifiez différents détails : des informations sur votre cluster, le moment auquel le problème s’est produit, la gravité et comment vous contacter. Renseignez les informations et cliquez sur le bouton **Suivant** dans le bas.

   ![Capture d’écran de la nouvelle demande de support avec l’onglet Détails sélectionné. Les champs d’information sont organisés selon les catégories suivantes : « Détails du problème », « Méthode de support » et « Informations de contact ».](media/ticket-details.png)

1. Passez en revue les informations de la page finale, puis cliquez sur **Créer**. Une confirmation et un numéro de ticket seront envoyés à votre adresse e-mail, et un membre de l’équipe du support vous contactera.

## <a name="request-a-quota-increase"></a>Demander une augmentation de quota

Pour savoir quels composants sont nécessaires au déploiement d’Avere vFXT pour Azure, lisez [Quota pour le cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster). Vous pouvez [faire une demande d’augmentation de quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dans le portail Azure.
