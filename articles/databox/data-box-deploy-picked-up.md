---
title: Renvoyer l’appareil Microsoft Azure Data Box | Microsoft Docs
description: Découvrez comment expédier l’appareil Azure Data Box à Microsoft.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: d649095a6b1b9f692a6795e96c9f15631d36e3e2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974508"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Didacticiel : Renvoyer l’appareil Azure Data Box et vérifier le chargement des données dans Azure

Ce didacticiel vous explique comment retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure.

Ce didacticiel fournit des informations sur les sujets suivants :

> [!div class="checklist"]
> * Expédier la Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données de la Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que vous avez suivi le [Didacticiel : Copier des données sur Azure Data Box et procéder à une vérification](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Renvoyer la Data Box

1. Assurez-vous que l’appareil est hors tension et que les câbles sont débranchés. Enroulez le cordon d’alimentation fourni avec la Data Box et fixez-le à l’arrière de l’appareil.
2. Assurez-vous que l’étiquette d’expédition est affichée sur l’écran E-ink et planifiez l’enlèvement avec votre transporteur. Si l’étiquette a été endommagée, perdue, ou qu’elle n’est pas affichée sur l’écran E-ink, téléchargez l’étiquette d’expédition à partir du Portail Azure et collez-la sur l’appareil. Accédez à **Vue d’ensemble > Télécharger une étiquette d’expédition**.
3. Planifiez une date d’enlèvement avec UPS en cas de renvoi aux États-Unis. Si vous renvoyez l’appareil en Europe via DHL, faites une demande d’enlèvement auprès de DHL en vous rendant sur leur site web et en spécifiant le numéro de fiche de transport aérien. Accédez au site web DHL Express local et choisissez **Book a Courier Collection (Réserver un enlèvement) > eReturn Shipment (Expédition eReturn)**. 

    Spécifiez le numéro de la fiche de transport et cliquez sur **Planifier l’enlèvement** pour choisir une date.

4. Une fois que la Data Box a été scannée et récupérée par le transporteur, l’état de la commande dans le portail affiche **Récupérée**. Un ID de suivi s’affiche également.

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Lorsque Microsoft reçoit et analyse l’appareil, l’état de la commande est mis à jour sur **Reçue**. L’appareil subit une vérification physique afin de détecter des dommages ou des signes d’altération potentiels. 

Une fois la vérification terminée, la Data Box est connectée au réseau du centre de données Azure. La copie des données démarre automatiquement. Selon la taille des données, l’opération de copie peut prendre de quelques heures à quelques jours. Vous pouvez suivre la progression du travail de copie dans le portail.

Une fois la copie terminée, la commande passe à l’état **Completed (Terminée)**.

Vérifiez que vos données se trouvent dans les comptes de stockage avant de les supprimer de la source. 

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


