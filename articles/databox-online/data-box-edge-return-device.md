---
title: Retourner votre appareil Azure Data Box Edge | Microsoft Docs
description: Décrit comment retourner l’appareil Azure Data Box Edge et supprimer la commande de l’appareil.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468603"
---
# <a name="return-your-azure-data-box-edge-device"></a>Retourner votre appareil Azure Data Box Edge

Cet article décrit comment effacer les données, puis retourner votre appareil Azure Data Box Edge. Une fois que vous avez retourné l’appareil, vous pouvez également supprimer la ressource associée à l’appareil.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Effacer les données des disques de données sur l’appareil
> * Ouvrir un ticket de support pour retourner votre appareil
> * Emballer l’appareil et planifier un enlèvement
> * Supprimer la ressource dans le Portail Azure

## <a name="erase-data-from-the-device"></a>Effacer les données de l’appareil

Pour effacer les données des disques de données de votre appareil, vous devez réinitialiser votre appareil. Vous pouvez réinitialiser votre appareil à l’aide de l’interface utilisateur web locale ou de l’interface PowerShell.

Avant de réinitialiser, créez une copie des données locales sur l’appareil si nécessaire. Vous pouvez copier les données de l’appareil dans un conteneur de Stockage Azure.

Pour réinitialiser votre appareil à l’aide de l’interface utilisateur web locale, effectuez les étapes suivantes.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Réinitialisation de l’appareil**.
2. Sélectionnez **Réinitialiser l’appareil**.

    ![Réinitialisation de l’appareil](media/data-box-edge-return-device/device-reset-1.png)

3. Lorsque vous êtes invité à confirmer l’opération, passez en revue l’avertissement et sélectionnez **Oui** pour continuer.

    ![Confirmer la réinitialisation](media/data-box-edge-return-device/device-reset-2.png)  

La réinitialisation efface les données des disques de données d’appareil. En fonction de la quantité de données sur votre appareil, ce processus prend environ 30 à 40 minutes.

Vous pouvez également vous connecter à l’interface PowerShell de l’appareil et utiliser l’applet de commande `Reset-HcsAppliance` pour effacer les données des disques de données. Pour plus d’informations, consultez [Réinitialiser votre appareil](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Si vous échangez pour ou effectuez une mise à niveau vers un nouvel appareil, nous vous recommandons de ne réinitialiser votre appareil que lorsque vous avez reçu le nouvel appareil.
> - La réinitialisation de l’appareil supprime uniquement les données locales de l’appareil. Les données qui se trouvent dans le cloud ne sont pas supprimées et entraîne des [frais](https://azure.microsoft.com/pricing/details/storage/). Ces données doivent être supprimées séparément à l’aide d’un outil de gestion de stockage cloud tel que [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Ouverture d’un ticket de support

Pour commencer le processus de retour, effectuez les étapes suivantes.

1. Ouvrez un ticket de support auprès du Support Microsoft indiquant que vous souhaitez retourner l’appareil. Sélectionnez le type de problème **Data Box Edge Hardware** (Matériel Azure Data Box Edge).

    ![Ouverture d’un ticket de support](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Un ingénieur du Support Microsoft vous contactera. Fournissez les informations d’expédition.
3. Si vous avez besoin d’une boîte d’expédition de retour, vous pouvez en demander une. Répondez **Oui** à la question **Need an empty box to return** (Besoin d’une boîte vide pour le retour).


## <a name="schedule-a-pickup"></a>Planification d’un enlèvement

1. Arrêtez l’appareil. Sur l’interface utilisateur web locale, accédez à **Maintenance > Paramètres d'alimentation**.
2. Sélectionnez **Arrêter**. Lorsque la boîte de dialogue de confirmation apparaît, cliquez sur **Oui** pour continuer. Pour plus d’informations, consultez [Gérer l’alimentation](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Débranchez les câbles d’alimentation et retirez tous les câbles réseau de l’appareil.
4. Préparez l’emballage d’expédition en utilisant votre propre boîte ou la boîte vide que vous avez reçue d’Azure. Placez l’appareil et les câbles d’alimentation fournis avec l’appareil dans la boîte.
5. Apposez l’étiquette d’expédition que vous avez reçue d’Azure sur l’emballage.
6. Planifiez un enlèvement avec votre transporteur régional. Si vous retournez l’appareil aux États-Unis, votre transporteur est UPS. Pour planifier l’enlèvement :

    1. Appelez l’UPS local (numéro d’appel gratuit propre à chaque pays).
    2. Lors de votre appel, indiquez le numéro de suivi de retour mentionné sur votre étiquette imprimée.
    3. Si le numéro de suivi n’est pas indiqué, UPS vous facturera des frais supplémentaires à l’enlèvement.

    Au lieu de planifier l’enlèvement, vous pouvez également déposer le disque Data Box Edge à l’emplacement de dépôt le plus proche.

## <a name="delete-the-resource"></a>Supprimer la ressource

Lorsque l’appareil est reçu par le centre de données Azure, il est inspecté pour rechercher des dommages ou des signes de falsification.

- Si l’appareil arrive intact et en bon état, le compteur de facturation s’arrête pour cette ressource. Le Support Microsoft vous contactera pour confirmer que l’appareil a été retourné. Vous pouvez alors supprimer la ressource associée à l’appareil dans le Portail Azure.
- Si l’appareil arrive sérieusement endommagé, des amendes peuvent s’appliquer. Pour plus de détails, consultez le [FAQ relatif aux appareils perdus ou endommagés](https://azure.microsoft.com/pricing/details/databox/edge/) et les [conditions d’utilisation du produit](https://www.microsoft.com/licensing/product-licensing/products).  


Vous pouvez supprimer l’appareil dans le Portail Azure :
-   Après avoir passé la commande et avant la préparation de l’appareil par Microsoft.
-   Lorsque vous avez retourné l’appareil à Microsoft, il est soumis à une inspection physique dans le centre de données Azure, et le Support Microsoft vous appelle pour confirmer que l’appareil a été retourné.

Si vous avez activé l’appareil sur un autre abonnement ou à un autre emplacement, Microsoft déplacera votre commande vers le nouvel abonnement ou emplacement dans un délai d’un jour ouvrable. Lorsque la commande est déplacée, vous pouvez supprimer cette ressource.


Effectuez les étapes suivantes pour supprimer l’appareil et la ressource dans le Portail Azure.

1. Dans le Portail Azure, accédez à votre ressource, puis à **Vue d’ensemble**. Dans la barre de commandes, sélectionnez **Supprimer**.

    ![Sélectionner Supprimer](media/data-box-edge-return-device/delete-resource-1.png)

2. Dans le panneau **Supprimer l’appareil**, saisissez le nom de l’appareil que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

    ![Confirmation de suppression](media/data-box-edge-return-device/delete-resource-2.png)

Vous êtes informé lorsque l’appareil et la ressource associée ont bien été supprimés.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer la bande passante](data-box-edge-manage-bandwidth-schedules.md).
