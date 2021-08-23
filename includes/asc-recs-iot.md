---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 07/25/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: d20d6ad62c7513ff810c1b151085019136021dc9
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675157"
---
Il existe **12** recommandations dans cette catégorie.

|Recommandation |Description |Gravité |
|---|---|---|
|[La stratégie de filtre IP par défaut devrait être de refuser](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5a3d6cdd-8eb3-46d2-ba11-d24a0d47fe65) |La configuration du filtre IP doit avoir des règles définies pour le trafic autorisé et refuser tout autre trafic par défaut<br />(Aucune stratégie associée) |Moyenne |
|[Les journaux de diagnostic dans IoT Hub doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/77785808-ce86-4e40-b45f-19110a547397) |Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.<br />(Stratégie associée : [Les journaux de diagnostic dans IoT Hub doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |Faible |
|[ Informations d’identification d’authentification identiques](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/9d07b7e6-2986-4964-a76c-b2689604e212) |Informations d’authentification identiques à l’IoT Hub utilisé par plusieurs appareils, ce qui peut indiquer qu’un appareil illégitime usurpe l’identité d’un appareil légitime. Cela crée aussi un risque d’emprunt d’identité de l’appareil par un attaquant.<br />(Aucune stratégie associée) |Élevé |
|[ Appareils IoT – Agent envoyant des messages sous-exploités](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a9a59ebb-5d6f-42f5-92a1-036fd0fd1879) |La capacité de taille des messages de l’agent IoT est actuellement sous-exploitée, ce qui entraîne une augmentation du nombre de messages envoyés. Ajuster les intervalles des messages pour une meilleure utilisation<br />(Aucune stratégie associée) |Faible |
|[ Appareils IoT – Le processus audité a arrêté d’envoyer des événements](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d74d2738-2485-4103-9919-69c7e63776ec) |Les événements de sécurité provenant du processus audité ne sont plus reçus depuis cet appareil<br />(Aucune stratégie associée) |Élevé |
|[ Appareils IoT – Ports ouverts sur l’appareil](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1a36f14a-8bd8-45f5-abe5-eef88d76ab5b) |Un point de terminaison d’écoute a été trouvé sur l’appareil.<br />(Aucune stratégie associée) |Moyenne |
|[ Appareils IoT – Échec de la validation de la base du système d’exploitation](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5f65e47f-7a00-4bf3-acae-90ee441ee876) |Problèmes de configuration système liés à la sécurité identifiés<br />(Aucune stratégie associée) |Moyenne |
|[ Appareils IoT – Stratégie de pare-feu permissive trouvée dans l’une des chaînes](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/beb62be3-5e78-49bd-ac5f-099250ef3c7c) |Une stratégie de pare-feu autorisée a été trouvée dans les chaînes de pare-feu principal (ENTRÉE/SORTIE). La stratégie doit refuser tout le trafic par défaut et définir des règles pour autoriser toute communication nécessaire vers/depuis l'appareil<br />(Aucune stratégie associée) |Moyenne |
|[ Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne d’entrée](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ba975338-f956-41e7-a9f2-7614832d382d) |Une règle dans le pare-feu contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée<br />(Aucune stratégie associée) |Moyenne |
|[ Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne de sortie](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d5a8d84a-9ad0-42e2-80e0-d38e3d46028a) |Une règle contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée dans le pare-feu<br />(Aucune stratégie associée) |Moyenne |
|[ Appareils IoT – Mise à niveau de la suite de chiffrement TLS requise](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/2acc27c6-5fdb-405e-9080-cb66b850c8f5) |Configurations TLS non sécurisées détectées. Mise à niveau immédiate de la suite de chiffrement TLS recommandée<br />(Aucune stratégie associée) |Moyenne |
|[ Plage IP large pour la règle de filtre IP](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d8326952-60bb-40fb-b33f-51e662708a88) |La plage d’adresses IP source d’une des règles de filtre IP Autoriser est trop grande. Des règles trop permissives risquent d’exposer le hub IoT à des personnes malveillantes.<br />(Aucune stratégie associée) |Medium |
|||
