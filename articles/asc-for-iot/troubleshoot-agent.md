---
title: Guide de résolution des problèmes de l’agent de sécurité Linux Azure Security Center pour IoT | Microsoft Docs
description: Résolvez les problèmes liés aux agents de sécurité Azure Security Center pour IoT pour Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600316"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guide de résolution des problèmes liés à l’agent de sécurité (Linux)

Cet article explique comment résoudre les problèmes potentiels dans le processus de démarrage de l’agent de sécurité.

L’agent Azure Security Center pour IoT démarre automatiquement immédiatement après son installation. Le processus de démarrage de l’agent inclut la lecture de la configuration locale, la connexion à Azure IoT Hub et la récupération de la configuration du jumeau distant. L’échec de l’une de ces étapes peut entraîner l’échec de l’agent de sécurité.

Dans ce guide de résolution des problèmes, vous allez apprendre à :
> [!div class="checklist"]
> * Vérifier si l’agent de sécurité est en cours d’exécution
> * Obtenir les erreurs de l’agent de sécurité
> * Comprendre et corriger les erreurs de l’agent de sécurité 

## <a name="validate-if-the-security-agent-is-running"></a>Vérifier si l’agent de sécurité est en cours d’exécution

1. Pour valider le fait que l’agent de sécurité est en cours d’exécution, patientez quelques minutes après l’installation de l’agent et exécutez la commande suivante. 
     <br>

    **Agent C**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **Agent C#**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Si la commande retourne une ligne vide, l’agent de sécurité n’a pas pu démarrer correctement.    

## <a name="force-stop-the-security-agent"></a>Forcer l’arrêt de l’agent de sécurité 
Dans les cas où l’agent de sécurité ne peut pas démarrer, arrêtez l’agent à l’aide de la commande suivante, puis passez au tableau des erreurs ci-dessous :

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>Obtenir les erreurs de l’agent de sécurité
1. Récupérez la ou les erreurs de l’agent de sécurité en exécutant la commande suivante :
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. La commande de récupération des erreurs de l’agent de sécurité récupère tous les journaux créés par l’agent Azure Security Center pour IoT. Utilisez le tableau suivant pour comprendre les erreurs et prendre les mesures appropriées pour la correction. 

> [!Note]
> Les journaux d’erreurs sont affichés dans l’ordre chronologique. Veillez à noter le timestamp de chaque erreur pour faciliter la correction. 

## <a name="restart-the-agent"></a>Redémarrer l’agent

1. Une fois que vous avez localisé et résolu une erreur de l’agent de sécurité, essayez de redémarrer l’agent en exécutant la commande suivante. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. Répétez la procédure précédente pour récupérer et arrêter les erreurs si l’agent continue d’échouer au processus de démarrage. 

## <a name="understand-security-agent-errors"></a>Présentation des erreurs de l’agent de sécurité

La plupart des erreurs de l’agent de sécurité sont affichées au format suivant : 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Code d'erreur | Sous-code d’erreur | Détails de l’erreur | Correction C | Correction C# |
|:-----------|:---------------|:--------|:------------|:------------|
| Configuration locale | Configuration manquante | Une configuration est manquante dans le fichier de configuration local. Le message d’erreur doit indiquer la clé manquante. | Ajoutez la clé manquante au fichier /var/LocalConfiguration.json. Pour plus d’informations, consultez [cs-localconfig-reference](azure-iot-security-local-configuration-c.md).| Ajoutez la clé manquante au fichier General.config. Pour plus d’informations, consultez [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md). |
| Configuration locale | Impossible d’analyser la configuration | Une valeur de configuration ne peut pas être analysée. Le message d’erreur doit indiquer la clé qui ne peut pas être analysée. Une valeur de configuration ne peut pas être analysée soit parce qu’elle n’est pas dans le type attendu, soit parce qu’elle est hors limites. | Corrigez la valeur de la clé dans le fichier /var/LocalConfiguration.json afin qu’elle corresponde au schéma LocalConfiguration. Pour plus d’informations, consultez [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md). |  Corrigez la valeur de la clé dans le fichier General.config afin qu’elle corresponde au schéma. Pour plus d’informations, consultez [cs-localconfig-reference](azure-iot-security-local-configuration-c.md).|
| Configuration locale | Format de fichier | L’analyse du fichier de configuration a échoué. | Le fichier de configuration est endommagé, téléchargez l’agent, puis réinstallez-le. | |
| Configuration distante | Délai d'expiration | L’agent n’a pas pu extraire le jumeau du module azureiotsecurity dans le délai imparti. | Vérifiez que la configuration de l’authentification est correcte, puis réessayez. | L’agent n’a pas pu extraire le jumeau du module azureiotsecurity dans le délai imparti. | Vérifiez que la configuration de l’authentification est correcte, puis réessayez. |
| Authentication | Le fichier n’existe pas | Le chemin d’accès du fichier n’existe pas. | Vérifiez que le fichier existe dans le chemin d’accès spécifié ou accédez au fichier **LocalConfiguration.json** et modifiez la configuration **FilePath**. | Vérifiez que le fichier existe dans le chemin d’accès spécifié ou accédez au fichier **Authentication.config** et modifiez la configuration **filePath**.|
| Authentication | Autorisation de fichier | L’agent ne dispose pas des autorisations suffisantes pour ouvrir le fichier. | Accordez aux utilisateurs **asciotagent** des autorisations de lecture sur le fichier dans le chemin d’accès donné. | Vérifiez que le fichier est accessible. |
| Authentication | Format de fichier | Le fichier donné n’est pas dans le format correct. | Vérifiez que le fichier est dans le format correct. Les types de fichier pris en charge sont .pfx et .pem. | Vérifiez que le fichier est un fichier de certificat valide. |
| Authentication | Non autorisé | L’agent n’a pas pu s’authentifier auprès de IoT Hub avec les informations d’identification fournies. | Validez la configuration de l’authentification dans le fichier LocalConfiguration. Passez en revue la configuration de l’authentification et vérifiez que tous les détails sont corrects. Confirmez que le secret dans le fichier correspond à l’identité authentifiée. | Validez la configuration de l’authentification dans le fichier Authentication.config. Passez en revue la configuration de l’authentification et vérifiez que tous les détails sont corrects. Confirmez que le secret dans le fichier correspond à l’identité authentifiée.
| Authentication | Introuvable | L’appareil/le module a été trouvé. | Valider la configuration de l’authentification : vérifiez que le nom d’hôte est correct, que l’appareil existe dans IoT Hub et qu’il a un module jumeau azureiotsecurity. |  Valider la configuration de l’authentification : vérifiez que le nom d’hôte est correct, que l’appareil existe dans IoT Hub et qu’il a un module jumeau azureiotsecurity. |
| Authentication | Configuration manquante | Une configuration est manquante dans le fichier *Authentication.config*. Le message d’erreur doit indiquer la clé manquante. | Ajoutez la clé manquante au fichier *LocalConfiguration.json*.| Ajoutez la clé manquante au fichier *Authentication.config*. Pour plus d’informations, consultez [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md). |
| Authentication | Impossible d’analyser la configuration | Une valeur de configuration ne peut pas être analysée. Le message d’erreur doit indiquer la clé qui ne peut pas être analysée. Une valeur de configuration ne peut pas être analysée soit parce qu’elle n’est pas dans le type attendu, soit parce qu’elle est hors limites. |Corrigez la valeur de la clé dans le fichier **LocalConfiguration.json**. |Corrigez la valeur de la clé dans le fichier **Authentication.config** afin qu’elle corresponde au schéma. Pour plus d’informations, consultez [cs-localconfig-reference](azure-iot-security-local-configuration-c.md).|
|

## <a name="restart-the-agent"></a>Redémarrer l’agent
1. Une fois que vous avez localisé et résolu une erreur de l’agent de sécurité, essayez de redémarrer l’agent en exécutant la commande suivante :

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. Si nécessaire, répétez les processus précédents pour forcer l’arrêt de l’agent et récupérer les erreurs si l’agent continue à faire échouer le processus de démarrage. 

## <a name="next-steps"></a>Étapes suivantes
- Lire la [Vue d’ensemble](overview.md) du service Microsoft Azure Security Center pour IoT
- En savoir plus sur l’[architecture](architecture.md) d’Azure Security Center pour IoT
- Activer le [service](quickstart-onboard-iot-hub.md) Azure Security Center pour IoT
- Lire la [FAQ](resources-frequently-asked-questions.md) du service Azure Security Center pour IoT
- Découvrir comment accéder aux [données de sécurité brutes](how-to-security-data-access.md)
- Comprendre les [recommandations](concept-recommendations.md)
- Comprendre les [alertes de sécurité](concept-security-alerts.md)
