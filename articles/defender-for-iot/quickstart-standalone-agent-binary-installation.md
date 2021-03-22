---
title: Installer le micro-agent Defender pour IoT
titleSuffix: Azure Defender for IoT
description: Découvrez comment installer et authentifier le micro-agent Defender.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/3/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: ccf28c47e2e1438a141e2497da70d32c1832ddb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120434"
---
# <a name="install-defender-for-iot-micro-agent"></a>Installer le micro-agent Defender pour IoT 

Cet article fournit explique comment installer et authentifier le micro-agent Defender.

## <a name="prerequisites"></a>Prérequis

Avant d’installer le module Defender pour IoT, vous devez créer une identité de module dans le hub IoT. Pour plus d’informations sur la création d’une identité de module, consultez [Créer un jumeau de module pour le micro-agent Defender pour IoT](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Installer le package

Installez et configurez le dépôt de packages Microsoft en suivant [ces instructions](/windows-server/administration/linux-package-repository-for-microsoft-software). 

Pour Debian 9, les instructions n’incluent pas le dépôt qui doit être ajouté. Pour l’ajouter, utilisez les commandes suivantes : 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Pour installer le package du micro-agent Defender sur Debian et sur les distributions Linux basées sur Ubuntu, utilisez la commande suivante :

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Méthodes d’authentification du micro-agent 

Les deux options utilisées pour authentifier le micro-agent Defender pour IoT sont les suivantes : 

- Chaîne de connexion. 

- Certificat.

### <a name="authenticate-using-a-connection-string"></a>S’authentifier au moyen d’une chaîne de connexion

Pour s’authentifier à l’aide d’une chaîne de connexion :

1. Placez un fichier nommé `connection_string.txt` contenant la chaîne de connexion codée au format UTF-8 dans le chemin `/var/defender_iot_micro_agent` du répertoire de l’agent Defender en entrant la commande suivante :

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    Le fichier `connection_string.txt` doit maintenant se trouver dans le chemin suivant : `/var/defender_iot_micro_agent/connection_string.txt`.

1. Redémarrez le service à l’aide de cette commande :  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>S’authentifier à l’aide d’un certificat

Pour s’authentifier à l’aide d’un certificat :

1. Procurez-vous un certificat en suivant [ces instructions](../iot-hub/iot-hub-security-x509-get-started.md).

1. Placez la partie publique codée en PEM du certificat et la clé privée dans le répertoire de l’agent Defender, dans les fichiers nommés `certificate_public.pem` et `certificate_private.pem`. 

1. Placez la chaîne de connexion appropriée dans le fichier `connection_string.txt`. La chaîne de connexion doit ressembler à ceci : 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Cette chaîne alerte l’agent Defender, afin de demander qu’un certificat soit fourni pour l’authentification. 

1. Redémarrez le service à l’aide de la commande suivante :  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Valider votre installation

Pour valider votre installation :

1. Vérifiez que le micro-agent s’exécute correctement avec la commande suivante :  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Vérifiez que le service est stable en contrôlant qu’il est actif (`active`) et que la durée du bon fonctionnement du processus est appropriée

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Vérifiez que votre service est stable et actif.":::
 
## <a name="testing-the-system-end-to-end"></a>Test du système de bout en bout 

Vous pouvez tester le système de bout en bout en créant un fichier déclencheur sur l’appareil. Le fichier déclencheur fait en sorte que l’analyse de ligne de base effectuée dans l’agent détecte le fichier comme une violation de la ligne de base. 

Créez un fichier sur le système de fichiers avec la commande suivante :

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Une recommandation d’échec de validation de ligne de base se produit dans le hub, avec CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0 comme valeur `CceId` : 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Recommandation d’échec de validation de ligne de base qui se produit dans le hub." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Patientez jusqu’à une heure pour que la recommandation apparaisse dans le hub. 

## <a name="micro-agent-versioning"></a>Gestion de versions du micro-agent 

Pour installer une version spécifique du micro-agent Defender pour IoT, exécutez la commande suivante : 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Étapes suivantes

[Générer le micro-agent Defender à partir du code source](quickstart-building-the-defender-micro-agent-from-source.md)