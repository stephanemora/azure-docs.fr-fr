---
title: Intégrer et gérer des capteurs dans le portail Defender pour IoT
description: Découvrez comment intégrer, visualiser et gérer les capteurs dans le portail Defender pour IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a763d8b65049cd9f301379c2c038a1d799114653
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835545"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Intégrer et gérer des capteurs dans le portail Defender pour IoT

Cet article explique comment intégrer, visualiser et gérer les capteurs dans le portail Defender pour IoT.

## <a name="onboard-sensors"></a>Intégrer des capteurs

Vous intégrez un capteur en l’inscrivant auprès d’Azure Defender pour IoT et en téléchargeant un fichier d’activation de capteur.

### <a name="register-the-sensor"></a>Inscrire le capteur

Pour vous inscrire :

1. Accédez à la page **Accueil** dans le portail Defender pour IoT.
1. Sélectionnez **Intégrer un capteur**.
1. Créez un nom de capteur. Nous vous recommandons d’inclure dans le nom l’adresse IP du capteur que vous avez installé ou d’utiliser un nom facilement identifiable. Cela facilite le suivi et la cohérence du nommage entre le nom d’inscription dans le portail Azure Defender pour IoT et l’adresse IP du capteur déployé qui est affichée dans la console du capteur.
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

Sur le portail Defender pour IoT, vous pouvez visualiser des informations de base sur les capteurs intégrés. 

1. Sélectionnez **Sites et capteurs**.
1. Sur la page **Sites et capteurs**, utilisez les outils de filtre et de recherche pour trouver les informations de capteur dont vous avez besoin.

Les informations disponibles incluent :

- Le nombre de capteurs intégrés
- Le nombre de capteurs connectés au cloud et gérés localement
- Le hub associé à un capteur intégré
- Des détails supplémentaires sur un capteur, tels que le nom affecté au capteur pendant l’intégration, la zone associée au capteur ou d’autres informations descriptives ajoutées avec des étiquettes

## <a name="manage-onboarded-sensors"></a>Gérer les capteurs intégrés

Vous utilisez le portail Defender pour IoT pour les tâches de gestion relatives aux capteurs.

### <a name="export"></a>Exporter

Pour exporter des informations relatives aux capteurs intégrés, sélectionnez l’icône **Exporter** en haut de la page **Sites et capteurs**.

### <a name="edit"></a>Modifier

Utilisez les outils de modification de **Sites et capteurs** pour ajouter et modifier le nom du site, la zone et les étiquettes.

### <a name="delete"></a>DELETE

Si vous supprimez un capteur connecté au cloud, les informations ne seront pas envoyées au hub IoT. Supprimez les capteurs connectés localement quand vous ne les utilisez plus.

Pour supprimer un capteur :

1. Sélectionnez le bouton de sélection ( **…** ) du capteur que vous souhaitez supprimer. 
1. Confirmez la suppression.

### <a name="reactivate"></a>Réactiver

Vous souhaiterez peut-être mettre à jour le mode d’administration de votre capteur. Par exemple :

- **Travailler en mode connecté au cloud plutôt qu’en mode géré localement** : Pour ce faire, mettez à jour le fichier d’activation de votre capteur connecté localement avec un fichier d’activation pour un capteur connecté au cloud. Après la réactivation, les détections du capteur sont affichées à la fois dans le capteur et dans le portail Defender pour IoT. Une fois le fichier de réactivation chargé, les informations d’alerte nouvellement détectées sont envoyées à Azure.

- **Travailler en mode connecté localement plutôt qu’en mode connecté au cloud** : Pour ce faire, mettez à jour le fichier d’activation d’un capteur connecté au cloud avec un fichier d’activation pour un capteur géré localement. Après la réactivation, les informations de détection de capteur sont affichées uniquement dans le capteur.

- **Associer le capteur à un nouveau hub IoT** :  Pour ce faire, réinscrivez le capteur, puis téléchargez un nouveau fichier d’activation.

Pour réactiver un capteur :

1. Accédez à la page **Sites et capteurs** sur le portail Defender pour IoT.

2. Sélectionnez le capteur pour lequel vous souhaitez télécharger un nouveau fichier d’activation.

3. Supprimez le capteur.

4. Intégrez à nouveau le capteur à partir de la page **Intégration** dans le nouveau mode ou avec un nouveau hub IoT.

5. Téléchargez le fichier d’activation à partir de la page **Télécharger le fichier d’activation**.

6. Connectez-vous à la console de capteur Defender pour IoT.

7. Dans la console du capteur, sélectionnez **Paramètres système** puis **Réactivation**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Chargez votre fichier d’activation pour réactiver le capteur.":::

8. Sélectionnez **Télécharger** et sélectionnez le fichier que vous avez enregistré.

9. Sélectionnez **Activer**. 

## <a name="see-also"></a>Voir aussi

[Activer et configurer votre capteur](how-to-activate-and-set-up-your-sensor.md)
