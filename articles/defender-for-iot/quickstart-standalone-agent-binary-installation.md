---
title: Installer le micro-agent Defender pour IoT (préversion)
description: Découvrez comment installer et authentifier le micro-agent Defender.
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: ecde9f42acc5cf4a924a657f4f0cdc545c4a668e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782722"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>Installer le micro-agent Defender pour IoT (préversion)

Cet article fournit explique comment installer et authentifier le micro-agent Defender.

## <a name="prerequisites"></a>Prérequis

Avant d’installer le module Defender pour IoT, vous devez créer une identité de module dans l’IoT Hub. Pour plus d’informations sur la manière de créer une identité de module, consultez [Créer un jumeau de module micro-agent Defender-IoT (préversion)](quickstart-create-micro-agent-module-twin.md).

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

- Chaîne de connexion d’identité de module. 

- Certificat.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Authentifier à l’aide d’une chaîne de connexion d’identité de module

Veillez à ce que les [conditions préalables](#prerequisites) pour cet article soient réunies et à créer une identité de module avant de commencer ces étapes. 

#### <a name="get-the-module-identity-connection-string"></a>Obtient la chaîne de connexion d’identité de module

Pour récupérer la chaîne de connexion d’identité de module à partir de l’IoT Hub : 

1. Accédez à l’IoT Hub, puis sélectionnez votre hub.

1. Dans le menu de gauche, dans la section **Explorateurs** , sélectionnez **Appareils IOT**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Dans le menu de gauche, sélectionnez appareils IoT.":::

1. Sélectionnez un appareil dans la liste d’ID d’appareils pour afficher la page **Détails de l’appareil**.

1. Sélectionnez l’onglet  **Identités de module** , puis le module **DefenderIotMicroAgent**  dans la liste des identités de module associées à l’appareil.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Sélectionnez l’onglet Identités de module.":::

1. Dans la page **Détails d’identité de module**, copiez la clé primaire en sélectionnant le bouton **Copier**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Sélectionnez le bouton Copier pour copier la clé primaire.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Configurer l’authentification à l’aide d’une chaîne de connexion d’identité de module

Pour configurer l’agent de sorte qu’il s’authentifie à l’aide d’une chaîne de connexion d’identité de module :

1. Placez un fichier nommé `connection_string.txt` contenant la chaîne de connexion codée au format UTF-8 dans le chemin `/var/defender_iot_micro_agent` du répertoire de l’agent Defender en entrant la commande suivante :

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    Le fichier `connection_string.txt` doit se trouver dans l’emplacement ayant pour chemin d’accès `/var/defender_iot_micro_agent/connection_string.txt`.

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