---
title: Guide d’administration du portail Azure Data Box | Microsoft Docs
description: Décrit comment utiliser le portail Azure pour administrer votre Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 49c2258100e99742bcb2e22fbce7f05b69c70ef6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090720"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Utiliser le portail Azure pour administrer votre Data Box

Cet article décrit certains des flux de travail et tâches de gestion complexes qui peuvent être effectués sur la Data Box. Vous pouvez gérer la Data Box via le portail Azure ou via l’interface utilisateur web locale. 

Cet article se concentre sur les tâches que vous pouvez effectuer à l’aide du portail Azure. Utilisez le portail Azure pour gérer les commandes et Data Box, et suivre l’état des commandes jusqu’à l’étape terminale.


## <a name="cancel-an-order"></a>Annuler une commande

Il peut vous arriver de devoir annuler, pour diverses raisons, une commande que vous avez passée. Vous pouvez uniquement annuler la commande tant qu’elle n’a pas été traitée. Une fois la commande traitée et la Data Box préparée, il n’est plus possible d’annuler la commande. 

Pour annuler une commande, procédez comme suit.

1.  Accédez à **Vue d’ensemble > Annuler**. 

    ![Annulation de commande 1](media/data-box-portal-admin/cancel-order1.png)

2.  Indiquez un motif d’annulation de la commande.  

    ![Annulation de commande 2](media/data-box-portal-admin/cancel-order2.png)

3.  Une fois que la commande est annulée, le portail met à jour l’état de la commande et l’affiche en tant que **Annulée**. 

## <a name="clone-an-order"></a>Cloner une commande

Le clonage est utile dans certaines situations. Par exemple, un utilisateur a utilisé Data Box pour transférer des données. À mesure qu’il génère de nouvelles données, il a besoin d’une autre Data Box pour transférer ces données vers Azure. Dans ce cas, la commande initiale peut simplement être clonée.

Effectuez les opérations suivantes pour cloner une commande.

1.  Accédez à **Vue d’ensemble > Cloner**. 

    ![Clonage de commande 1](media/data-box-portal-admin/clone-order1.png)

2.  Tous les détails de la commande restent inchangés. Le nom de la commande est le nom de la commande d’origine, suivi de *-Clone*. Cochez la case pour confirmer que vous avez lu les informations sur la confidentialité des données. Cliquez sur **Créer**.    

Le clone est créé en quelques minutes et le portail est actualisé pour afficher la nouvelle commande.


## <a name="delete-order"></a>Supprimer une commande

Il peut arriver que vous souhaitiez supprimer une commande lorsque celle-ci est terminée. La commande contient vos données personnelles telles que votre nom, votre adresse et vos informations de contact. Ces informations personnelles sont supprimées en même temps que la commande.

Vous pouvez uniquement supprimer des commandes qui sont terminées ou annulées. Pour supprimer une commande, procédez comme suit.

1. Sélectionnez **Toutes les ressources**. Recherchez votre commande.

2. Cliquez sur la commande que vous souhaitez supprimer et accédez à **Vue d’ensemble**. Dans la barre de commandes, cliquez sur **Supprimer**.

    ![Suppression de la commande Data Box 1](media/data-box-portal-admin/delete-order1.png)

3. Entrez le nom de la commande lorsque vous êtes invité à confirmer la suppression de la commande. Cliquez sur **Supprimer**.

## <a name="download-shipping-label"></a>Télécharger une étiquette d’expédition

Vous devrez peut-être télécharger l’étiquette d’expédition si l’écran E-ink de votre Data Box ne fonctionne pas et n’affiche pas l’étiquette de retour. 

Procédez comme suit pour télécharger une étiquette d’expédition.
1.  Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**. Cette option est disponible uniquement une fois que l’appareil a été expédié. 

    ![Télécharger une étiquette d’expédition](media/data-box-portal-admin/download-shipping-label.png)

2.  L’étiquette de retour suivante est alors téléchargée. Enregistrez puis imprimez l’étiquette. Pliez et insérez l’étiquette dans la pochette transparente accolée à l’appareil. Vérifiez que l’étiquette est visible. Retirez les éventuels autocollants qui ont été accolés à l’appareil lors de la précédente expédition.

    ![Exemple d’étiquette d’expédition](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Modifier l’adresse de livraison

Il peut arriver que vous deviez modifier l’adresse de livraison une fois la commande passée. Cela est possible uniquement avant l’envoi de l’appareil. Une fois l’appareil expédié, vous ne pouvez plus modifier l’adresse de livraison.

Pour modifier la commande, procédez comme suit.

1. Accédez à **Détails de la commande > Modifier l’adresse de livraison**.

    ![Modification de l’adresse de livraison 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Modifiez et validez l’adresse de livraison, puis enregistrez les modifications.

    ![Modification de l’adresse de livraison 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Modifier les détails de notification

Vous devrez peut-être modifier les utilisateurs qui recevront les e-mails indiquant l’état de la commande. Par exemple, un utilisateur doit être informé dès que l’appareil est livré ou récupéré. Un autre utilisateur doit quant à lui être informé lorsque la copie des données est terminée afin qu’il puisse vérifier que les données se trouvent bien dans le compte de stockage Azure avant de les supprimer de la source. Dans ce cas, vous pouvez modifier les détails de notification.

Pour modifier les détails de modification, procédez comme suit.

1. Accédez à **Détails de la commande > Modifier les détails de notification**.

    ![Modification des détails de notification 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Vous pouvez maintenant modifier les détails de notification et enregistrer les modifications.
 
    ![Modification des détails de notification 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="view-order-status"></a>Afficher l’état de la commande

Vous êtes informé par courrier électronique dès que l’état de l’appareil change dans le portail.

|État de la commande |Description |
|---------|---------|
|Ordered (Validée)     | La commande a été validée. <br>Si l’appareil est disponible, Microsoft identifie l’appareil à expédier et le prépare. <br> Si l’appareil n’est pas disponible immédiatement, la commande sera traitée lorsque l’appareil devient disponible. Le traitement de la commande peut prendre quelques jours à deux mois. Si elle ne peut pas être traitée sous 90 jours, la commande est annulée et vous en êtes informé.         |
|Processed (Traitée)     | La commande a été traitée. Conformément à votre commande, l’appareil est prêt pour expédition dans le centre de données.         |
|Dispatched (Distribuée)     | La commande a été expédiée. Utilisez l’ID de suivi affiché sur votre commande dans le portail pour effectuer le suivi de l’expédition.        |
|Delivered (Livrée)     | Le colis a été livré à l’adresse spécifiée dans la commande.        |
|Picked up (Récupérée)     |Le colis de retour a été récupéré et scanné par le transporteur.         |
|Reçu     | Votre appareil a été reçu et scanné au centre de données Azure. <br> Une fois le colis inspecté, le téléchargement de l’appareil commencera.      |
|Copie de données     | La copie des données est en cours. Suivez la progression de la copie de votre commande sur le portail Azure. <br> Attendez que la copie des données se termine. |
|Completed       |La commande a été terminée avec succès.<br> Vérifiez que vos données se trouvent bien dans Azure avant de supprimer les données locales des serveurs.         |
|Completed with errors (Terminée avec des erreurs)| La copie des données a été effectuée mais des erreurs se sont produites pendant l’opération. <br> Consultez les journaux de copie en utilisant le chemin d’accès spécifié dans le portail Azure.   |
|Canceled            |La commande a été annulée. <br> Soit vous avez annulé la commande, soit une erreur s’est produite et le service a annulé la commande. Si elle ne peut pas être traitée sous 90 jours, la commande est également annulée et vous en êtes informé.     |
|Nettoyer | Les données sur les disques de l’appareil sont effacées. Le nettoyage de l’appareil est considéré comme terminé lorsque le rapport de la commande est disponible dans le portail Azure.|



## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [résoudre les problèmes relatifs à Data Box](data-box-faq.md).
