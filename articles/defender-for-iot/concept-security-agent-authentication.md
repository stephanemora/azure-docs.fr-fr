---
title: Authentification des agents de sécurité (préversion)
titleSuffix: Azure Defender for IoT
description: Effectuez l’authentification auprès du micro-agent suivant deux méthodes possibles.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 018da32b90c7730f82eaa5aa2cd2b5c7a64719a6
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809607"
---
# <a name="micro-agent-authentication-methods-preview"></a>Méthodes d’authentification auprès du micro-agent (préversion)

Il existe deux options d’authentification auprès du micro-agent Defender for IoT : 

- Chaîne de connexion 

- Certificat 

## <a name="authentication-using-a-connection-string"></a>Authentification avec une chaîne de connexion 

Pour pouvoir utiliser une chaîne de connexion, vous devez ajouter un fichier nommé `connection_string.txt` qui la comporte au format UTF-8 dans le répertoire de l’agent Defender. Par exemple,

```azurecli
echo “<connection string>” > connection_string.txt 
```

Une fois que vous avez effectué cette opération, redémarrez le service avec la commande suivante.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Authentification avec un certificat 


Pour effectuer l’authentification avec un certificat, procédez comme suit : 

1. Placez la partie publique encodée PEM d’un certificat dans un fichier nommé `certificate_public.pem` au sein du répertoire de l’agent Defender.
1. Placez la clé privée encodée PEM dans un fichier nommé `certificate_private.pem` au sein du répertoire de l’agent Defender.
1. Placez la chaîne de connexion appropriée dans un fichier nommé `connection_string.txt`. Par exemple,

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Du fait de cette action, l’agent Defender s’attend à ce qu’un certificat soit fourni pour l’authentification. 

1. Redémarrez le service avec le code suivant : 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Vérification que le micro-agent s’exécute correctement 

1. Exécutez la commande suivante : 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Vérifiez que le service est stable en vous assurant qu’il est **actif** et que la durée du bon fonctionnement du processus est appropriée : 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Vérification de la stabilité du service par un contrôle de son activité.":::

## <a name="next-steps"></a>Étapes suivantes

Vérifiez votre [Posture de sécurité – Benchmark CIS](concept-security-posture.md).
