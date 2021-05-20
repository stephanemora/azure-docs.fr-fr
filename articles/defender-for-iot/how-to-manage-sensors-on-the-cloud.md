---
title: Gérer des capteurs dans le portail Defender pour IoT
description: Découvrez comment intégrer, visualiser et gérer les capteurs dans le portail Defender pour IoT.
ms.date: 04/29/2021
ms.topic: how-to
ms.openlocfilehash: 5db2b440a70b2e172a1167f6b8759b78d0d7878e
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109654901"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Gérer des capteurs dans le portail Defender pour IoT

Cet article explique comment intégrer, visualiser et gérer les capteurs dans le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Intégrer des capteurs

Vous intégrez un capteur en l’inscrivant auprès d’Azure Defender pour IoT et en téléchargeant un fichier d’activation de capteur.

### <a name="register-the-sensor"></a>Inscrire le capteur

**Pour vous inscrire :**

1. Accédez à la page **Accueil** dans le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

1. Sélectionnez **Intégrer un capteur**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/onboard-a-sensor.png" alt-text="Sélectionnez Capteur intégré pour démarrer le processus d’intégration de votre capteur.":::

1. Créez un nom de capteur. 

    Nous vous recommandons d’inclure dans le nom l’adresse IP du capteur que vous avez installé ou d’utiliser un nom facilement identifiable. Cela facilite le suivi et la cohérence du nommage entre le nom d’inscription dans le [portail Azure Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) et l’adresse IP du capteur déployé qui est affichée dans la console du capteur.

1. Associez le capteur à un abonnement Azure.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/name-subscription.png" alt-text="Entrez un nom explicite et connectez votre capteur à un abonnement.":::

1. Choisissez un mode de connexion du capteur à l’aide du bouton bascule **Connecté au cloud**. Si le bouton bascule est activé, le capteur est connecté au cloud. Si le bouton bascule est désactivé, le capteur est géré localement.

   - **Capteurs connectés au cloud** : Les informations détectées par le capteur s’affichent dans la console du capteur. Les informations sur les alertes sont transmises par le biais d’un hub IoT et peuvent être partagées avec d’autres services Azure, comme Azure Sentinel. En outre, les packages de renseignement sur les menaces peuvent être envoyés (push) aux capteurs à partir du portail Azure Defender pour IoT. À l’inverse, lorsque le capteur n’est pas connecté au cloud, vous devez télécharger les packages de renseignement sur les menaces, puis les charger sur les capteurs de votre entreprise. Pour autoriser Defender pour IoT à envoyer (push) des packages aux capteurs, activez le bouton bascule **Mises à jour automatiques du renseignement sur les menaces**. Pour plus d’informations, consultez [Recherche et packages de renseignement sur les menaces](how-to-work-with-threat-intelligence-packages.md).
   
   Choisissez un hub IoT qui servira de passerelle entre ce capteur et le portail Azure Defender pour IoT. Définissez un nom et une zone de site. Vous pouvez également ajouter des étiquettes descriptives. Le nom du site, la zone et les étiquettes sont des entrées descriptives de la page [Sites et capteurs](#view-onboarded-sensors).

   - **Capteurs gérés localement** : Les informations détectées par les capteurs s’affichent dans leur console. Si vous travaillez dans un réseau hermétique et que vous souhaitez une vue unifiée de toutes les informations détectées par plusieurs capteurs gérés localement, utilisez la console de gestion locale.

   Pour les capteurs connectés au cloud, le nom défini lors de l’intégration est le nom qui apparaît dans la console de capteur. Vous ne pouvez pas modifier ce nom directement à partir de la console. Pour les capteurs gérés localement, le nom appliqué pendant l’intégration est stocké dans Azure, mais peut être mis à jour dans la console du capteur.

### <a name="download-the-sensor-activation-file"></a>Télécharger le fichier d’activation du capteur

Le fichier d’activation de capteur contient des instructions relatives au mode d’administration du capteur. Vous téléchargez un fichier d’activation unique pour chaque capteur que vous déployez. Un utilisateur qui se connecte à la console du capteur pour la première fois charge le fichier d’activation sur le capteur.

**Pour télécharger un fichier d’activation :**

1. Sur la page **Intégrer un capteur**, sélectionnez **Télécharger le fichier d’activation**.

1. Rendez le fichier accessible à l’utilisateur qui se connecte à la console du capteur pour la première fois.

## <a name="view-onboarded-sensors"></a>Afficher les capteurs intégrés

Sur le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started), vous pouvez visualiser des informations opérationnelles importantes sur les capteurs intégrés.

1. Sélectionnez **Sites et capteurs**. La page affiche le nombre de capteurs intégrés, le nombre de capteurs connectés au cloud et gérés localement, ainsi que les éléments suivants :

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/sites-and-sensors.png" alt-text="Sélectionnez la page Sites et capteurs pour afficher tous les capteurs associés.":::

    - Le nom du capteur attribué lors de l’intégration.
    - Le type de connexion (connecté au cloud ou géré localement).
    - La zone associée au capteur.
    - La version du capteur installée.
    - L’état de connexion du capteur au cloud.
    - la dernière fois que le capteur a été détecté en train de se connecter au cloud.

## <a name="manage-onboarded-sensors"></a>Gérer les capteurs intégrés

Utilisez le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) pour les tâches de gestion relatives aux capteurs.

Les capteurs intégrés peuvent être affichés sur la page **Sites et capteurs**. Vous pouvez également modifier les informations d’un capteur depuis cette page.

### <a name="export-sensor-details"></a>Exporter les détails d’un capteur

Pour exporter des informations relatives aux capteurs intégrés, sélectionnez l’icône **Exporter** en haut de la page **Sites et capteurs**.

:::image type="content" source="media/how-to-manage-sensors-on-the-cloud/export-sensors.png" alt-text="Sélectionnez le bouton Exporter pour exporter les informations de capteur.":::

### <a name="edit-sensor-zone-details"></a>Modifier les détails de la zone d’un capteur

Utilisez les options d’édition **Sites et capteurs** pour modifier le nom et la zone d’un capteur.

**Pour apporter des modifications :**

1. Sélectionnez les **points de suspension** ( **…** ) pour le capteur que vous souhaitez modifier.

1. Sélectionnez **Edit** (Modifier).

1. Mettez à jour la zone du capteur, ou créez une zone.

### <a name="delete-a-sensor"></a>Supprimer un capteur

Si vous supprimez un capteur connecté au cloud, les informations ne seront pas envoyées au hub IoT. Supprimez les capteurs connectés localement quand vous ne les utilisez plus.

**Pour supprimer un capteur :**

1. Sélectionnez le bouton de sélection ( **…** ) du capteur que vous souhaitez supprimer.

1. Sélectionnez **Supprimer un capteur**.

### <a name="reactivate-a-sensor"></a>Réactiver un capteur 

Vous devrez peut-être réactiver votre capteur car vous souhaitez :

- **Travailler en mode connecté au cloud plutôt qu’en mode géré localement** : Après la réactivation, les détections apparaissent dans le capteur, et les informations d’alerte récemment détectées sont transmises via l’IoT Hub. Ces informations peuvent être partagées avec d’autres services Azure, par exemple Azure Sentinel.

- **Travailler en mode généré localement plutôt qu’en mode connecté** : Après la réactivation, les informations de détection de capteur sont affichées uniquement dans le capteur.

- **Associer le capteur à un nouveau hub IoT** :  Pour ce faire, réinscrivez le capteur avec un nouveau hub, puis téléchargez un nouveau fichier d’activation.

**Pour réactiver un capteur :**

1. Accédez à la page **Sites et capteurs** sur le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

1. Sélectionnez le capteur pour lequel vous souhaitez télécharger un nouveau fichier d’activation.

1. Sélectionnez les **points de suspension** ( **...** ), puis sélectionnez **Supprimer le capteur**.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/delete-a-sensor.png" alt-text="Sélectionnez les points de suspension, puis Supprimer le capteur.":::

1. [Intégrez à nouveau le capteur](#onboard-sensors) dans le nouveau mode ou avec un nouveau IoT Hub en sélectionnant **Intégrer un capteur** à partir de la page Prise en main.

1. Télécharger le fichier d’activation.

1. Connectez-vous à la console de capteur Defender pour IoT.

1. Dans la console du capteur, sélectionnez **Paramètres système** puis **Réactivation**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Chargez votre fichier d’activation pour réactiver le capteur.":::

1. Sélectionnez **Télécharger** et sélectionnez le fichier que vous avez enregistré à partir de la page Intégrer un capteur.

1. Sélectionnez **Activer**.

## <a name="next-steps"></a>Étapes suivantes

[Activer et configurer votre capteur](how-to-activate-and-set-up-your-sensor.md)
