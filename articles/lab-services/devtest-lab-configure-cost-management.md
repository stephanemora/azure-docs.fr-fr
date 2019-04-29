---
title: Affichage des tendances de coût de laboratoire mensuelles estimées dans Azure DevTest Labs | Microsoft Docs
description: En savoir plus sur le graphique des tendances des coûts mensuels estimés Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868250"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Suivre les coûts associés à un laboratoire dans Azure DevTest Labs
Cet article fournit des informations sur la façon de suivre le coût de votre laboratoire. Il vous montre comment afficher l’estimation des coûts trent pour le mois calendaire actuel pour le laboratoire. L’article vous montre également comment afficher le coût month-to-date par ressource dans le laboratoire.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Afficher les tendances de coût estimé de laboratoire mensuelles 
Dans cette section, vous allez apprendre à utiliser le **tendance des coûts mensuels estimés** graphique pour afficher estimé coût-to-date le mois calendaire en cours et le coût projeté de la fin du mois pour le mois calendaire actuel. Vous allez également apprendre à gérer les coûts de laboratoire en définissant des seuils et des dépenses cibles qui, lorsque atteints, déclenchent dev/pour signaler les résultats pour vous.

Pour afficher le graphique Tendance des coûts mensuels estimés, procédez comme suit : 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Dans la liste de laboratoires, sélectionnez votre laboratoire.  
4. Sélectionnez **Configuration et stratégies** sur le menu de gauche.  
4. Sélectionnez **tendance des coûts** dans le **suivi des coûts** section dans le menu de gauche. La capture d’écran suivante montre un exemple de graphique de coûts. 
   
    ![Graphique de coûts](./media/devtest-lab-configure-cost-management/graph.png)

    La valeur **Coût estimé** est le coût estimé à ce jour pour le mois calendaire en cours. Le **Coût projeté** est le coût estimé pour le mois calendaire entier, calculé à l’aide du coût du laboratoire lors des cinq derniers jours.

    Les montants des coûts sont arrondis à l’entier supérieur. Par exemple :  

   * 5,01 est arrondi à 6 
   * 5,50 est arrondi à 6
   * 5,99 est arrondi à 6

     Comme indiqué au-dessus du graphique, les coûts que vous voyez par défaut dans le graphique sont les coûts *estimés* avec les tarifs de l’offre [Paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Vous pouvez également définir vos propres dépenses cibles à afficher dans les graphiques en [gérant les coûts cibles de votre laboratoire](#managing-cost-targets-for-your-lab).

     Les coûts suivants sont *pas* inclus dans le calcul des coûts :

   * Les abonnements CSP et Dreamspark ne sont pas pris en charge. En effet, Azure DevTest Labs utilise les [API de facturation Azure](../billing/billing-usage-rate-card-overview.md) pour calculer les coûts de laboratoire, et celles-ci ne prennent pas en charge les abonnements CSP et Dreamspark.
   * Les tarifs de votre offre. Actuellement, vous ne pouvez pas utiliser les tarifs de l’offre (affichés sous votre abonnement) que vous avez négociés avec Microsoft ou les partenaires Microsoft. Seuls les tarifs du paiement à l'utilisation sont disponibles.
   * Vos taxes
   * Vos remises
   * Votre devise de facturation. Actuellement, le coût du labo s'affiche uniquement dans la devise USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gestion des coûts cibles de votre laboratoire
DevTest Labs vous aide à mieux gérer les coûts de votre laboratoire en définissant des dépenses cibles que vous pouvez ensuite afficher dans le graphique de tendance du coût estimé par mois. DevTest Labs peut également vous envoyer une notification quand le seuil ou les dépenses cibles spécifiés sont atteints. 

1. Sur le **tendance des coûts** page, sélectionnez **gérer la cible**.

    ![Bouton Gérer la cible](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Sur le **gérer la cible** , spécifiez les dépenses cibles et des seuils. Vous pouvez également indiquer si chaque seuil sélectionné doit figurer sur le graphique de tendance du coût ou être communiqué via une notification Webhook.

    ![Volet Gérer la cible](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Sélectionnez la période de temps durant laquelle vos coûts cibles doivent être suivis.
      - **Tous les mois** : le suivi des coûts cibles est effectué mensuellement.
      - **Fixe**: coûts cibles sont suivies pour la plage de dates que vous spécifiez dans les dates de début et de fin. En règle générale, ces valeurs représentent la durée pendant laquelle votre projet est planifié pour s’exécuter.
   - Spécifiez un **coût cible**. Par exemple, le montant prévu à dépenser pour ce laboratoire dans la période de temps que vous avez défini.
   - Sélectionnez l’option appropriée pour activer ou désactiver un seuil à rapporter (par incréments de 25 %), jusqu’à 125 % du **coût cible** spécifié.
      - **Notifier**: Quand ce seuil est atteint, vous êtes averti par une URL webhook que vous spécifiez.
      - **Tracer sur le graphique**: Quand ce seuil est atteint, les résultats sont tracées sur le graphique de tendance de coût que vous pouvez afficher, comme décrit dans l’affichage du graphique de tendance des coûts mensuels estimés.
   - Si vous choisissez l’option **Notifier** pour l’envoi d’une notification quand le seuil est atteint, vous devez spécifier une URL Webhook. Dans la zone Intégrations des coûts, sélectionnez **Cliquez ici pour ajouter une intégration**. Entrez un **URL du Webhook** dans le volet Configurer la notification, puis sélectionnez **OK**.

       ![Volet Configurer la notification](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Si vous sélectionnez l’option **Notifier**, vous devez définir une URL Webhook.
     - De même, si vous définissez une URL Webhook, vous devez définir le paramètre **Notification** sur **Activé** dans le volet Seuil de coût.
     - Vous devez créer un Webhook avant de l’indiquer ici.  

       Pour plus d’informations sur les webhooks, consultez [Créer une fonction Azure d’API ou de webhook](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Afficher les coûts par ressource 
La fonctionnalité de tendance de coût mensuel dans labs vous permet de voir combien vous avez dépensé dans le mois calendaire actuel. Il montre également la projection de la dépense jusqu'à la fin du mois, en fonction de vos dépenses au cours des sept derniers jours. Dès le départ pour vous aider à comprendre pourquoi les dépenses dans le laboratoire respecte les seuils vous pouvez utiliser la **coût par ressource** fonctionnalité qui vous montre le coût month-to-date **par ressource** dans une table.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
4. Sélectionnez **Configuration et stratégies** sur le menu de gauche.
5. Sélectionnez **coût par ressource** dans le **suivi des coûts** section dans le menu de gauche. Vous consultez les coûts associés à chaque ressource associée à un laboratoire. 

    ![Coût par ressource](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Cette fonctionnalité vous aide à identifier facilement les ressources dont le prix est le plus afin que vous puissiez prendre des mesures pour réduire les dépenses de laboratoire. Par exemple, le coût d’une machine virtuelle est basé sur la taille de la machine virtuelle. Plus la taille de la machine virtuelle, plus est le coût. Vous pouvez trouver facilement la taille d’une machine virtuelle et le propriétaire, afin que vous pouvez communiquer avec le propriétaire de la machine virtuelle pour comprendre pourquoi cette taille de machine virtuelle est nécessaire et s’il existe un risque de réduire la taille.

[Stratégie d’arrêt automatique](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) vous aide à réduire le coût en arrêtant les machines virtuelles de laboratoire à un moment précis de la journée. Toutefois, un utilisateur de laboratoire peut refuser de la stratégie d’arrêt, ce qui augmente le coût d’exécution de la machine virtuelle. Vous pouvez sélectionner une machine virtuelle dans le tableau pour voir si elle a été choisi à la sortie de la stratégie d’arrêt automatique. Si tel est le cas, vous pouvez communiquer avec le propriétaire de la machine virtuelle pour rechercher la raison pour laquelle la machine virtuelle a été choisi à la sortie de la stratégie.
 
## <a name="next-steps"></a>Étapes suivantes
Voici quelques possibilités d’opérations pour la suite :

* [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md) : apprenez à définir les différentes stratégies utilisées pour gérer l’utilisation de votre laboratoire et de ses machines virtuelles. 
* [Créer une image personnalisée](devtest-lab-create-template.md) : quand vous créez une machine virtuelle, vous spécifiez une base, qui peut être soit une image personnalisée, soit une image Marketplace. Cet article explique comment créer une image personnalisée à partir d’un fichier VHD.
* [Configurer des images Marketplace](devtest-lab-configure-marketplace-images.md) : DevTest Labs prend en charge la création de machines virtuelles basées sur des images Azure Marketplace. Cet article explique comment spécifier, le cas échéant, les images Azure Marketplace pouvant être utilisées lors de la création de machines virtuelles dans un laboratoire.
* [Créer une machine virtuelle dans un laboratoire](devtest-lab-add-vm.md) : montre comment créer une machine virtuelle à partir d’une image de base (personnalisée ou Marketplace) et comment utiliser des artefacts dans votre machine virtuelle.

