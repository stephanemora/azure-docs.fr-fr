---
title: Comprendre ASC pour l’aperçu des recommandations de sécurité IoT | Microsoft Docs
description: En savoir plus sur le concept des recommandations de sécurité et comment elles sont utilisées dans ASC pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1e4582d93d1e3380ecdabdb241f27839d4da4565
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541856"
---
# <a name="security-recommendations"></a>Recommandations de sécurité

> [!IMPORTANT]
> ASC pour IoT est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En fonction de l’analyse en cours de la solution, ASC pour IoT fournit les recommandations suivantes si nécessaire pour aider à améliorer et protéger vos appareils, un état opérationnel et un environnement global de IoT Hub. 


## <a name="device-recommendations"></a>Recommandations de l’appareil

Recommandations de périphérique offrent des analyses et des suggestions pour améliorer la sécurité de l’appareil et de comportement. 

| Severity | Nom                                                      | source de données | Description                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Moyenne   | Ouvrir des Ports sur l’appareil                                      | Agent       | Un point de terminaison d'écoute a été trouvé sur l'appareil                                                                                                                                                          |
| Moyenne   | Stratégie de pare-feu permissifs trouvé dans une chaînes de. | Agent       | Autorisé trouvée (e) de stratégie de pare-feu. Stratégie de pare-feu doit refuser tout le trafic par défaut et définir des règles pour permettre la communication nécessaire à l’appareil.                               |
| Moyenne   | Règle de pare-feu permissifs dans la chaîne d’entrée a été trouvé     | Agent       | Une règle dans le pare-feu a été trouvée qui contient un modèle permissif pour un large éventail d’adresses IP ou des ports.                                                                                    |
| Moyenne   | Règle de pare-feu permissifs dans la chaîne de sortie a été trouvé    | Agent       | Une règle dans le pare-feu a été trouvée qui contient un modèle permissif pour un large éventail d’adresses IP ou des ports.                                                                                   |
| Moyenne   | Validation de ligne de base système l’opération a échoué.           | Agent       | N’est pas conforme avec [benchmarks CIS Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendation"></a>Recommandation opérationnelle

Recommandations opérationnelles offrent des analyses et des suggestions pour améliorer la configuration de l’agent.

| Severity | Nom                                    | source de données | Description                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Faible      | L’agent envoie des messages inutilisés          | Agent       | au moins 10 % des messages de sécurité ont été inférieure à 4 Ko au cours des dernières 24 heures.  |
| Faible      | Configuration de représentations de sécurité non optimale | Agent       | Configuration de représentations de sécurité n’est pas optimale.                                        |
| Faible      | Conflit de configuration de représentations de sécurité    | Agent       | Conflits ont été identifiés dans la configuration de représentations de sécurité.                           |


## <a name="iot-hub-recommendations"></a>Recommandations de IoT Hub

Les alertes de recommandation fournissent un aperçu et les suggestions d’actions à améliorer la sécurité de votre environnement.  

| Severity | Nom                                                     | source de données | Description                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Élevé     | Informations d’identification d’authentification identique utilisées par plusieurs périphériques | IoT Hub     | Informations d’identification de l’authentification de IoT Hub sont utilisées par plusieurs appareils. Cela peut indiquer un appareil illégitime empruntant l’identité d’un appareil légitime. Utilisation des informations d’identification en double augmente le risque d’emprunt d’identité de l’appareil par un acteur malveillant. |
| Moyenne   | Stratégie de filtre IP par défaut doit être refuser                  | IoT Hub     | Configuration du filtre IP doit avoir des règles définies pour le trafic autorisé et doivent par défaut, refuser tout autre trafic par défaut.                                                                                                     |
| Moyenne   | Règle de filtre IP inclut des grandes plages IP                   | IoT Hub     | Une plage d’IP source règle Autoriser IP filtre est trop grande. Règles trop permissifs peuvent exposer votre IoT hub pour les personnes malveillantes.                                                                                       |
| Faible      | Activer les journaux de diagnostic dans IoT Hub                       | IoT Hub     | Activez les journaux et conservez-les pendant jusqu’à un an. Rétention des journaux vous permet de recréer les pistes d’activité pour à des fins d’investigation lorsqu’un incident de sécurité se produit ou votre réseau est compromis.                                       |
|