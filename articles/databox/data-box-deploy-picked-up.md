---
title: Renvoyer l’appareil Microsoft Azure Data Box | Microsoft Docs
description: Découvrez comment expédier l’appareil Azure Data Box à Microsoft.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: alkohli
ms.openlocfilehash: b948616aa21d390cedc877b0a09e272815abc82a
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358556"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutoriel : Retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure

Ce didacticiel vous explique comment retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure.

Ce didacticiel fournit des informations sur les sujets suivants :

> [!div class="checklist"]
> * Expédier la Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données de la Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez suivi le [tutoriel : Copier des données sur Azure Data Box et procéder à une vérification](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Renvoyer la Data Box

1. Assurez-vous que l’appareil est hors tension et que les câbles sont débranchés. Enroulez le cordon d’alimentation fourni avec la Data Box et fixez-le à l’arrière de l’appareil.
2. Si l’appareil est expédié aux États-Unis, assurez-vous que l’étiquette d’expédition est affichée sur l’écran E-ink et planifiez l’enlèvement avec votre transporteur. Si l’étiquette a été endommagée, perdue, ou qu’elle n’est pas affichée sur l’écran E-ink, téléchargez l’étiquette d’expédition à partir du Portail Azure et collez-la sur l’appareil. Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**. 

    Si l’appareil est prévu pour fonctionner en Europe, l’écran E-ink n’affiche pas l’étiquette d’expédition. Au lieu de cela, l’étiquette d’expédition de retour est incluse dans la pochette transparente sous l’étiquette d’expédition. Supprimez l’ancienne étiquette d’expédition et vérifiez que l’étiquette d’expédition de retour est clairement visible.
    
3. Planifiez une date d’enlèvement avec UPS en cas de renvoi de l’appareil. Pour planifier une date d’enlèvement, appelez le service UPS local (numéro vert propre au pays) ou déposez l’appareil Data Box au dépôt le plus proche.

4. Une fois que la Data Box a été scannée et récupérée par le transporteur, l’état de la commande dans le portail affiche **Récupérée**. Un ID de suivi s’affiche également.

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Lorsque Microsoft reçoit et analyse l’appareil, l’état de la commande est mis à jour sur **Reçue**. L’appareil subit une vérification physique afin de détecter des dommages ou des signes d’altération potentiels. 

Une fois la vérification terminée, la Data Box est connectée au réseau du centre de données Azure. La copie des données démarre automatiquement. Selon la taille des données, l’opération de copie peut prendre de quelques heures à quelques jours. Vous pouvez suivre la progression du travail de copie dans le portail.

Une fois la copie terminée, la commande passe à l’état **Completed (Terminée)**.

Vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source. Lorsque vous copiez les données sur Data Box, selon le type, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.

- Pour les objets blob de blocs et de pages : `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
- Pour les fichiers Azure : `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

## <a name="erasure-of-data-from-data-box"></a>Effacer les données de la Data Box
 
Une fois que le chargement des données vers Azure est terminé, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Expédier la Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données de la Data Box

Passez à l’article suivant pour apprendre à gérer la Data Box via l’interface utilisateur web locale.

> [!div class="nextstepaction"]
> [Administrer l’appareil Azure Data Box à l’aide de l’interface utilisateur web locale](./data-box-local-web-ui-admin.md)


