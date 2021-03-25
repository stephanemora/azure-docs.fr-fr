---
title: Intégrer et gérer des capteurs et des abonnements dans le portail Defender pour IoT
description: Découvrez comment intégrer, visualiser et gérer les capteurs dans le portail Defender pour IoT.
ms.date: 2/18/2021
ms.topic: how-to
ms.openlocfilehash: 5c529a5679e8b3d4879b003fa3d168e911f9b518
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781821"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Intégrer et gérer des capteurs et des abonnements dans le portail Defender pour IoT

Cet article explique comment intégrer, visualiser et gérer les capteurs dans le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Intégrer des capteurs

Vous intégrez un capteur en l’inscrivant auprès d’Azure Defender pour IoT et en téléchargeant un fichier d’activation de capteur.

### <a name="register-the-sensor"></a>Inscrire le capteur

Pour vous inscrire :

1. Accédez à la page **Accueil** dans le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Sélectionnez **Intégrer un capteur**.
1. Créez un nom de capteur. Nous vous recommandons d’inclure dans le nom l’adresse IP du capteur que vous avez installé ou d’utiliser un nom facilement identifiable. Cela facilite le suivi et la cohérence du nommage entre le nom d’inscription dans le [portail Azure Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) et l’adresse IP du capteur déployé qui est affichée dans la console du capteur.
1. Associez le capteur à un abonnement Azure.
1. Choisissez un mode d’administration du capteur à l’aide du bouton bascule **Connecté au cloud**. Si le bouton bascule est activé, le capteur est connecté au cloud. Si le bouton bascule est désactivé, le capteur est géré localement.

   - **Capteurs connectés au cloud** : Les informations détectées par le capteur s’affichent dans la console du capteur. Les informations sur les alertes sont transmises par le biais d’un hub IoT et peuvent être partagées avec d’autres services Azure, comme Azure Sentinel.

   - **Capteurs gérés localement** : Les informations détectées par les capteurs s’affichent dans leur console. Si vous travaillez dans un réseau hermétique et que vous souhaitez une vue unifiée de toutes les informations détectées par plusieurs capteurs gérés localement, utilisez la console de gestion locale.

   Pour les capteurs connectés au cloud, le nom défini lors de l’intégration est le nom qui apparaît dans la console de capteur. Vous ne pouvez pas modifier ce nom directement à partir de la console. Pour les capteurs gérés localement, le nom appliqué pendant l’intégration est stocké dans Azure et peut être mis à jour dans la console du capteur.

1. Choisissez un hub IoT qui servira de passerelle entre ce capteur et Azure Defender pour IoT.
1. Si le capteur est connecté au cloud, associez-le à un hub IoT, puis définissez un nom de site et une zone. Vous pouvez également ajouter des étiquettes descriptives. Le nom du site, la zone et les étiquettes sont des entrées descriptives de la page [Sites et capteurs](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Télécharger le fichier d’activation du capteur

Le fichier d’activation de capteur contient des instructions relatives au mode d’administration du capteur. Vous téléchargez un fichier d’activation unique pour chaque capteur que vous déployez. Un utilisateur qui se connecte à la console du capteur pour la première fois charge le fichier d’activation sur le capteur.

Pour télécharger un fichier d’activation :

1. Sur la page **Intégrer un capteur**, sélectionnez **Télécharger le fichier d’activation**.
1. Rendez le fichier accessible à l’utilisateur qui se connecte à la console du capteur pour la première fois.

## <a name="view-onboarded-sensors"></a>Afficher les capteurs intégrés

Sur le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started), vous pouvez visualiser des informations de base sur les capteurs intégrés.

1. Sélectionnez **Sites et capteurs**.
1. Utilisez les outils de filtre et de recherche pour trouver des informations sur les capteurs et les menaces dont vous avez besoin.

- Le nombre de capteurs intégrés
- Le nombre de capteurs connectés au cloud et gérés localement
- Le hub associé à un capteur intégré
- Des détails supplémentaires sur un capteur, tels que le nom affecté au capteur pendant l’intégration, la zone associée au capteur ou d’autres informations descriptives ajoutées avec des étiquettes

## <a name="manage-onboarded-sensors"></a>Gérer les capteurs intégrés

Utilisez le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) pour les tâches de gestion relatives aux capteurs.

Les capteurs intégrés peuvent être affichés sur la page **Sites et capteurs**. Vous pouvez également modifier les informations d’un capteur depuis cette page.

### <a name="export-sensor-details"></a>Exporter les détails d’un capteur

Pour exporter des informations relatives aux capteurs intégrés, sélectionnez l’icône **Exporter** en haut de la page **Sites et capteurs**.

### <a name="edit-sensor-zone-details"></a>Modifier les détails de la zone d’un capteur

Utilisez les options d’édition **Sites et capteurs** pour modifier le nom et la zone d’un capteur.

Pour apporter des modifications :

1. Sélectionnez les **points de suspension** ( **…** ) pour le capteur que vous souhaitez modifier.
1. Sélectionnez **Edit** (Modifier).
1. Mettez à jour la zone du capteur, ou créez une zone.

### <a name="delete-a-sensor"></a>Supprimer un capteur

Si vous supprimez un capteur connecté au cloud, les informations ne seront pas envoyées au hub IoT. Supprimez les capteurs connectés localement quand vous ne les utilisez plus.

Pour supprimer un capteur :

1. Sélectionnez le bouton de sélection ( **…** ) du capteur que vous souhaitez supprimer.
1. Confirmez la suppression.

### <a name="reactivate-a-sensor"></a>Réactiver un capteur 

Vous devrez peut-être réactiver votre capteur car vous souhaitez :

- **Travailler en mode connecté au cloud plutôt qu’en mode géré localement** : Après la réactivation, les détections apparaissent dans le capteur, et les informations d’alerte récemment détectées sont transmises via l’IoT Hub. Ces informations peuvent être partagées avec d’autres services Azure, par exemple Azure Sentinel.

- **Travailler en mode généré localement plutôt qu’en mode connecté** : Après la réactivation, les informations de détection de capteur sont affichées uniquement dans le capteur.

- **Associer le capteur à un nouveau hub IoT** :  Pour ce faire, réinscrivez le capteur avec un nouveau hub, puis téléchargez un nouveau fichier d’activation.

Pour réactiver un capteur :

1. Accédez à la page **Sites et capteurs** sur le [portail Defender pour IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Sélectionnez le capteur pour lequel vous souhaitez télécharger un nouveau fichier d’activation.

3. Supprimez le capteur.

4. Intégrez à nouveau le capteur dans le nouveau mode ou avec un nouveau IoT Hub en sélectionnant **Intégrer un capteur** à partir de la page Prise en main.

5. Télécharger le fichier d’activation.

1. Connectez-vous à la console de capteur Defender pour IoT.

7. Dans la console du capteur, sélectionnez **Paramètres système** puis **Réactivation**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Chargez votre fichier d’activation pour réactiver le capteur.":::

8. Sélectionnez **Télécharger** et sélectionnez le fichier que vous avez enregistré à partir de la page Intégrer un capteur.

9. Sélectionnez **Activer**.

## <a name="offboard-a-subscription"></a>Retirer un abonnement

Les abonnements sont gérés mensuellement. Quand vous retirez un abonnement, vous êtes facturé pour celui-ci jusqu’à la fin du mois. 

Désinstallez tous les capteurs associés à l’abonnement avant de retirer l’abonnement. Pour plus d’informations sur la suppression d’un capteur, consultez [Supprimer un capteur](#delete-a-sensor). 

Pour retirer un abonnement :

1. Accédez à la page **Tarification**.
1. Sélectionnez l’abonnement, puis l’icône **Supprimer** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false":::.
1. Dans la fenêtre contextuelle de confirmation, activez la case à cocher pour confirmer que vous avez supprimé tous les capteurs associés à l’abonnement.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Activez la case à cocher et sélectionnez Annuler pour annuler votre capteur.":::

1. Sélectionnez le bouton **Retirer**. 

L’environnement local n’est pas affecté, mais vous devez en désinstaller le capteur, ou réaffecter le capteur à un autre abonnement afin d’éviter que des données associées soient transmises à la console de gestion locale. 

## <a name="see-also"></a>Voir aussi

[Activer et configurer votre capteur](how-to-activate-and-set-up-your-sensor.md)
