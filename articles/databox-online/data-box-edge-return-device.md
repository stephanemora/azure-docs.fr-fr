---
title: Retourner votre appareil Edge de zone de données Azure | Microsoft Docs
description: Décrit comment retourner le périphérique périmétrique de zone de données Azure et de supprimer la commande de l’appareil.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468603"
---
# <a name="return-your-azure-data-box-edge-device"></a>Retourner votre appareil Edge de zone de données Azure

Cet article décrit comment réinitialiser les données, puis retourner votre appareil Edge de zone de données Azure. Une fois que vous avez renvoyé l’appareil, vous pouvez également supprimer la ressource associée à l’appareil.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Réinitialiser les données hors tension les disques de données sur l’appareil
> * Ouvrez un ticket de Support pour retourner votre appareil
> * Pack de configuration de l’appareil et planifier l’enlèvement
> * Supprimer la ressource dans le portail Azure

## <a name="erase-data-from-the-device"></a>Effacer les données de l’appareil

Pour effacer les données des disques de données de votre appareil, vous devez réinitialiser votre appareil. Vous pouvez réinitialiser votre appareil à l’aide de l’interface utilisateur web locale ou l’interface de PowerShell.

Avant de réinitialiser, créer une copie des données locales sur l’appareil si nécessaire. Vous pouvez copier les données à partir de l’appareil à un conteneur de stockage Azure.

Pour réinitialiser votre appareil à l’aide de l’interface utilisateur web locale, procédez comme suit.

1. Dans l’interface utilisateur web locale, accédez à **Maintenance > périphérique réinitialisé**.
2. Sélectionnez **réinitialisation du périphérique**.

    ![Réinitialisation de l’appareil](media/data-box-edge-return-device/device-reset-1.png)

3. Lorsque vous êtes invité à confirmer l’opération, passez en revue l’avertissement et sélectionnez **Oui** pour continuer.

    ![Confirmez la réinitialisation](media/data-box-edge-return-device/device-reset-2.png)  

La réinitialisation efface les données hors tension les disques de données de périphérique. Selon la quantité de données sur votre appareil, ce processus prend environ 30 à 40 minutes.

Vous pouvez également vous connecter à l’interface PowerShell de l’appareil et l’utilisation du `Reset-HcsAppliance` applet de commande pour effacer les données à partir des disques de données. Pour plus d’informations, consultez [réinitialiser votre appareil](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Si vous êtes échanger ou la mise à niveau vers un nouvel appareil, nous vous recommandons de réinitialiser votre appareil uniquement une fois que vous avez reçu le nouvel appareil.
> - Le périphérique uniquement de réinitialisation supprime toutes les données locales, l’appareil hors tension. Les données qui se trouvent dans le cloud n’est pas supprimées et collecte [frais](https://azure.microsoft.com/pricing/details/storage/). Ces données doivent être supprimée séparément à l’aide d’un outil de gestion de stockage cloud tel que [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Ouvrez un ticket de Support

Pour commencer le processus de retour, effectuez les étapes suivantes.

1. Ouvrir un ticket de Support auprès du Support de Microsoft qui indique que vous souhaitez retourner l’appareil. Sélectionnez le type de problème **données boîte Edge matériel**.

    ![Ouvrir le ticket de Support](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Un ingénieur du Support Microsoft vous contactera. Fournir les informations d’expédition.
3. Si vous avez besoin d’une zone expédition de retournée, vous pouvez le demander. Réponse **Oui** à la question **besoin d’une zone vide pour retourner**.


## <a name="schedule-a-pickup"></a>Planifier l’enlèvement

1. Arrêtez l’appareil. Sur l’interface utilisateur web locale, accédez à **Maintenance > Paramètres d'alimentation**.
2. Sélectionnez **arrêter**. Lorsque vous êtes invité à confirmer l’opération, cliquez sur **Oui** pour continuer. Pour plus d’informations, consultez [gestion de la puissance](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Débranchez les câbles d’alimentation et de supprimer tous les câbles réseau de l’appareil.
4. Préparer le package d’expédition à l’aide de votre propre zone ou la zone vide que vous avez reçu à partir d’Azure. Placez l’appareil et les câbles d’alimentation fournis avec l’appareil dans la zone.
5. Apposer l’étiquette d’expédition que vous avez reçu à partir d’Azure sur le package.
6. Planifier l’enlèvement avec votre opérateur régional. Si le retour de l’appareil dans des États-Unis, votre opérateur est UPS. Pour planifier l’enlèvement :

    1. Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays).
    2. Dans l’appel, citer l’expédition inverse, comme indiqué sur votre étiquette de numéro de suivi.
    3. Si le numéro de suivi n’est pas entre guillemets, UPS vous devrez payer des frais supplémentaires au cours de collecte.

    Au lieu de planifier l’enlèvement, vous pouvez également supprimer au-delà du bord de la zone de données à l’emplacement de dépôt le plus proche.

## <a name="delete-the-resource"></a>Supprimer la ressource

Une fois l’appareil est reçue par le centre de données, l’appareil est inspecté de dommages ou des signes de falsification.

- Si l’appareil arrive intacte et en bon état, le compteur de facturation s’arrête pour cette ressource. Support Microsoft vous contactera pour confirmer que l’appareil a été retourné. Vous pouvez ensuite supprimer la ressource associée à l’appareil dans le portail Azure.
- Si l’appareil arrive sérieusement endommagé, amendes peuvent s’appliquer. Pour plus d’informations, consultez le [Forum aux questions sur l’appareil perdu ou endommagé](https://azure.microsoft.com/pricing/details/databox/edge/) et [produit les termes du contrat de Service](https://www.microsoft.com/licensing/product-licensing/products).  


Vous pouvez supprimer l’appareil dans le portail Azure :
-   Une fois que vous avez placé l’ordre et avant que l’appareil est préparé par Microsoft.
-   Une fois que vous avez renvoyé à l’appareil à Microsoft, il transmet l’inspection physique du centre de données Azure et les appels au Support de Microsoft pour confirmer que l’appareil a été retourné.

Si vous avez activé l’appareil par rapport à un autre abonnement ou emplacement, Microsoft déplacera votre commande vers le nouvel abonnement ou un emplacement au sein d’un jour ouvrable. Une fois que l’ordre est déplacé, vous pouvez supprimer cette ressource.


Effectuez les étapes suivantes pour supprimer l’appareil et la ressource dans le portail Azure.

1. Dans le portail Azure, accédez à votre ressource puis **vue d’ensemble**. Dans la barre de commandes, sélectionnez **supprimer**.

    ![Sélectionner Supprimer](media/data-box-edge-return-device/delete-resource-1.png)

2. Dans le **supprimer l’appareil** panneau, tapez le nom de l’appareil que vous souhaitez supprimer, puis sélectionnez **supprimer**.

    ![Confirmez la suppression](media/data-box-edge-return-device/delete-resource-2.png)

Vous êtes averti une fois le périphérique et la ressource associée est supprimée avec succès.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer la bande passante](data-box-edge-manage-bandwidth-schedules.md).
