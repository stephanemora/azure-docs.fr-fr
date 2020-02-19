---
title: Installer Azure IoT Edge sur Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Windows 10, Windows Server et Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: e3f55f9be28a8b53f012e111e43ba1f495b1d585
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186472"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installer le runtime Azure IoT Edge sur Windows

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud.

Pour en savoir plus sur le runtime IoT Edge, voir [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes d’installation du runtime Azure IoT Edge avec les conteneurs Windows sur votre système Windows x64 (AMD/Intel).

> [!NOTE]
> Un problème connu du système d’exploitation Windows empêche la transition vers les états Veille et Veille prolongée lorsque des modules IoT Edge (conteneurs Windows Nano Server isolés en processus) sont exécutés. Ce problème affecte l’autonomie de la batterie de l’appareil.
>
> Pour contourner ce problème, utilisez la commande `Stop-Service iotedge` pour arrêter tous les modules IoT Edge en cours d’exécution avant d’utiliser ces états d’alimentation.

L’utilisation de conteneurs Linux sur les systèmes Windows n’est pas une configuration de production recommandée ni prise en charge pour Azure IoT Edge. Cependant, ils peuvent être utilisés à des fins de développement et de test. Pour plus d’informations, consultez [Utiliser IoT Edge sur Windows pour exécuter des conteneurs Linux](how-to-install-iot-edge-windows-with-linux.md).

Pour plus d’informations sur ce qu’inclut la dernière version d’IoT Edge, voir les [publications d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Prérequis

Cette section a pour but de vous aider à déterminer si votre appareil Windows peut prendre en charge IoT Edge, ainsi qu’à le préparer à un moteur de conteneur avant installation.

### <a name="supported-windows-versions"></a>Versions de Windows prises en charge

Pour les scénarios de développement et de test, Azure IoT Edge avec conteneurs Windows peut être installé sur n’importe quelle version de Windows 10 ou Windows Server 2019 (build 17763) qui prend en charge la fonctionnalité de conteneurs. Pour plus d’informations sur les systèmes d’exploitation actuellement pris en charge pour les scénarios de production, consultez [Systèmes pris en charge par Azure IoT Edge](support.md#operating-systems).

Les appareils IoT Core doivent inclure la fonctionnalité facultative IoT Core – Conteneurs Windows pour prendre en charge le runtime IoT Edge. Utilisez la commande suivante dans une [session PowerShell à distance](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) pour vérifier que les conteneurs Windows sont pris en charge sur votre appareil :

```powershell
Get-Service vmcompute
```

Si le service est présent, vous devez obtenir une réponse positive avec l’état de service **en cours d’exécution**. Si le service vmcompute est introuvable, cela signifie que votre appareil ne présente pas la configuration requise pour IoT Edge. Contactez votre fournisseur en matériel pour vous renseigner à propos de la prise en charge de cette fonctionnalité.

### <a name="prepare-for-a-container-engine"></a>Préparer pour un moteur de conteneur

Azure IoT Edge s’appuie sur un moteur de conteneur [compatible avec OCI](https://www.opencontainers.org/). En production, utilisez le moteur de Moby inclus dans le script d’installation pour exécuter des conteneurs Windows sur votre appareil Windows.

## <a name="install-iot-edge-on-a-new-device"></a>Installer IoT Edge sur un nouvel appareil

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

Un script PowerShell télécharge et installe le démon de sécurité Azure IoT Edge. Le démon de sécurité démarre ensuite le premier des deux modules de runtime, l’agent IoT Edge qui permet de déployer à distance d’autres modules.

>[!TIP]
>Pour les appareils IoT Core, nous vous recommandons d’exécuter les commandes d’installation à l’aide d’une session RemotePowerShell. Pour plus d’informations, consultez [Utilisation de PowerShell pour Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Lorsque vous installez le runtime IoT Edge pour la première fois sur un appareil, vous devez fournir à celui-ci une identité d’un hub IoT. Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning Service (DPS) pour provisionner automatiquement des appareils, ce qui s’avère particulièrement utile lorsque vous devez configurer de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement.

Les sections suivantes décrivent les cas et paramètres d’utilisation courants du script d’installation d’IoT Edge sur un nouvel appareil.

### <a name="option-1-install-and-manually-provision"></a>Option 1 : Installer et approvisionner manuellement

Dans cette première option, vous fournissez une **chaîne de connexion d’appareil** générée par IoT Hub pour provisionner l’appareil.

Cet exemple présente une installation manuelle avec des conteneurs Windows :

1. Si ce n’est pas déjà fait, inscrivez un nouvel appareil IoT Edge et récupérez la **chaîne de connexion d’appareil**. Copiez cette chaîne de connexion pour l’utiliser plus tard dans cette section. Vous pouvez effectuer cette étape à l’aide des outils suivants :

   * [Azure portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Exécutez PowerShell ISE en tant qu’administrateur.

   >[!NOTE]
   >Utilisez une session AMD64 de PowerShell pour installer IoT Edge, pas PowerShell (x86). Si vous ne savez pas quel type de session vous utilisez, exécutez la commande suivante :
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. La commande **Deploy-IoTEdge** vérifie que votre ordinateur Windows a une version prise en charge, active la fonctionnalité des conteneurs, avant de télécharger le runtime moby et le runtime IoT Edge. Par défaut, la commande utilise des conteneurs de Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. À ce stade, les appareils IoT Core peuvent redémarrer automatiquement. D’autres appareils Windows 10 ou Windows Server peuvent vous inviter à redémarrer. Si c’est le cas, redémarrez votre appareil maintenant. Une fois votre appareil prêt, exécutez à nouveau PowerShell en tant qu’administrateur.

5. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande est une commande d’approvisionnement manuel avec des conteneurs Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Lorsque vous y êtes invité, renseignez la chaîne de connexion que vous avez récupérée dans l’étape 1. La chaîne de connexion d’appareil associe l’appareil physique à un ID d’appareil dans IoT Hub.

   La chaîne de connexion se présente au format suivant et ne doit pas contenir de guillemets : `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Utilisez les étapes dans [Vérifier une installation réussie](#verify-successful-installation) pour vérifier le statut d’IoT Edge sur votre appareil.

Lorsque vous installez et provisionnez un appareil manuellement, vous pouvez utiliser des paramètres supplémentaires pour modifier l’installation, à savoir :

* Diriger le trafic pour qu’il transite par un serveur proxy,
* Pointer le programme d’installation sur un répertoire hors connexion
* Déclarer une image conteneur d’agent spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé

Pour plus d’informations sur ces options d’installation, passez à la section décrivant [tous les paramètres d’installation](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Option n°2 : Installer et approvisionner automatiquement

Dans cette deuxième option, vous provisionnez l’appareil en utilisant le Service IoT Hub Device Provisioning. Fournissez l’**ID de l’étendue** d’une instance du service Device Provisioning, ainsi que toute autre information spécifique de votre [mécanisme d’attestation](../iot-dps/concepts-security.md#attestation-mechanism) favori :

* [Créer et approvisionner un appareil IoT Edge simulé avec un TPM virtuel sous Windows](how-to-auto-provision-simulated-device-windows.md)
* [Créer et approvisionner un appareil IoT Edge à l’aide d’une attestation de clé symétrique](how-to-auto-provision-symmetric-keys.md)

Lorsque vous installez et approvisionnez un appareil automatiquement, vous pouvez utiliser des paramètres supplémentaires pour modifier l’installation, à savoir :

* Diriger le trafic pour qu’il transite par un serveur proxy,
* Pointer le programme d’installation sur un répertoire hors connexion
* Déclarer une image conteneur d’agent spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé

Pour plus d’informations sur ces options d’installation, poursuivez la lecture de cet article, ou passez à la section décrivant [tous les paramètres d’installation](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Installation d’une version hors connexion ou spécifique

Lors de l’installation, deux fichiers sont téléchargés :

* Le fichier cab Microsoft Azure IoT Edge, contenant le démon de sécurité IoT Edge (iotedged), le moteur du conteneur Moby et Moby CLI.
* Programme d’installation MSI du Package redistribuable Visual C++ (Runtime VC)

Si votre appareil est hors connexion pendant l’installation, ou si vous souhaitez installer une version spécifique d’IoT Edge, vous pouvez télécharger à l’avance l’un de ces fichiers ou les deux sur l’appareil. Quand il est temps d’effectuer l’installation, pointez le script d’installation sur le répertoire contenant les fichiers téléchargés. Le programme d’installation commence par vérifier le répertoire, puis télécharge uniquement les composants qui ne s’y trouvent pas. Si tous les fichiers sont disponibles hors connexion, vous pouvez procéder à l’installation sans connexion Internet.

Pour accéder aux derniers fichiers d’installation IoT Edge ainsi qu’aux versions précédentes, voir les [publications d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Pour procéder à l’installation avec des composants hors connexion, utilisez le paramètre `-OfflineInstallationPath` dans la commande Deploy-IoTEdge en spécifiant le chemin d’accès absolu au répertoire des fichiers. Par exemple,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>Le paramètre `-OfflineInstallationPath` recherche un fichier nommé **Microsoft-Azure-IoTEdge.cab** dans le répertoire fourni. À partir d’IoT Edge version 1.0.9-rc4, deux fichiers. cab peuvent être utilisés, un pour les appareils AMD64 et l’autre pour ARM32. Téléchargez le fichier approprié pour votre appareil, puis renommez le fichier pour supprimer le suffixe d’architecture.

La commande `Deploy-IoTEdge` installe les composants IoT Edge, puis vous devez passer à la commande `Initialize-IoTEdge` pour approvisionner l’appareil avec son ID d’appareil et sa connexion IoT Hub. Exécutez la commande directement et fournissez une chaîne de connexion d’IoT Hub, ou utilisez l’un des liens de la section précédente pour savoir comment approvisionner automatiquement des appareils avec le service Device Provisioning.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

Vous pouvez aussi utiliser le paramètre de chemin d’accès d’installation hors ligne avec la commande Update-IoTEdge.

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Vérifiez l’état du service IoT Edge. Il doit être listé comme étant en cours d’exécution.  

```powershell
Get-Service iotedge
```

Examinez les journaux d’activité du service des 5 dernières minutes. Si vous venez tout juste d’installer le runtime IoT Edge, vous pouvez voir une liste d’erreurs de la période entre l’exécution de la commande **Deploy-IoTEdge** et de la commande **Initialize-IoTEdge**. Ces erreurs sont attendues, car le service tente de démarrer avant d’être configuré.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Exécutez une vérification automatisée des erreurs de configuration et de mise en réseau les plus courantes.

```powershell
iotedge check
```

Répertoriez les modules en cours d’exécution. Après une nouvelle installation, le seul module que vous devez voir en cours d’exécution est **edgeAgent**. Une fois que vous avez [déployé les modules IoT Edge](how-to-deploy-modules-portal.md) pour la première fois, l’autre module système, **edgeHub**, démarre également sur l’appareil.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gérer les conteneurs de module

Le service IoT Edge nécessite un moteur de conteneur en cours d’exécution sur votre appareil. Lorsque vous déployez un module sur un appareil, le runtime IoT Edge utilise le moteur de conteneur pour extraire l’image de conteneur à partir d’un registre dans le cloud. Le service IoT Edge vous permet d’interagir avec vos modules et de récupérer les journaux, mais vous pouvez être amené à utiliser le moteur de conteneur pour interagir avec le conteneur lui-même.

Pour plus d’informations sur les concepts de module, consultez [Comprendre les modules Azure IoT Edge](iot-edge-modules.md).

Si vous exécutez les conteneurs Windows sur votre appareil Windows IoT Edge, l’installation de IoT Edge inclus le moteur de conteneur Moby. Le moteur Moby était basé sur les mêmes normes que Docker et a été conçu pour s’exécuter en parallèle sur le même ordinateur que le bureau Docker. Pour cette raison, si vous souhaitez cibler des conteneurs gérés par le moteur Moby, vous devez cibler spécifiquement ce moteur et non Docker.

Par exemple, pour répertorier toutes les images Docker, exécutez la commande suivante :

```powershell
docker images
```

Pour répertorier toutes les images Moby, modifiez la même commande en ajoutant un pointeur vers le moteur Moby :

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Le moteur d’URI est répertorié dans la sortie du script d’installation, ou vous pouvez le trouver dans la section des paramètres du runtime du conteneur pour le fichier config.yaml.

![moby_runtime uri dans config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Pour plus d’informations sur les commandes que vous pouvez utiliser pour interagir avec des conteneurs et des images en cours d’exécution sur votre appareil, consultez [Interfaces de ligne de commande Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Windows, utilisez la commande suivante à partir d’une fenêtre PowerShell d’administration. Cette commande supprime le runtime IoT Edge, ainsi que votre configuration existante et les données du moteur Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

La commande de Uninstall-IoTEdge ne fonctionne pas sur Windows IoT Core. Pour supprimer IoT Edge des appareils Windows IoT Core, vous devez redéployer votre image Windows IoT Core.

Pour plus d’informations sur les options de désinstallation, utilisez la commande `Get-Help Uninstall-IoTEdge -full`.

## <a name="all-installation-parameters"></a>Tous les paramètres d’installation

Les sections précédentes ont présenté des scénarios d’installation courants avec des exemples montrant comment utiliser des paramètres pour modifier le script d’installation. Cette section fournit des tables de référence des paramètres communs utilisés pour installer, mettre à jour ou désinstaller IoT Edge.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

La commande Deploy-IoTEdge télécharge et déploie le démon de sécurité IoT Edge et ses dépendances. La commande de déploiement accepte ces paramètres communs, entre autres. Pour obtenir la liste complète, utilisez la commande `Get-Help Deploy-IoTEdge -full`.  

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Si aucun système d’exploitation de conteneur n’est spécifié, Windows est la valeur par défaut.<br><br>Pour des conteneurs Windows, IoT Edge utilise le moteur de conteneur Moby inclus dans l’installation. Pour des conteneurs Linux, vous devez installer un moteur de conteneur avant de commencer l’installation. |
| **Proxy** | URL du proxy | Incluez ce paramètre si votre appareil doit passer par un serveur proxy pour accéder à internet. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Chemin du répertoire | Si ce paramètre est inclus, le programme d’installation vérifie la présence dans le répertoire listé des fichiers cab IoT Edge et MSI du Runtime VC. Tous les fichiers introuvables dans le répertoire sont téléchargés. Si les deux fichiers sont dans le répertoire, vous pouvez installer IoT Edge sans connexion Internet. Vous pouvez également utiliser ce paramètre pour utiliser une version spécifique. |
| **InvokeWebRequestParameters** | Table de hachage de paramètres et valeurs | Pendant l’installation, plusieurs requêtes web sont effectuées. Ce champ permet de définir des paramètres pour ces requêtes HTTP. Ce paramètre est utile pour configurer des informations d’identification pour des serveurs proxy. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Aucun | Cet indicateur permet au script de déploiement de redémarrer l’ordinateur sans invite, si nécessaire. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

La commande Initialize-IoTEdge configure IoT Edge avec votre chaîne de connexion d’appareil et les détails opérationnels. La plupart des informations générées par cette commande sont ensuite stockées dans le fichier iotedge\config.yaml. La commande d’initialisation accepte ces paramètres communs, entre autres. Pour obtenir la liste complète, utilisez la commande `Get-Help Initialize-IoTEdge -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **Manuel** | None | **Paramètre booléen**. Si aucun type d’approvisionnement n’est spécifié, la valeur par défaut est manuelle.<br><br>Déclare que vous allez fournir une chaîne de connexion d’appareil pour provisionner l’appareil manuellement |
| **Dps** | None | **Paramètre booléen**. Si aucun type d’approvisionnement n’est spécifié, la valeur par défaut est manuelle.<br><br>Déclare que vous allez fournir l’ID de l’étendue d’un Service Device Provisioning (DPS) et l’ID d’inscription de votre appareil à provisionner via DPS.  |
| **DeviceConnectionString** | Chaîne de connexion d’un appareil IoT Edge inscrit dans un IoT Hub, entre apostrophes | **Obligatoire** pour une installation manuelle. Si vous ne fournissez pas de chaîne de connexion dans les paramètres de script, vous serez invité à la spécifier durant l’installation. |
| **ScopeId** | ID de l’étendue d’une instance de Service Device Provisioning associée à votre IoT Hub. | **Obligatoire** pour une installation DPS. Si vous ne fournissez pas d’ID de l’étendue dans les paramètres de script, vous serez invité à le spécifier durant l’installation. |
| **RegistrationID** | ID d’inscription généré par votre appareil | **Obligatoire** pour l’installation DPS si vous utilisez TPM ou une attestation de clé symétrique. |
| **SymmetricKey** | Clé symétrique utilisée pour provisionner l’identité de l’appareil IoT Edge lors de l’utilisation de DPS | **Obligatoire** pour l’installation DPS si vous utilisez l’attestation de clé symétrique. |
| **ContainerOs** | **Windows** ou **Linux** | Si aucun système d’exploitation de conteneur n’est spécifié, Windows est la valeur par défaut.<br><br>Pour des conteneurs Windows, IoT Edge utilise le moteur de conteneur Moby inclus dans l’installation. Pour des conteneurs Linux, vous devez installer un moteur de conteneur avant de commencer l’installation. |
| **InvokeWebRequestParameters** | Table de hachage de paramètres et valeurs | Pendant l’installation, plusieurs requêtes web sont effectuées. Ce champ permet de définir des paramètres pour ces requêtes HTTP. Ce paramètre est utile pour configurer des informations d’identification pour des serveurs proxy. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI d’image de l’agent IoT Edge | Par défaut, une nouvelle installation d’IoT Edge utilise la dernière balise de déploiement pour l’image d’agent IoT Edge. Ce paramètre permet de définir une balise spécifique pour la version d’image, ou de fournir votre propre image d’agent. Pour plus d’informations, voir [Comprendre les balises IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nom d’utilisateur** | Nom d’utilisateur du registre de conteneurs | Utilisez ce paramètre uniquement si vous définissez le paramètre -AgentImage sur un conteneur dans un registre privé. Fournissez un nom d’utilisateur ayant accès au registre. |
| **Mot de passe** | Chaîne de mot de passe sécurisé | Utilisez ce paramètre uniquement si vous définissez le paramètre -AgentImage sur un conteneur dans un registre privé. Fournissez le mot de passe pour l’accès au registre. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Si aucun système d’exploitation de conteneur n’est spécifié, Windows est la valeur par défaut. Pour des conteneurs Windows, un moteur de conteneur est inclus dans l’installation. Pour des conteneurs Linux, vous devez installer un moteur de conteneur avant de commencer l’installation. |
| **Proxy** | URL du proxy | Incluez ce paramètre si votre appareil doit passer par un serveur proxy pour accéder à internet. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Table de hachage de paramètres et valeurs | Pendant l’installation, plusieurs requêtes web sont effectuées. Ce champ permet de définir des paramètres pour ces requêtes HTTP. Ce paramètre est utile pour configurer des informations d’identification pour des serveurs proxy. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Chemin du répertoire | Si ce paramètre est inclus, le programme d’installation vérifie la présence dans le répertoire listé des fichiers cab IoT Edge et MSI du Runtime VC. Tous les fichiers introuvables dans le répertoire sont téléchargés. Si les deux fichiers sont dans le répertoire, vous pouvez installer IoT Edge sans connexion Internet. Vous pouvez également utiliser ce paramètre pour utiliser une version spécifique. |
| **RestartIfNeeded** | Aucun | Cet indicateur permet au script de déploiement de redémarrer l’ordinateur sans invite, si nécessaire. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **Force** | Aucun | Cet indicateur force la désinstallation si la précédente tentative de désinstallation a échoué.
| **RestartIfNeeded** | Aucun | Cet indicateur permet au script de désinstallation de redémarrer l’ordinateur sans invite, si nécessaire. |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous éprouvez des difficultés à installer correctement IoT Edge, voir la page [Résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
