---
title: Guide pratique pour obtenir un support sur Avere vFXT pour Azure
description: Explique comment ouvrir un ticket de support au sujet d’Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 8a371f902703287ed3105ed53a57d6341b9528d4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415105"
---
# <a name="get-help-with-your-system"></a>Obtenir de l’aide avec votre système

Si vous avez besoin d’aide concernant l’utilisation d’Avere vFXT pour Azure, voici les différentes méthodes disponibles :

* **Problème avec Avere vFXT** : utilisez le portail Azure afin d’ouvrir un ticket de support pour Avere vFXT, comme expliqué [ci-dessous](#open-a-support-ticket-for-your-avere-vfxt).
* **Quota** : si vous avez un problème lié aux quotas, [faite une demande d’augmentation de quota](#request-a-quota-increase).
* **Documentation et exemples** : si vous avez constaté des erreurs dans la documentation ou les exemples, accédez au bas de la page jusqu’à la section **Commentaires** pour voir les problèmes déjà signalés ou en signaler un nouveau.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Ouvrir un ticket de support pour Avere vFXT

Si vous rencontrez des problèmes lors du déploiement ou de l’utilisation d’Avere vFXT, demandez de l’aide via le portail Azure.

Suivez ces étapes pour que votre ticket de support soit étiqueté avec une ressource du cluster. L’étiquetage du ticket nous permet de l’acheminer vers la ressource de support adaptée.

1. Dans [https://portal.azure.com](https://portal.azure.com), sélectionnez **Groupes de ressources**.

   ![Capture d’écran du menu de gauche du portail Azure, avec l’option « Groupes de ressources » entourée](media/avere-vfxt-ticket-rg.png)

1. Accédez au groupe de ressources qui contient le cluster vFXT où le problème s’est produit, puis cliquez sur l’une des machines virtuelles Avere.

    ![Capture d’écran du panneau « Vue d’ensemble » du groupe de ressources dans le portail Azure, avec une machine virtuelle entourée](media/avere-vfxt-ticket-vm.png)

1. Dans la page de la machine virtuelle, accédez au bas du panneau gauche, puis cliquez sur **Nouvelle demande de support**.

    ![Capture d’écran de la page de la machine virtuelle de la capture d’écran précédente. Au bas du menu de gauche, l’option « Nouvelle demande de support » est entourée.](media/avere-vfxt-ticket-request.png)

1. Dans la première page de la demande de support, cliquez sur **Tous les services**, puis, sous **Stockage**, choisissez **Avere vFXT**.

    ![Capture d’écran de la nouvelle demande de support dans le portail Azure, sous l’en-tête « Bases », et avec l’élément « Service » entouré. Le bouton « Tous les services » est sélectionné, et la valeur « Avere vFXT » est sélectionnée dans la liste déroulante.](media/avere-vfxt-ticket-service.png)

1. Dans la deuxième page, choisissez le type et la catégorie qui correspondent le mieux à votre problème. Ajoutez un titre court et une description comprenant le moment où s’est produit le problème.

   ![Capture d’écran de la nouvelle demande de support sous l’en-tête « Problème », où de nombreux champs doivent être renseignés.](media/avere-vfxt-ticket-problem.png)

1. Dans la troisième page, entrez vos informations de contact, puis cliquez sur **Créer**. Une confirmation et un numéro de ticket seront envoyés à votre adresse e-mail, et un membre de l’équipe du support vous contactera.

## <a name="request-a-quota-increase"></a>Demander une augmentation de quota

Pour savoir quels composants sont nécessaires au déploiement d’Avere vFXT pour Azure, lisez [Quota pour le cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster). Vous pouvez [faire une demande d’augmentation de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) dans le portail Azure.
