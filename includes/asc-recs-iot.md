---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 05/05/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 285aa520f22874cc9ad93ca253086617a7363943
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108766470"
---
Il existe **12** recommandations dans cette catégorie.

|Recommandation |Description |Gravité |
|---|---|---|
|La stratégie de filtre IP par défaut devrait être de refuser |La configuration du filtre IP doit avoir des règles définies pour le trafic autorisé et refuser tout autre trafic par défaut<br />(Aucune stratégie associée) |Moyenne |
|Les journaux de diagnostic dans IoT Hub doivent être activés |Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.<br />(Stratégie associée : [Les journaux de diagnostic dans IoT Hub doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |Faible |
| Informations d’identification d’authentification identiques |Informations d’authentification identiques à l’IoT Hub utilisé par plusieurs appareils, ce qui peut indiquer qu’un appareil illégitime usurpe l’identité d’un appareil légitime. Cela crée aussi un risque d’emprunt d’identité de l’appareil par un attaquant.<br />(Aucune stratégie associée) |Élevé |
| Appareils IoT – Agent envoyant des messages sous-exploités |La capacité de taille des messages de l’agent IoT est actuellement sous-exploitée, ce qui entraîne une augmentation du nombre de messages envoyés. Ajuster les intervalles des messages pour une meilleure utilisation<br />(Aucune stratégie associée) |Faible |
| Appareils IoT – Le processus audité a arrêté d’envoyer des événements |Les événements de sécurité provenant du processus audité ne sont plus reçus depuis cet appareil<br />(Aucune stratégie associée) |Élevé |
| Appareils IoT – Ports ouverts sur l’appareil |Un point de terminaison d’écoute a été trouvé sur l’appareil.<br />(Aucune stratégie associée) |Moyenne |
| Appareils IoT – Échec de la validation de la base du système d’exploitation |Problèmes de configuration système liés à la sécurité identifiés<br />(Aucune stratégie associée) |Moyenne |
| Appareils IoT – Stratégie de pare-feu permissive trouvée dans l’une des chaînes |Une stratégie de pare-feu autorisée a été trouvée dans les chaînes de pare-feu principal (ENTRÉE/SORTIE). La stratégie doit refuser tout le trafic par défaut et définir des règles pour autoriser toute communication nécessaire vers/depuis l'appareil<br />(Aucune stratégie associée) |Moyenne |
| Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne d’entrée |Une règle dans le pare-feu contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée<br />(Aucune stratégie associée) |Moyenne |
| Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne de sortie |Une règle contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée dans le pare-feu<br />(Aucune stratégie associée) |Moyenne |
| Appareils IoT – Mise à niveau de la suite de chiffrement TLS requise |Configurations TLS non sécurisées détectées. Mise à niveau immédiate de la suite de chiffrement TLS recommandée<br />(Aucune stratégie associée) |Moyenne |
| Plage IP large pour la règle de filtre IP |La plage d’adresses IP source d’une des règles de filtre IP Autoriser est trop grande. Des règles trop permissives risquent d’exposer le hub IoT à des personnes malveillantes.<br />(Aucune stratégie associée) |Medium |
|||
