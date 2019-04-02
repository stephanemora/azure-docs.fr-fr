---
title: Créer un jumeau de module de sécurité ASC pour IoT (préversion) | Microsoft Docs
description: Découvrez comment créer un jumeau de module ASC pour IoT en vue d’une utilisation avec ASC pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 89802a638944ec220186e943d5fdc33524b2d4e9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541746"
---
# <a name="quickstart-create-an-asc-for-iot-module-twin"></a>Démarrage rapide : Créer un jumeau de module ASC pour IoT

> [!IMPORTANT]
> ASC pour IoT est actuellement disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les explications de ce guide de démarrage rapide détaillent la création de jumeaux de module ASC pour IoT individuels destinés aux nouveaux appareils, ainsi que la création par lot de jumeaux de module pour tous les appareils d’un hub IoT.  

## <a name="understanding-asc-for-iot-module-twins"></a>Présentation des jumeaux de module ASC pour IoT 

Pour les solutions IoT intégrées à Azure, les jumeaux d’appareil jouent un rôle essentiel dans la gestion d’appareils et l’automatisation des processus. 

ASC pour IoT offre une intégration complète à votre plateforme de gestion d’appareils IoT existants, ce qui vous permet de gérer l’état de sécurité de vos appareils et de tirer parti des fonctionnalités de contrôle d’appareils existants. L’intégration ASC pour IoT est obtenue par l’utilisation du mécanisme de jumelage IoT Hub.  

Consultez [Jumeaux de module IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) pour en savoir plus sur le concept général des jumeaux de module dans Azure IoT Hub. 
 
ASC pour IoT utilise le mécanisme de jumelage de module et gère le jumeau de module de sécurité pour chacun de vos appareils. Le jumeau de module de sécurité contient toutes les informations relatives à la sécurité de l’appareil pour chacun de vos appareils. 
 
Afin de tirer pleinement parti des caractéristiques ASC pour IoT, vous devez créer, configurer et utiliser ces jumeaux de module de sécurité pour chaque appareil dans le service.  

## <a name="create-asc-for-iot-module-twin"></a>Créer un jumeau de module ASC pour IoT 

Les jumeaux de module ASC pour IoT peuvent être créés en mode batch par le biais de la configuration par défaut, ou individuellement avec des configurations spécifiques pour chaque appareil. Pour la création par lot, destinée aux appareils nouveaux ou n’ayant pas de jumeau de module, utilisez le [script de commandes par lot de module](https://aka.ms/iot-security-github-create-module). 

>[!NOTE] 
> L’utilisation de la méthode de traitement par lot ne remplace pas les jumeaux de module existants. L’utilisation de la méthode de traitement par lot crée UNIQUEMENT des jumeaux de module pour les appareils qui ne disposent pas encore d’un jumeau de module. 

Consultez [Modifier un jumeau de module de sécurité](how-to-modify-security-module-twin.md) pour savoir comment modifier ou changer la configuration d’un jumeau de module existant. 

Pour créer un jumeau de module ASC pour IoT destiné à un appareil, utilisez les instructions suivantes : 

1. Dans votre hub IoT, recherchez et sélectionnez l’appareil pour lequel vous souhaitez créer un jumeau de module de sécurité dans votre hub IoT. 
1. Dans le champ **Nom de l’identité Microsoft**, entrez **ascforiotsecurity**.
1. Cliquez sur **Enregistrer**. 

## <a name="verify-creation-of-a-module-twin"></a>Vérifier la création d’un jumeau de module

Pour vérifier l’existence d’un jumeau de module de sécurité pour un appareil particulier :

1. Dans votre hub IoT, sélectionnez **Appareils IoT** dans le menu **Explorateurs**.    
1. Entrez l’ID de l’appareil, ou sélectionnez une option dans le **champ de recherche des appareils**, puis cliquez sur **Interroger les appareils**. 
    ![Interroger les appareils](./media/quickstart/verify-security-module-twin.png)
1. Sélectionnez l’appareil ou double-cliquez dessus pour ouvrir la page des détails de l’appareil. 
1. Sélectionnez le menu **Identités de module** et vérifiez l’existence du module **ascforiotsecurity** et d’un **État de connexion** défini sur **Connecté**dans la liste des identités de module associées à l’appareil. 
    ![Modules associés à un appareil](./media/quickstart/verify-security-module-twin-2.png)


Pour en savoir plus sur la personnalisation des propriétés des jumeaux de module ASC pour IoT, consultez [Configuration de l’agent](concept-agent-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment configurer les alertes personnalisées...

> [!div class="nextstepaction"]
> [Configurer les alertes personnalisées](quickstart-create-custom-alerts.md)
