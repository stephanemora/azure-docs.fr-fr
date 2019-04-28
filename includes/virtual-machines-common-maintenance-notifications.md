---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 50a215175d7305834a64b7e0cfbc153431b10b7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301735"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Afficher les machines virtuelles sur lesquelles une maintenance est planifiée dans le portail

Après la planification d’une vague d’opérations de maintenance, vous pouvez observer la liste des machines virtuelles impactées par la maintenance à venir. 

Vous pouvez utiliser le portail Azure et rechercher les machines virtuelles sur lesquelles une maintenance est planifiée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans la barre de navigation de gauche, cliquez sur **Machines virtuelles**.

3. Dans le volet Machines virtuelles, cliquez sur le bouton **Colonnes** pour ouvrir la liste des colonnes disponibles.

4. Sélectionnez et ajoutez les colonnes suivantes :

   **Maintenance** : Affiche l’état de maintenance de la machine virtuelle. Les valeurs potentielles sont les suivantes :
      
      | Valeur | Description |
      |-------|-------------|
      | Démarrer maintenant | La machine virtuelle étant dans la fenêtre de maintenance libre-service, vous pouvez lancer la maintenance vous-même. Consultez la procédure ci-dessous pour démarrer la maintenance sur votre machine virtuelle. | 
      | Planifiée | Une maintenance est planifiée sur la machine virtuelle, mais aucune option de lancement ne vous est proposée. Pour déterminer la fenêtre de maintenance, sélectionnez la fenêtre Maintenance - Planifiée dans cet affichage ou cliquez sur la machine virtuelle. | 
      | Mise à jour déjà effectuée | Votre machine virtuelle est déjà mise à jour et aucune action supplémentaire n’est nécessaire pour l’instant. | 
      | Réessayer plus tard | Vous avez lancé la maintenance, mais sans succès. Vous pourrez utiliser l’option de maintenance libre-service ultérieurement. | 
      | Réessayer maintenant | Vous pouvez faire une nouvelle tentative de maintenance automatique après un échec. | 
      | - | Votre machine virtuelle ne fait pas partie d’un cycle de maintenance planifiée. |
      

   **Fenêtre Maintenance - Libre-service** : Affiche la fenêtre de temps dans laquelle vous pouvez démarrer vous-même la maintenance de vos machines virtuelles.
   
   **Fenêtre Maintenance - Planifiée** : Affiche la fenêtre de temps dans laquelle Azure assure la maintenance de votre machine virtuelle pour la terminer. 



## <a name="notification-and-alerts-in-the-portal"></a>Notification et alertes dans le portail

Azure communique une planification de maintenance planifiée en envoyant un e-mail au propriétaire et au groupe de copropriétaires de l’abonnement. Vous pouvez ajouter des destinataires et des chaînes à cette communication en créant des alertes de journal d’activité Azure. Pour plus d’informations, consultez [Créer des alertes de journal d’activité sur les notifications de service](../articles/azure-monitor/platform/alerts-activity-log-service-notifications.md).

Veillez à définir le **type d’événement** comme **maintenance planifiée** et les **services** comme **groupes de machines virtuelles identiques** et/ou **machines virtuelles**
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Démarrer la maintenance sur votre machine virtuelle à partir du portail

L’affichage détaillé de la machine virtuelle contient davantage d’informations relatives à la maintenance.  
En haut de l’affichage détaillé, un nouveau ruban de notification est ajouté si votre machine virtuelle fait l’objet d’une vague d’opérations de maintenance planifiées. En outre, une nouvelle option permet de démarrer la maintenance dès que possible. 


Cliquez sur la notification de maintenance pour afficher la page de maintenance avec plus de détails sur la maintenance planifiée. À partir de là, vous pouvez **démarrer la maintenance** sur votre machine virtuelle.

Une fois la maintenance démarrée, votre machine virtuelle est soumise à une maintenance, puis après quelques minutes, l’état de la maintenance est mis à jour pour refléter le résultat.

Si vous avez raté la fenêtre de libre-service, vous pourrez toujours afficher la fenêtre quand votre machine virtuelle sera soumise à une maintenance par Azure. 
