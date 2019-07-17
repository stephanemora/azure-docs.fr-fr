---
title: Tutoriel sur le renvoi d’Azure Data Box | Microsoft Docs
description: Découvrez comment expédier l’appareil Azure Data Box à Microsoft.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 7/08/2019
ms.author: alkohli
ms.openlocfilehash: db0f0ac3073687b7c1cd8ca60e459e4bb3aa03f4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626354"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Didacticiel : Retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure

Ce didacticiel vous explique comment retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure.

Ce didacticiel fournit des informations sur les sujets suivants :

> [!div class="checklist"]
> * Prérequis
> * Préparer l’expédition
> * Expédier la Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données de la Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez les points suivants :

- Vous avez suivi le [Tutoriel : Copier des données sur Azure Data Box et procéder à une vérification](data-box-deploy-copy-data.md). 
- Les tâches de copie sont terminées. La préparation de l’expédition ne peut pas s’effectuer si les tâches de copie sont en cours d’exécution.

## <a name="prepare-to-ship"></a>Préparer l’expédition

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Renvoyer la Data Box

Vérifiez que la copie des données sur l’appareil a été effectuée et que la **préparation de l’expédition** a réussi. En fonction de la région où vous expédiez l’appareil, la procédure est différente.


### <a name="ship-in-us-canada-europe"></a>Expédier aux États-Unis, au Canada et en Europe

Effectuez les étapes suivantes si vous retournez l’appareil aux États-Unis, au Canada ou en Europe.

1. Vérifiez que l’appareil est hors tension et que les câbles sont débranchés. 
2. Enroulez le cordon d’alimentation fourni avec la Data Box et fixez-le à l’arrière de l’appareil.
3. Assurez-vous que l’étiquette d’expédition est affichée sur l’écran E-ink et planifiez l’enlèvement avec votre transporteur. Si l’étiquette est endommagée ou perdue, ou si elle ne s’affiche pas sur l’écran E-ink, contactez le support Microsoft. Si le support technique le suggère, vous pouvez accéder à **Vue d’ensemble > Télécharger une étiquette d’expédition** dans le portail Azure. Téléchargez l’étiquette d’expédition et collez-la sur l’appareil. 
4. Planifiez une date d’enlèvement avec UPS en cas de renvoi de l’appareil. Pour planifier l’enlèvement :

    - Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays/région).
    - Lors de l’appel, indiquez le numéro de suivi du retour, qui est indiqué dans l’écran E-ink ou sur l’étiquette de retour imprimée.
    - Si le numéro de suivi n’est pas indiqué, UPS vous facturera des frais supplémentaires à l’enlèvement.

    Au lieu de planifier l’enlèvement, vous pouvez déposer la Data Box dans le point de dépôt le plus proche.
4. Une fois que la Data Box a été scannée et récupérée par le transporteur, l’état de la commande dans le portail affiche **Récupérée**. Un ID de suivi s’affiche également.

### <a name="ship-in-asia-pacific-region"></a>Expédier dans la région Asie-Pacifique

#### <a name="ship-in-australia"></a>Expédier en Australie

Les centres de données Azure de l’Australie ont une notification de sécurité supplémentaire. Toutes les expéditions entrantes doivent avoir une notification préalable. Effectuez les étapes suivantes pour expédier en Australie.


1. Conservez la boîte d’origine utilisée pour expédier l’appareil dans le cadre d’un retour.
2. Vérifiez que la copie des données sur l’appareil a été effectuée et que la **préparation de l’expédition** a réussi.
3. Mettez l’appareil hors tension et débranchez les câbles.
4. Enroulez le cordon d’alimentation fourni avec l’appareil, et fixez-le à l’arrière de ce dernier.
5. Envoyez un e-mail à Quantium Solutions pour demander un enlèvement. Consultez le numéro de référence de service spécifié sur le portail Azure. Utilisez le modèle d’e-mail suivant : - *Demande d’étiquette d’expédition inverse avec code TAU*. Veillez à inclure les informations suivantes dans l’e-mail : 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions Australia vous enverra par e-mail une étiquette d’expédition pour le retour.
7. Imprimez l’étiquette de retour et collez-la sur la boîte d’expédition.
8. Remettez le colis au transporteur.

Si nécessaire, envoyez un e-mail au Support Quantium Solution à l’adresse Azure@quantiumsolutions.com, ou contactez-les par téléphone.


Pour toute demande relative à votre commande par téléphone :

- Envoyez d’abord un e-mail pour l’enlèvement.
- Indiquez votre nom de commande par téléphone.

#### <a name="ship-in-japan"></a>Expédier au Japon 

1. Conservez la boîte d’origine utilisée pour expédier l’appareil dans le cadre d’un retour.
2. Mettez l’appareil hors tension et débranchez les câbles.
3. Enroulez le cordon d’alimentation fourni avec l’appareil, et fixez-le à l’arrière de ce dernier.
4. Écrivez le nom et l’adresse de votre entreprise dans la partie du bordereau de réexpédition réservée à l’expéditeur.
5. Envoyez un courrier à Quantium Solutions en utilisant le modèle d’e-mail suivant.

    - Si le bordereau de réexpédition de Japon Post Chakubarai était manquant, consignez ce fait que dans cet e-mail. Quantium Solutions demandera à Japan Post d’apporter le bordereau de réexpédition à l’enlèvement.
    - Si vous avez plusieurs commandes, envoyez un e-mail pour garantir un enlèvement individuel.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Recevoir un e-mail de confirmation de Quantium Solutions près avoir réservé un enlèvement. L’e-mail de confirmation inclut également des informations sur le bordereau de réexpédition de Chakubarai.

Si nécessaire, vous pouvez contacter le support de Quantium Solutions (en langue japonaise) dont les coordonnées sont les suivantes : 

- E-mail ：Customerservice.JP@quantiumsolutions.com 
- Téléphone：+81 3 57 55 01 50 


## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Lorsque Microsoft reçoit et analyse l’appareil, l’état de la commande est mis à jour sur **Reçue**. L’appareil subit une vérification physique afin de détecter des dommages ou des signes d’altération potentiels.

Une fois la vérification terminée, la Data Box est connectée au réseau du centre de données Azure. La copie des données démarre automatiquement. Selon la taille des données, l’opération de copie peut prendre de quelques heures à quelques jours. Vous pouvez suivre la progression du travail de copie dans le portail.

Une fois la copie terminée, la commande passe à l’état **Completed (Terminée)** .

Vérifiez que vos données ont été chargées dans Azure avant de les supprimer de la source. Vos données peuvent se trouver aux emplacements suivants :

- Votre ou vos comptes de stockage Azure. Lorsque vous copiez les données sur Data Box, selon le type, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.

  - Pour les objets blob de blocs et de pages : `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Pour les fichiers Azure : `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

- Votre ou vos groupes de ressources spécifiés pour les disques managés. Quand vous créez des disques managés, les disques durs virtuels sont chargés en tant qu’objets blob de pages, puis convertis en disques managés. Les disques managés sont attachés aux groupes de ressources spécifiés au moment de la création de la commande. 

    - Si la copie vers des disques managés dans Azure a réussi, vous pouvez accéder aux **détails de la commande** dans le portail Azure et noter les noms des groupes de ressources spécifiés pour les disques managés.

        ![Identifier les groupes de ressources spécifiés pour les disques managés](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Accédez au groupe de ressources noté précédemment pour trouver vos disques managés.

        ![Disque managé attaché à des groupes de ressources](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Si vous avez copié un VHDX, ou un VHD dynamique ou de différenciation, le VHDX/VHD est chargé dans le compte de stockage de préproduction comme un objet blob de pages, mais la conversion du VHD en disque managé échoue. Accédez à votre compte de stockage de préproduction (**Compte de stockage > Objets blob**), puis sélectionnez le conteneur approprié (SSD Standard, HDD Standard ou SSD Premium). Les disques durs virtuels sont chargés en tant qu’objets blob de pages dans votre compte de stockage de préproduction.

## <a name="erasure-of-data-from-data-box"></a>Effacer les données de la Data Box
 
Une fois que le chargement des données vers Azure est terminé, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Prérequis
> * Préparer l’expédition
> * Expédier la Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données de la Data Box

Passez à l’article suivant pour apprendre à gérer la Data Box via l’interface utilisateur web locale.

> [!div class="nextstepaction"]
> [Administrer l’appareil Azure Data Box à l’aide de l’interface utilisateur web locale](./data-box-local-web-ui-admin.md)


