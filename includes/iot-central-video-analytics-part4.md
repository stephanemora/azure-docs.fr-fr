---
title: Fichier include
description: Fichier include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832065"
---
### <a name="publish-the-device-template"></a>Publier le modèle d’appareil

Avant d’ajouter un appareil à l’application, vous devez publier le modèle d’appareil :

1. Dans le modèle d’appareil **LVA Edge Gateway v2**, sélectionnez **Publier**.

1. Sur la page **Publier ce modèle d’appareil dans l’application**, sélectionnez **Publier**.

**LVA Edge Gateway v2** figure désormais dans la page **Appareils** de l’application, en tant que type d’appareil disponible.

## <a name="migrate-the-gateway-device"></a>Effectuer la migration de l’appareil de passerelle

L’appareil **gateway-001** existant utilise le modèle d’appareil **LVA Edge Gateway**. Pour utiliser votre nouveau manifeste de déploiement, effectuez la migration de l’appareil vers le nouveau modèle d’appareil :

Pour effectuer la migration de l’appareil **gateway-001** :

1. Accédez à la page **Appareils**, puis sélectionnez le modèle d’appareil **gateway-001** pour le mettre en surbrillance dans la liste.

1. Sélectionnez **Migrer**. Si l’icône de **migration** n’est pas visible, sélectionnez **...** pour afficher plus d’options.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Effectuer la migration de l’appareil de passerelle vers une nouvelle version":::

1. Dans la liste de la boîte de dialogue **Migration**, sélectionnez **LVA Edge Gateway v2**, puis **Effectuer la migration**.

La migration se termine au bout de quelques secondes. Votre appareil utilise maintenant le modèle d’appareil **LVA Edge Gateway v2** avec votre manifeste de déploiement personnalisé.

Désormais, aucun appareil n’utilise encore le modèle d’appareil d’origine **LVA Edge Gateway**. Supprimez ce modèle d'appareil :

1. Accédez à la page **Modèles d’appareil** et sélectionnez le modèle d’appareil **LVA Edge Gateway**.

1. Sélectionnez **Supprimer** pour supprimer le modèle d’appareil.

### <a name="get-the-device-credentials"></a>Obtenir les informations d’identification de l’appareil

Vous avez besoin des informations d’identification qui permettent à l’appareil de se connecter à votre application IoT Central. Pour obtenir les informations d’identification de l’appareil :

1. Dans la page **Appareils**, sélectionnez l’appareil **gateway-001**.

1. Sélectionnez **Connecter**.

1. Sur la page **Connexion de l’appareil**, notez les valeurs *Étendue de l’ID*, **ID de l’appareil** et **Clé primaire** dans le fichier **scratchpad.txt**. Vous utiliserez ces valeurs plus tard.

1. Assurez-vous que la méthode de connexion est définie sur **Signature d'accès partagé**.

1. Sélectionnez **Fermer**.

