---
title: 'Démarrage rapide : Créer des alertes personnalisées destinées à Azure Security Center pour IoT'
description: Étudiez, créez et affectez des alertes d’appareil personnalisées destinées au service de sécurité Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 063e5c9e7d75fd1c07d148c265b1fe64eee3cbc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303526"
---
# <a name="quickstart-create-custom-alerts"></a>Démarrage rapide : Créer des alertes personnalisées


Les alertes et groupes de sécurité personnalisés vous permettent de tirer pleinement parti des informations de sécurité de bout en bout et des connaissances catégorisées sur les appareils pour assurer une plus grande sécurité dans votre solution IoT. 

## <a name="why-use-custom-alerts"></a>Pourquoi utiliser des alertes personnalisées ? 

Vous connaissez vos appareils IoT mieux que personne.

Pour les clients qui connaissent parfaitement le comportement attendu de leurs appareils, Azure Security Center pour IoT permet de traduire cette connaissance en stratégie de comportement des appareils et de générer des alertes sur les écarts enregistrés par rapport au comportement normal attendu.

## <a name="security-groups"></a>Groupes de sécurité

Les groupes de sécurité vous permettent de définir des groupes logiques d’appareils et de gérer leur état de sécurité de façon centralisée.

Ces groupes peuvent représenter des appareils équipés d’un matériel particulier, des appareils déployés dans un emplacement donné, en fait tout groupe répondant à des besoins spécifiques.

Les groupes de sécurité sont définis par une propriété d’étiquette de jumeau d'appareil nommée **SecurityGroup**. Par défaut, chaque solution IoT sur IoT Hub dispose d’un groupe de sécurité nommé **par défaut**. Modifiez la valeur de la propriété **SecurityGroup** pour modifier le groupe de sécurité d’un appareil.
 
Par exemple :

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Utilisez des groupes de sécurité pour regrouper vos appareils par catégories logiques. Après avoir créé les groupes, affectez-les aux alertes personnalisées de votre choix, pour utiliser la solution de sécurité IoT de bout en bout la plus efficace. 

## <a name="customize-an-alert"></a>Personnaliser une alerte

1. Ouvrez votre hub IoT. 
2. Cliquez sur **Alertes personnalisées** dans la section **Sécurité**. 
3. Choisissez un groupe de sécurité auquel vous souhaitez appliquer la personnalisation. 
4. Cliquez sur **Ajouter une alerte personnalisée**.
5. Sélectionnez une alerte personnalisée dans la liste déroulante. 
6. Modifiez les propriétés nécessaires, puis cliquez sur **OK**.
7. N’oubliez pas de cliquer sur **ENREGISTRER**. Si la nouvelle alerte n’est pas enregistrée, elle est supprimée la prochaine fois que vous fermez IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Alertes disponibles pour la personnalisation

Azure Security Center pour IoT propose un grand nombre d’alertes qui peuvent être personnalisées en fonction de vos besoins spécifiques. Consultez le [tableau d’alertes personnalisables](concept-customizable-security-alerts.md) pour connaître la gravité des alertes, la source de données et la description ainsi que les étapes de correction que nous suggérons, si et quand chaque alerte est reçue. 


## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment déployer un agent de sécurité...

> [!div class="nextstepaction"]
> [Déployer un agent de sécurité](how-to-deploy-agent.md)
