---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/21/2021
ms.author: alkohli
ms.openlocfilehash: bd845eef3e2f41bc9f14303430e3e44ab68c1b68
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247217"
---
Effectuez les étapes suivantes si le retour de l’appareil s’effectue en Europe.

1. Vérifiez que l’appareil est hors tension et que les câbles sont débranchés.
2. Enroulez le cordon d’alimentation fourni avec la Data Box et fixez-le à l’arrière de l’appareil.
3. Assurez-vous que l’étiquette d’expédition est affichée sur l’écran E-ink et planifiez l’enlèvement avec votre transporteur. Si l’étiquette est endommagée ou perdue, ou si elle ne s’affiche pas sur l’écran E-ink, contactez le support Microsoft. Si le support technique le suggère, vous pouvez accéder à **Vue d’ensemble > Télécharger une étiquette d’expédition** dans le portail Azure. Téléchargez l’étiquette d’expédition et collez-la sur l’appareil.
1. **Si vous effectuez l’expédition en Allemagne ou en Suisse,** le centre de données Azure exige un avis préalable concernant tous les retours d’appareil :
    1. Envoyez un e-mail à Azure Data Box Operations, à l’aide du modèle suivant, pour recevoir un ID entrant. Envoyez un e-mail à [adbops@microsoft.com](mailto:adbops@microsoft.com).

       ```
       To: adbops@microsoft.com
       Subject: Request for Azure Data Box Inbound ID: <orderName> 
       Body: 
        
       I am ready to return an Azure Data Box and would like to request an Inbound ID for the following order:
       
       Order Name: <orderName>
       Return Tracking Number: <returnTracking#>
       ```

    1. Notez le numéro d’ID entrant fourni par Azure Data Box Operations, puis collez-le sur l’unité, de manière clairement visible, près de l’étiquette de retour.
1. Planifiez une date d’enlèvement avec UPS en cas de renvoi de l’appareil. Pour planifier l’enlèvement :

    * Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays/région).
    * Lors de l’appel, indiquez le numéro de suivi du retour, qui est indiqué dans l’écran E-ink ou sur l’étiquette de retour imprimée. Si vous n’indiquez pas le numéro de suivi, UPS vous facturera des frais supplémentaires à l’enlèvement.
    * Si des problèmes surviennent pendant la planification d’un enlèvement, ou si vous êtes invité à payer des frais supplémentaires, contactez le service des opérations Azure Data Box. Envoyez un e-mail à [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Au lieu de planifier l’enlèvement, vous pouvez déposer la Data Box dans le point de dépôt le plus proche.

    <!--FOLLOW-UP: Move this to after the Include in data-box-deploy-picked-up. This has been done already in data-box-export-deploy-picked-up. - **If you're shipping from Germany or Switzerland,** you can also [use self-managed shipping](data-box-deploy-picked-up.md#self-managed-shipping).-->

4. Une fois que la Data Box a été scannée et récupérée par le transporteur, l’état de la commande dans le portail affiche **Récupérée**. Un ID de suivi s’affiche également.

