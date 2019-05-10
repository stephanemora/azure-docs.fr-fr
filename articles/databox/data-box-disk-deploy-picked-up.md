---
title: Tutoriel sur le renvoi d’Azure Data Box Disk | Microsoft Docs
description: Utilisez ce didacticiel pour savoir comment renvoyer vos disques Azure Data Box à Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 023542dbc22234fc57e4ce8b662a9760be4efe04
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150766"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Didacticiel : Retourner des disques Azure Data Box et vérifier le chargement des données dans Azure

Ce tutoriel est le dernier de la série : Déployer Azure Data Box Disk. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Expédier le disque Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données du disque Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez suivi le [tutoriel : Copier des données sur Azure Data Box Disk et procéder à une vérification](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Renvoyer un disque Data Box

1. Une fois que la validation des données est terminée, déconnectez les disques. Débranchez les câbles de connexion.
2. Emballez tous les disques et les câbles de connexion dans du papier à bulles et placez le tout dans la boîte d’envoi. Des frais peuvent s’appliquer si les accessoires sont manquants.
    - Réutilisez l’emballage de la première expédition.  
    - Nous vous recommandons d’emballer les disques avec du papier bulle pour les protéger.
    - Vérifiez qu’ils sont suffisamment serrés dans la boîte pour éviter qu’ils ne bougent.

Les étapes suivantes sont déterminées par l’endroit où vous retournez l’appareil.

### <a name="pick-up-in-us-canada"></a>Enlèvement aux États-Unis et au Canada

Effectuez les étapes suivantes si le retour de l’appareil s’effectue aux États-Unis ou au Canada.

1. Utilisez l’étiquette de retour figurant dans la pochette en plastique transparente collée sur la boîte. Si l’étiquette est endommagée ou perdue :
    - Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**.

        ![Télécharger une étiquette d’expédition](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Cette action permet de télécharger une étiquette de retour comme illustré ci-dessous.

        ![Exemple d’étiquette d’expédition](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Collez l’étiquette sur l’appareil.

2. Scellez la boîte d’envoi et assurez-vous que l’étiquette de retour est visible.
3. Planifiez un enlèvement avec UPS. Pour planifier l’enlèvement :

    - Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays).
    - Lors de votre appel, indiquez le numéro de suivi de retour mentionné sur votre étiquette imprimée.
    - Si le numéro de suivi n’est pas indiqué, UPS vous facturera des frais supplémentaires à l’enlèvement.
    - Au lieu de planifier l’enlèvement, vous pouvez également déposer le disque Data Box Disk à l’emplacement de dépôt le plus proche.


### <a name="pick-up-in-europe"></a>Enlèvement en Europe

Effectuez les étapes suivantes si le retour de l’appareil s’effectue en Europe.

1. Utilisez l’étiquette de retour figurant dans la pochette en plastique transparente collée sur la boîte. Si l’étiquette est endommagée ou perdue :
    - Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**.

        ![Télécharger une étiquette d’expédition](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Cette action permet de télécharger une étiquette de retour comme illustré ci-dessous.

        ![Exemple d’étiquette d’expédition](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Collez l’étiquette sur l’appareil.

2. Scellez la boîte d’envoi et assurez-vous que l’étiquette de retour est visible.
3. Si vous renvoyez l’appareil en Europe via DHL, faites une demande d’enlèvement auprès de DHL en vous rendant sur leur site web et en spécifiant le numéro de fiche de transport aérien.
4. Accédez au site web DHL Express local et choisissez **Book a Courier Collection (Réserver un enlèvement) > eReturn Shipment (Expédition eReturn)**.

    ![Expédition du retour par DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Spécifiez le numéro de la fiche de transport et cliquez sur **Planifier l’enlèvement** pour choisir une date.

      ![Planifier l’enlèvement](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Enlèvement dans la région Asie-Pacifique

Les instructions concernant cette région s’appliquent à l’enlèvement au Japon, en Corée et en Australie.

#### <a name="pick-up-in-australia"></a>Enlèvement en Australie

Les centres de données Azure de l’Australie ont une notification de sécurité supplémentaire. Toutes les expéditions entrantes doivent avoir une notification préalable. Effectuez les étapes suivantes pour l’enlèvement en Australie.

1. Envoyez un e-mail à `adbops@microsoft.com` pour demander l’étiquette d’expédition avec un ID entrant unique ou le code TAU. Faites votre demande au moins 3 jours avant la date d’expédition prévue pour obtenir l’étiquette à temps.
2. L’objet de l’e-mail doit être : *Demande d’étiquette d’expédition inverse avec code TAU*. Veillez à inclure les informations suivantes dans l’e-mail : 

    - Nom de la commande
    - Adresse
    - Nom du contact

#### <a name="pick-up-in-japan"></a>Enlèvement au Japon

1. Veillez à inclure le bordereau d’expédition de retour de Japan Post Chakubarai.
2. Écrivez le nom et l’adresse de votre entreprise dans la partie réservée à l’expéditeur du bordereau d’expédition.
3. Appelez Japan Post au 0800-0800-111 (appel gratuit) pour faire une demande d’enlèvement. Composez le code postal à 7 chiffres de l’adresse d’enlèvement et transmettez-le au bureau de poste le plus proche de chez vous.
    - Les heures d’enlèvement disponibles varient selon le bureau de poste.
    - Indiquez que l’expédition est un colis (Yu-Pack) Japan Post Chakubarai.
    - Utilisez le bordereau d’expédition Chakubarai inclus.
4. Si le bordereau d’expédition Japan Post Chakubarai n’était pas inclus, envoyez un e-mail à *Quantium Solutions* à l’adresse `Customerservice.JP@quantiumsolutions.com`. *Quantium Solutions* demandera à Japan Post d’enlever votre colis et d’apporter le bordereau d’expédition à cette occasion.
    - Indiquez le numéro de référence dans la colonne réservée aux remarques sur le bordereau d’expédition Chakubarai que Japan Post doit apporter.
    - Entrez l’adresse de livraison comme illustré ci-dessous :   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-3-6 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

Si le bordereau d’expédition Chakubarai est manquant, vous pouvez demander l’enlèvement par e-mail. Utilisez le modèle d’e-mail suivant pour demander l’enlèvement.

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>Enlèvement en Corée

1. Veillez à inclure le bordereau d’expédition de retour.
2. Pour demander un enlèvement :
    1. Appelez l’assistance téléphonique de *Quantium Solutions International* au 070-8231-1418 pendant les heures de bureau (du lundi au vendredi de 10 h à 17 h). Indiquez *Enlèvement Microsoft* et le numéro du bordereau d’expédition pour planifier un enlèvement.  
    2. Si le numéro de l’assistance téléphonique est occupé, envoyez un e-mail à `microsoft@rocketparcel.com`, avec l’objet *Enlèvement Microsoft* et le numéro du bordereau d’expédition comme référence.
    3. Si le transporteur ne procède pas à l’enlèvement, appelez l’assistance téléphonique de *Quantium Solutions International* pour trouver une autre solution. 

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Une fois que les disques ont été récupérés par le transporteur, l’état de la commande dans le portail affiche **Picked up (Récupérée)**. Un ID de suivi s’affiche également.

![Disques récupérés](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

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


