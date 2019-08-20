---
title: Tutoriel sur le renvoi d’Azure Data Box Disk | Microsoft Docs
description: Utilisez ce didacticiel pour savoir comment renvoyer vos disques Azure Data Box à Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 9e3159a083d21e4a7a81df2fcf7a7d5db882b757
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976911"
---
::: zone target="docs"

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

    - Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays/région).
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
4. Accédez au site web DHL Express du pays ou de la région et choisissez **Book a Courier Collection (Réserver un enlèvement) > eReturn Shipment (Expédition eReturn)** .

    ![Expédition du retour par DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Spécifiez le numéro de la fiche de transport et cliquez sur **Planifier l’enlèvement** pour choisir une date.

      ![Planifier l’enlèvement](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Enlèvement dans la région Asie-Pacifique

Les instructions concernant cette région s’appliquent à l’enlèvement au Japon, en Corée, en Australie et à Singapour.

#### <a name="pick-up-in-australia"></a>Enlèvement en Australie

Les centres de données Azure de l’Australie ont une notification de sécurité supplémentaire. Toutes les expéditions entrantes doivent avoir une notification préalable. Effectuez les étapes suivantes pour l’enlèvement en Australie.

1. Envoyez un e-mail à `adbops@microsoft.com` pour demander l’étiquette d’expédition avec un ID entrant unique ou le code TAU. Faites votre demande au moins 3 jours avant la date d’expédition prévue pour obtenir l’étiquette à temps.
2. L’objet de l’e-mail doit être : *Demande d’étiquette d’expédition inverse avec code TAU*. Veillez à inclure les informations suivantes dans l’e-mail : 

    - Nom de la commande
    - Adresse
    - Nom du contact

#### <a name="pick-up-in-japan"></a>Enlèvement au Japon

1. Écrivez le nom et l’adresse de votre entreprise dans la partie du bordereau de réexpédition réservée à l’expéditeur.
2. Envoyez un courrier à Quantium Solutions en utilisant le modèle d’e-mail suivant.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **Si l’enlèvement doit se faire à Osaka**, remplacez l’objet du modèle d’e-mail par celui-ci : `Pickup request for Microsoft Azure OSA`.
    - Si le bordereau de réexpédition de Japon Post Chakubarai était manquant, consignez ce fait que dans cet e-mail. Quantium Solutions demandera à Japan Post d’apporter le bordereau de réexpédition à l’enlèvement.
    - Si vous avez plusieurs commandes, envoyez un e-mail pour garantir un enlèvement individuel.

3. Recevoir un e-mail de confirmation de Quantium Solutions près avoir réservé un enlèvement. L’e-mail de confirmation inclut également des informations sur le bordereau de réexpédition de Chakubarai.

Si nécessaire, vous pouvez contacter le support de Quantium Solutions (en langue japonaise) dont les coordonnées sont les suivantes : 

- E-mail ：Customerservice.JP@quantiumsolutions.com 
- Téléphone : 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Enlèvement en Corée

1. Veillez à inclure le bordereau de réexpédition.
2. Pour demander un enlèvement lorsque le bordereau de réexpédition est présent :
    1. Appelez l’assistance téléphonique de *Quantium Solutions International* au 070-8231-1418 pendant les heures de bureau (du lundi au vendredi de 10 h à 17 h). Indiquez *Enlèvement Microsoft Azure* et le numéro de la demande de service pour organiser l’enlèvement.  
    2. Si l’assistance téléphonique est occupée, envoyez un e-mail à `microsoft@rocketparcel.com`, avec l’objet *Enlèvement Microsoft Azure* et le numéro de demande de service comme référence.
    3. Si le transporteur ne procède pas à l’enlèvement, appelez l’assistance téléphonique de *Quantium Solutions International* pour trouver une autre solution. 
    4. Vous recevez un e-mail de confirmation pour la planification de l’enlèvement.
3. Effectuez cette étape uniquement si le bordereau de réexpédition n’est pas présent. Pour demander un enlèvement :
    1. Appelez l’assistance téléphonique de *Quantium Solutions International* au 070-8231-1418 pendant les heures de bureau (du lundi au vendredi de 10 h à 17 h). Indiquez *Enlèvement Microsoft Azure* et le numéro de la demande de service pour organiser l’enlèvement. Spécifiez que vous avez besoin d’un nouveau bordereau de réexpédition pour organiser un enlèvement. Fournissez à l’expéditeur (client) les informations du destinataire (centre de données Azure) et le numéro de référence (numéro de la demande de service). 
    2. Si l’assistance téléphonique est occupée, envoyez un e-mail à `microsoft@rocketparcel.com`, avec l’objet *Enlèvement Microsoft Azure* et le numéro de demande de service comme référence.
    3. Si le transporteur ne procède pas à l’enlèvement, appelez l’assistance téléphonique de *Quantium Solutions International* pour trouver une autre solution. 
    4. Si la demande est effectuée par téléphone, vous recevez une confirmation verbale.

### <a name="pick-up-in-singapore"></a>Enlever à Singapour

1. Imprimez l’étiquette d’expédition et apposez-la sur la boîte. Si l’étiquette est endommagée ou perdue :
    - Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**.

        ![Télécharger une étiquette d’expédition](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Cette action permet de télécharger une étiquette de retour comme illustré ci-dessous.

        ![Exemple d’étiquette d’expédition](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Collez l’étiquette sur l’appareil. Vérifiez que l’étiquette est visible.

2. Pour demander un enlèvement :
    - Appelez **SingPost** au **6845 6485** pendant des heures de bureau (de 9 à 17 h, du lundi au vendredi).  
    - Indiquez *Enlèvement Microsoft Azure* et le numéro de la demande de service (numéro de suivi sur l’étiquette de réexpédition) pour organiser l’enlèvement. 
    - Vous recevrez une confirmation verbale pour la planification de l’enlèvement. 
    - Si le transporteur ne se présente pas pour l’enlèvement, appelez **SingPost** au **6845 6485** pour trouver une autre solution. 
3. Remettez le colis au transporteur. 


## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Une fois que les disques ont été récupérés par le transporteur, l’état de la commande dans le portail affiche **Picked up (Récupérée)** . Un ID de suivi s’affiche également.

![Disques récupérés](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Lorsque Microsoft reçoit et analyse le disque, l’état de la commande est défini sur **Received (Reçue)** . 

![Disques reçus](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Les données sont copiées automatiquement une fois que les disques sont connectés à un serveur dans le centre de données Azure. Selon la taille des données, l’opération de copie peut prendre de quelques heures à quelques jours. Vous pouvez suivre la progression du travail de copie dans le portail.

Une fois la copie terminée, la commande passe à l’état **Completed (Terminée)** .

![Copie de données terminée](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Si la copie de termine avec des erreurs, voir [résoudre des erreurs de chargement](data-box-disk-troubleshoot-upload.md).

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

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>Retourner le disque et vérifier le chargement sur Azure

## <a name="ship-to-azure"></a>Envoyer à Azure

1. Une fois la validation des données terminée, déconnectez les disques. Débranchez les câbles de connexion.
2. Emballez tous les disques et les câbles de connexion dans du papier à bulles et placez le tout dans la boîte d’envoi. Des frais peuvent s’appliquer si les accessoires sont manquants.
    - Réutilisez l’emballage de la première expédition.  
    - Nous vous recommandons d’emballer les disques avec du papier bulle pour les protéger.
    - Vérifiez qu’ils sont suffisamment serrés dans la boîte pour éviter qu’ils ne bougent.
3. Les étapes suivantes sont déterminées par l’endroit où vous retournez l’appareil.
    - [Planifiez une date d’enlèvement avec UPS en cas de renvoi aux États-Unis et au Canada](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
    - [Planifiez une date d’enlèvement auprès de DHL pour l’Europe](data-box-disk-deploy-picked-up.md#pick-up-in-europe) en visitant son site web et en spécifiant le numéro de facture de transport aérien.
    - [Planifiez une date d’enlèvement pour les pays de la région Australie-Pacifique](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region), tels que l’Australie, le Japon, la Corée et Singapour.
4. Une fois que les disques ont été récupérés par le transporteur, l’état de la commande dans le portail est mis à jour et l’ID de suivi est affiché.

## <a name="verify-upload-to-azure"></a>Vérifier le chargement sur Azure

Une fois les données chargées sur Azure, vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source. Vos données peuvent se trouver aux emplacements suivants :

- Votre ou vos comptes de stockage Azure. Lorsque vous copiez les données sur Data Box, selon le type, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.

    - **Pour les objets blob de blocs et de pages** : https://<nom_compte_stockage>.blob.core.windows.net/<containername>/files/a.txt

    - **Pour Azure Files** : https://<nom_compte_stockage>.file.core.windows.net/<sharename>/files/a.txt

    Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

- Votre ou vos groupes de ressources spécifiés pour les disques managés. Quand vous créez des disques managés, les disques durs virtuels sont chargés en tant qu’objets blob de pages, puis convertis en disques managés. Les disques managés sont attachés aux groupes de ressources spécifiés au moment de la création de la commande.

::: zone-end


