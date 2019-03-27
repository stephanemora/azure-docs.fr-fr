---
title: Renvoyer des disques Microsoft Azure Data Box | Microsoft Docs
description: Utilisez ce didacticiel pour savoir comment renvoyer vos disques Azure Data Box à Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 0dd0474ad1ad360fd82cfdf746d2e9837f74833a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108373"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Tutoriel : Retourner des disques Azure Data Box et vérifier le chargement des données dans Azure

Ce tutoriel est le dernier de la série : Déployer Azure Data Box Disk. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Expédier le disque Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données du disque Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez suivi le [tutoriel : Copier des données sur Azure Data Box Disk et procéder à une vérification](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Renvoyer un disque Data Box

1. Une fois que la validation des données est terminée, déconnectez les disques. Débranchez les câbles de connexion.
2. Emballez tous les disques et les câbles de connexion dans du papier à bulles et placez le tout dans la boîte d’envoi.
3. Utilisez l’étiquette de retour figurant dans la pochette en plastique transparente collée sur la boîte. Si l’étiquette a été endommagée ou perdue, téléchargez une nouvelle étiquette d’expédition à partir du portail Azure et collez-la sur la boîte. Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**.

    ![Télécharger une étiquette d’expédition](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    Cette action permet de télécharger une étiquette de retour comme illustré ci-dessous.

    ![Exemple d’étiquette d’expédition](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Scellez la boîte d’envoi et assurez-vous que l’étiquette de retour est visible.
5. Planifiez une date d’enlèvement avec UPS en cas de renvoi aux États-Unis. Si vous renvoyez l’appareil en Europe via DHL, faites une demande d’enlèvement auprès de DHL en vous rendant sur leur site web et en spécifiant le numéro de fiche de transport aérien. Accédez au site web DHL Express local et choisissez **Book a Courier Collection (Réserver un enlèvement) > eReturn Shipment (Expédition eReturn)**.

    ![Expédition du retour par DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    Spécifiez le numéro de la fiche de transport et cliquez sur **Planifier l’enlèvement** pour choisir une date.

      ![Planifier l’enlèvement](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. Une fois que les disques ont été récupérés par le transporteur, l’état de la commande dans le portail affiche **Picked up (Récupérée)**. Un ID de suivi s’affiche également.

    ![Disques récupérés](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Lorsque Microsoft reçoit et analyse le disque, l’état de la commande est défini sur **Received (Reçue)**. 

![Disques reçus](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Les données sont copiées automatiquement une fois que les disques sont connectés à un serveur dans le centre de données Azure. Selon la taille des données, l’opération de copie peut prendre de quelques heures à quelques jours. Vous pouvez suivre la progression du travail de copie dans le portail.

Une fois la copie terminée, la commande passe à l’état **Completed (Terminée)**.

![Copie de données terminée](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source. Vos données peuvent se trouver aux emplacements suivants :

- Votre ou vos comptes de stockage Azure. Lorsque vous copiez les données sur Data Box, selon le type, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.

  - Pour les objets blob de blocs et de pages : `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Pour les fichiers Azure : `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

- Votre ou vos groupes de ressources spécifiés pour les disques managés. Quand vous créez des disques managés, les disques durs virtuels sont chargés en tant qu’objets blob de pages, puis convertis en disques managés. Les disques managés sont attachés aux groupes de ressources spécifiés au moment de la création de la commande.

  - Si la copie vers des disques managés dans Azure a réussi, vous pouvez accéder aux **détails de la commande** dans le portail Azure et noter le nom du groupe de ressources spécifié pour les disques managés.

      ![Voir les détails de la commande](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Accédez au groupe de ressources noté précédemment pour trouver vos disques managés.

      ![Groupe de ressources pour les disques managés](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Si vous avez copié un VHDX ou un VHD dynamique ou de différenciation, le VHDX/VHD est chargé dans le compte de stockage de préproduction comme objet blob de blocs. Accédez à votre compte de stockage de préproduction (**Compte de stockage > Objets blob**), puis sélectionnez le conteneur approprié (StandardSSD, StandardHDD ou PremiumSSD). Les disques VHDX/VHD doivent s’afficher en tant qu’objets blob de blocs dans votre compte de stockage de préproduction.

Pour vérifier que les données ont bien été chargées dans Azure, procédez comme suit :

1. Accédez au compte de stockage associé à votre commande de disque.
2. Accédez à **Service BLOB > Parcourir les objets blob**. La liste des conteneurs apparaît. En fonction des sous-dossiers que vous avez créés dans les dossiers *BlockBlob* et *PageBlob*, des conteneurs de même nom sont créés dans votre compte de stockage.
    Si les noms des dossiers ne sont pas conformes aux conventions d’affectation de noms Azure, les données ne seront pas chargées dans Azure.

4. Pour vérifier que l’ensemble du jeu de données a été chargé, utilisez l’Explorateur Stockage Microsoft Azure. Associez le compte de stockage correspondant à la commande de location de disque, puis consultez la liste des conteneurs d’objets blob. Sélectionnez un conteneur, cliquez sur **...Plus**, puis cliquez sur **Statistiques des dossiers**. Le volet **Activités** affiche les statistiques de ce dossier, y compris le nombre d’objets blob et la taille totale des objets blob. La taille totale des objets blob, exprimée en octets, doit correspondre à la taille du jeu de données.

    ![Statistiques des dossiers dans l’explorateur de stockage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Effacer les données du disque Data Box

Une fois que la copie est terminée et que vous avez vérifié que les données se trouvent bien dans le compte de stockage Azure, les disques sont effacés de manière sécurisée conformément à la norme NIST.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances sur les disques Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Expédier le disque Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données du disque Data Box


Passez au guide pratique suivant pour savoir comment gérer des disques Data Box via le portail Azure.

> [!div class="nextstepaction"]
> [Utiliser le portail Azure pour administrer le disque Azure Data Box](./data-box-portal-ui-admin.md)


