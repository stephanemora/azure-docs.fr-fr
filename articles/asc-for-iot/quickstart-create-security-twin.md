---
title: 'Démarrage rapide : Créer un jumeau de module de sécurité Azure Security Center pour IoT'
description: Dans le cadre de ce démarrage rapide, découvrez comment créer un jumeau de module Azure Security Center pour IoT en vue d’une utilisation avec Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: b362130c2b717f813a6332f81a3c8179bea4166a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73904162"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Démarrage rapide : Créer un jumeau de module azureiotsecurity

Ce guide de démarrage rapide explique comment créer des jumeaux de module _azureiotsecurity_ individuels destinés à de nouveaux appareils, ou créer par lot des jumeaux de module pour tous les appareils d’un hub IoT.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Présentation des jumeaux de module azureiotsecurity 

Pour les solutions IoT intégrées à Azure, les jumeaux d’appareil jouent un rôle essentiel dans la gestion d’appareils et l’automatisation des processus. 

Azure Security Center pour IoT offre une intégration complète avec votre plateforme de gestion d’appareils IoT, ce qui vous permet de gérer l’état de sécurité de vos appareils et de tirer parti des fonctionnalités de contrôle d’appareils existants.
L’intégration d’Azure Security Center pour IoT est obtenue par l’utilisation du mécanisme de jumelage IoT Hub.  

Consultez [Jumeaux de module IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) pour en savoir plus sur le concept général des jumeaux de module dans Azure IoT Hub. 
 
Azure Security Center pour IoT utilise le mécanisme de jumelage de module et gère un jumeau de module de sécurité nommé _azureiotsecurity_ pour chacun de vos appareils.

Le jumeau de module de sécurité contient toutes les informations relatives à la sécurité de l’appareil pour chacun de vos appareils. 
 
Afin de tirer pleinement parti des caractéristiques Azure Security Center pour IoT, vous devez créer, configurer et utiliser ces jumeaux de module de sécurité pour chaque appareil dans le service.  

## <a name="create-azureiotsecurity-module-twin"></a>Créer un jumeau de module azureiotsecurity 

Vous pouvez créer des jumeaux de module _azureiotsecurity_ de deux manières :
1. [Script de commandes par lot pour la création de module](https://aka.ms/iot-security-github-create-module) : crée automatiquement le jumeau de module pour les nouveaux appareils ou pour les appareils sans jumeau de module en utilisant la configuration par défaut.
2. Modifiez manuellement chaque jumeau de module individuellement avec des configurations spécifiques pour chaque appareil.

>[!NOTE] 
> L’utilisation de la méthode de traitement par lot ne remplace pas les jumeaux de module azureiotsecurity existants. L’utilisation de la méthode de traitement par lot crée UNIQUEMENT des jumeaux de module pour les appareils qui ne disposent pas encore d’un jumeau de module de sécurité. 

Consultez [Configuration des agents](how-to-agent-configuration.md) pour savoir comment modifier ou changer la configuration d’un jumeau de module existant. 

Pour créer manuellement un jumeau de module _azureiotsecurity_ destiné à un appareil, utilisez les instructions suivantes : 

1. Dans votre IoT Hub, recherchez et sélectionnez l’appareil pour lequel vous souhaitez créer un jumeau de module de sécurité.
1. Cliquez sur votre appareil, puis sur **Ajouter l’identité de module**.
1. Dans le champ **Nom de l’identité de module**, entrez **azureiotsecurity**.

1. Cliquez sur **Enregistrer**. 

## <a name="verify-creation-of-a-module-twin"></a>Vérifier la création d’un jumeau de module

Pour vérifier l’existence d’un jumeau de module de sécurité pour un appareil particulier :

1. Dans votre hub IoT, sélectionnez **Appareils IoT** dans le menu **Explorateurs**.    
1. Entrez l’ID de l’appareil, ou sélectionnez une option dans le **champ de recherche des appareils**, puis cliquez sur **Interroger les appareils**. 
    ![Interroger les appareils](./media/quickstart/verify-security-module-twin.png)
1. Sélectionnez l’appareil ou double-cliquez dessus pour ouvrir la page des détails de l’appareil. 
1. Sélectionnez le menu **Identités de module** et vérifiez l’existence du module **azureiotsecurity** dans la liste des identités de module associées à l’appareil. 
    ![Modules associés à un appareil](./media/quickstart/verify-security-module-twin-3.png)


Pour en savoir plus sur la personnalisation des propriétés des jumeaux de module Azure Security Center pour IoT, voir [Configuration de l’agent](how-to-agent-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment configurer les alertes personnalisées...

> [!div class="nextstepaction"]
> [Configurer les alertes personnalisées](quickstart-create-custom-alerts.md)
