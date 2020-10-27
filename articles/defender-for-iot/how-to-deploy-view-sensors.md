---
title: Afficher et gérer les capteurs intégrés dans Azure Defender pour IoT
description: Cet article explique comment afficher et supprimer des capteurs intégrés, ainsi que télécharger de nouveaux fichiers d’activation pour les capteurs intégrés dans Azure Defender pour IoT.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094119"
---
# <a name="view-and-manage-onboarded-sensors"></a>Afficher et gérer les capteurs intégrés

Cet article explique comment afficher et supprimer des capteurs intégrés, ainsi que télécharger de nouveaux fichiers d’activation pour les capteurs intégrés.

## <a name="update-sensor-management-mode"></a>Mettre à jour le mode de gestion des capteurs

Vous souhaiterez peut-être mettre à jour le mode de gestion de votre capteur. Dans ce cas, vous devez supprimer le capteur actuel de la page de gestion du capteur et charger un nouveau fichier d’activation.

- **Travailler en mode géré par le cloud plutôt qu’en mode géré localement** : Mettez à jour le fichier d’activation de votre capteur géré localement avec un fichier d’activation pour un capteur géré par le cloud. Après la réactivation, les détections de capteur sont affichées à la fois dans le capteur et dans le portail Azure Defender pour IoT. Une fois le fichier de réactivation téléchargé, les informations d’alerte nouvellement détectées sont envoyées à Azure.

- **Travailler en mode géré localement au lieu du mode géré par le cloud** : Mettez à jour le fichier d’activation d’un capteur géré par le cloud avec un fichier d’activation pour un capteur géré localement. Après la réactivation, les informations de détection de capteur sont affichées uniquement dans le capteur.

- **Associer le capteur à un nouveau IoT Hub** : Vous souhaiterez peut-être associer votre capteur à un nouveau IoT Hub. Pour ce faire, réinscrivez le capteur, puis téléchargez un nouveau fichier d’activation.

**Pour réactiver le capteur :**

1. Accédez à la page **Gestion des capteurs** d’Azure Defender pour IoT.

2. Sélectionnez le capteur pour lequel vous souhaitez télécharger un nouveau fichier d’activation.

3. Supprimez-la.

4. Intégrez à nouveau le capteur à partir de la page **Intégration** dans le nouveau mode ou avec un nouveau IoT Hub.

5. Téléchargez le fichier d’activation à partir de la page **Télécharger le fichier d’activation** .

6. Connectez-vous à la console Azure Defender pour IoT.

7. Dans la console du capteur, sélectionnez **Paramètres système** puis **Réactivation** .

   ![Capture d’écran de la vue Réactivation](media/updates/image14.png)

8. Sélectionnez **Télécharger** et sélectionnez le fichier que vous avez enregistré.

9. Sélectionnez **Activer** .
 
## <a name="delete-sensors"></a>Supprimer des capteurs

Si vous supprimez un capteur géré par le cloud, les informations ne sont pas envoyées au IoT Hub.

Supprimez les capteurs gérés localement quand vous ne les utilisez plus.

**Pour supprimer des capteurs :**

1. Accédez à la page **Gestion des capteurs** d’Azure Defender pour IoT.

2. Sélectionnez les capteurs à supprimer.

3. Sélectionnez **Supprimer un capteur** .

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les options de configuration, accédez au guide pratique de configuration du module.
> [!div class="nextstepaction"]
> [Guide pratique de configuration du module](./how-to-agent-configuration.md)
