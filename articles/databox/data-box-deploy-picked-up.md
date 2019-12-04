---
title: Tutoriel sur le renvoi d’Azure Data Box | Microsoft Docs
description: Découvrez comment expédier l’appareil Azure Data Box à Microsoft.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1c375370ec6bae2775ad758688825b92cbbbca50
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407040"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Didacticiel : Retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Retourner l’appareil Data Box et vérifier le chargement des données dans Azure

::: zone-end

::: zone target="docs"

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

::: zone-end

::: zone target="chromeless"

Une fois la copie des données terminée, vous pouvez préparer et expédier l’appareil. Quand l’appareil arrive au centre de données Azure, les données sont automatiquement chargées vers Azure.

## <a name="prepare-to-ship"></a>Préparer l’expédition

Avant de préparer l’expédition, assurez-vous que les travaux de copie sont terminés.

1. Accédez à la page **Préparation de l'expédition** dans l'interface utilisateur web locale et commencez la préparation de l'expédition. 
2. Mettez l’appareil hors tension à partir de l’interface utilisateur web locale. Débranchez les câbles de l’appareil. 

Les étapes suivantes sont déterminées par l’endroit où vous retournez l’appareil.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Renvoyer la Data Box

Vérifiez que la copie des données sur l’appareil a été effectuée et que la **préparation de l’expédition** a réussi. En fonction de la région où vous expédiez l’appareil, la procédure est différente.

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[États-Unis, Canada, Europe](#tab/in-us-canada-europe)

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Effacer les données de la Data Box
 
Une fois que le chargement des données vers Azure est terminé, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australiatabin-australia"></a>[En Australie](#tab/in-australia)

Les centres de données Azure de l’Australie ont une notification de sécurité supplémentaire. Toutes les expéditions entrantes doivent avoir une notification préalable. Effectuez les étapes suivantes pour expédier en Australie.


1. Conservez la boîte d’origine utilisée pour expédier l’appareil dans le cadre d’un retour.
2. Vérifiez que la copie des données sur l’appareil a été effectuée et que la **préparation de l’expédition** a réussi.
3. Mettez l’appareil hors tension et débranchez les câbles.
4. Enroulez le cordon d’alimentation fourni avec l’appareil, et fixez-le à l’arrière de ce dernier.
5. Réservez un enlèvement en ligne sur [DHL Link](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Effacer les données de la Data Box
 
Une fois que le chargement des données vers Azure est terminé, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-japantabin-japan"></a>[Au Japon](#tab/in-japan) 

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Effacer les données de la Data Box
 
Une fois que le chargement des données vers Azure est terminé, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



