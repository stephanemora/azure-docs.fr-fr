---
title: Installer Azure IoT Edge sur Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Windows 10, Windows Server et Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 95e984f6f08af01a2ffd7b9b4e0ec598d73f4d05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595087"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installer le runtime Azure IoT Edge sur Windows

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. 

Pour en savoir plus sur le runtime IoT Edge, voir [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes d’installation du runtime Azure IoT Edge sur votre système Windows x64 (AMD/Intel). La prise en charge de Windows est actuellement assurée en préversion.

> [!NOTE]
> Un problème de système d’exploitation Windows connu empêche la transition pour la mise en veille et veille prolongée d’états d’alimentation lors de l’exécutant des modules IoT Edge (processus isolé des conteneurs Windows Nano Server). Ce problème a un impact sur la durée de la batterie sur l’appareil.
>
> Pour résoudre ce problème, utilisez la commande `Stop-Service iotedge` pour arrêter tous les modules IoT Edge en cours d’exécution avant d’utiliser ces États d’alimentation. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

À l’aide de Linux conteneur sur les systèmes de Windows n’est pas une configuration de production recommandé ou pris en charge pour Azure IoT Edge. Cependant, ils peuvent être utilisés à des fins de développement et de test. 

## <a name="prerequisites"></a>Conditions préalables

Cette section a pour but de vous aider à déterminer si votre appareil Windows peut prendre en charge IoT Edge, ainsi qu’à le préparer à un moteur de conteneur avant installation. 

### <a name="supported-windows-versions"></a>Versions de Windows prises en charge

Azure IoT Edge prend en charge différentes versions de Windows, selon que vous exécutez des conteneurs de Windows ou Linux. 

La dernière version d’Azure IoT Edge avec des conteneurs Windows est exécutable sur les versions suivantes de Windows :
* Windows 10 ou Windows IoT Core avec la mise à jour d’octobre 2018 (build 17763)
* Windows Server 2019 (Build 17763)

La dernière version d’Azure IoT Edge avec des conteneurs Linux est exécutable sur les versions suivantes de Windows : 
* Mise à jour anniversaire Windows 10 (build 14393) ou une version ultérieure
* Windows Server 2016 ou version ultérieure

Pour plus d’informations sur les systèmes d’exploitation actuellement pris en charge, voir [Prise en charge d’Azure IoT Edge](support.md#operating-systems). 

Pour plus d’informations sur qu’inclut la dernière version d’IoT Edge, voir les [publications d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

### <a name="prepare-for-a-container-engine"></a>Préparer pour un moteur de conteneur 

Azure IoT Edge s’appuie sur un moteur de conteneur [compatible avec OCI](https://www.opencontainers.org/). En production, utilisez le moteur de Moby inclus dans le script d’installation pour exécuter des conteneurs Windows sur votre appareil Windows. Pour développer et tester, vous pouvez exécuter des conteneurs Linux sur votre appareil Windows, mais vous devez installer et configurer un moteur de conteneur avant d’installer IoT Edge. Dans les deux cas, voir les sections suivantes afin de connaître les conditions préalables pour préparer votre appareil. 

Si vous souhaitez installer IoT Edge sur un ordinateur virtuel, activer la virtualisation imbriquée et allouez au moins 2 Go de mémoire. Comment vous activez la virtualisation imbriquée est différente selon l’hyperviseur de votre utilisation. Pour Hyper-V, les ordinateurs virtuels de génération 2 ont imbriquées virtualisation est activée par défaut. Pour VMWare, il existe un bouton bascule pour activer la fonctionnalité sur votre machine virtuelle. 

#### <a name="moby-engine-for-windows-containers"></a>Moteur Moby pour conteneurs Windows

Pour des appareils Windows exécutant IoT Edge en production, Moby est le seul moteur de conteneur officiellement pris en charge. Le script d’installation installe automatiquement le moteur Moby sur votre appareil avant d’installer IoT Edge. Préparez votre appareil en activant la fonctionnalité Conteneurs. 

1. Dans la barre de démarrage, recherchez **Activer ou désactiver des fonctionnalités Windows**, puis ouvrez le programme Panneau de configuration.
2. Recherchez et sélectionnez **Conteneurs**.
3. Sélectionnez **OK**. 

#### <a name="docker-for-linux-containers"></a>Docker pour conteneurs Linux

Si vous utilisez Windows pour développer et tester des conteneurs pour appareils Linux, vous pouvez utiliser [Docker pour Windows](https://www.docker.com/docker-windows) en tant que moteur de conteneur. Vous pouvez configurer Docker pour [utiliser des conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). Vous devez installer Docker et le configurer avant d’installer IoT Edge. Les conteneurs Linux ne sont pas pris en charge sur des appareils Windows en production. 

Si votre appareil Azure IoT Edge est un ordinateur Windows, vérifiez qu’il présente la [configuration requise](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) pour Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installer IoT Edge sur un nouvel appareil

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package implique l’acceptation de ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

Un script PowerShell télécharge et installe le démon de sécurité Azure IoT Edge. Le démon de sécurité démarre ensuite le premier des deux modules de runtime, l’agent IoT Edge qui permet de déployer à distance d’autres modules. 

Lorsque vous installez le runtime IoT Edge pour la première fois sur un appareil, vous devez fournir à celui-ci une identité d’un hub IoT. Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning pour provisionner automatiquement des appareils, ce qui s’avère particulièrement utile lorsque vous devez configurer de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement. 

Les sections suivantes décrivent les cas et paramètres d’utilisation courants du script d’installation d’IoT Edge sur un nouvel appareil. 

### <a name="option-1-install-and-manually-provision"></a>Option 1 : Installer et approvisionner manuellement

Dans cette première option, vous fournissez une chaîne de connexion d’appareil générée par IoT Hub pour provisionner l’appareil. 

Suivez les étapes de la rubrique [Inscrire un nouvel appareil Azure IoT Edge](how-to-register-device-portal.md) pour inscrire votre appareil et récupérer la chaîne de connexion d’appareil. 

Cet exemple présente une installation manuelle avec des conteneurs Windows :

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

Lorsque vous installez et provisionnez un appareil manuellement, vous pouvez utiliser des paramètres supplémentaires pour modifier l’installation, à savoir :
* Diriger le trafic pour qu’il transite par un serveur proxy
* Pointer le programme d’installation sur un répertoire hors connexion
* Déclarer une image conteneur d’agent spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé
* Ignorer l’installation de l’interface de ligne de commande Moby

Pour plus d’informations sur ces options d’installation, poursuivez la lecture de cet article, ou passez à la section décrivant [tous les paramètres d’installation](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Option 2 : Installer et approvisionner automatiquement

Dans cette deuxième option, vous provisionnez l’appareil en utilisant le Service IoT Hub Device Provisioning. Fournissez l’**ID de l’étendue** d’une instance du Service Device Provisioning, et l’**ID d’inscription** de votre appareil.

Suivez les étapes de la rubrique [Créer et provisionner un appareil Edge avec TPM simulé sur Windows](how-to-auto-provision-simulated-device-windows.md) pour configurer le service Device Provisioning et récupérer son **ID d’étendue**, simuler un appareil TPM et récupérer son **ID d’inscription**, puis créez une inscription individuelle. Une fois l’appareil inscrit dans votre IoT Hub, passez à l’installation.  

   >[!TIP]
   >Gardez ouverte la fenêtre dans laquelle s’exécute le simulateur TPM durant l’installation et les tests. 

L’exemple suivant présente une installation automatique avec des conteneurs Windows :

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

Lorsque vous installez et provisionnez un appareil manuellement, vous pouvez utiliser des paramètres supplémentaires pour modifier l’installation, à savoir :
* Diriger le trafic pour qu’il transite par un serveur proxy
* Pointer le programme d’installation sur un répertoire hors connexion
* Déclarer une image conteneur d’agent spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé
* Ignorer l’installation de l’interface de ligne de commande Moby

Pour plus d’informations sur ces options d’installation, poursuivez la lecture de cet article, ou passez à la section décrivant [tous les paramètres d’installation](#all-installation-parameters).

## <a name="update-an-existing-installation"></a>Mettre à jour une installation existante

Si vous avez déjà installé le runtime IoT Edge sur un appareil et l’avez provisionné avec une identité d’IoT Hub, vous pouvez utiliser un script d’installation simplifiée. L’indicateur `-ExistingConfig` déclare qu’un fichier de configuration IoT Edge existe déjà sur l’appareil. Le fichier de configuration contient des informations sur l’identité de l’appareil, ainsi que des certificats et des paramètres réseau. Vous pouvez utiliser cette option d’installation si votre appareil a été initialement configuré manuellement ou automatiquement. 

Pour plus d’informations, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).

Cet exemple présente une installation qui pointe vers un fichier de configuration existant, et utilise des conteneurs Windows : 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

Lorsque vous installez IoT Edge à partir d’un fichier de configuration existant, vous pouvez utiliser des paramètres supplémentaires pour modifier l’installation, à savoir :
* Diriger le trafic pour qu’il transite par un serveur proxy,
* Pointer le programme d’installation sur un répertoire hors connexion, ou 
* Ignorer l’installation de l’interface de ligne de commande Moby. 

Vous ne pouvez pas déclarer une image conteneur d’agent IoT Edge avec des paramètres de script, car ces informations sont déjà définies dans le fichier de configuration de l’installation précédente. Si vous souhaitez modifier l’image conteneur d’agent, faites-le dans le fichier config.yaml. 

Pour plus d’informations sur ces options d’installation, poursuivez la lecture de cet article, ou passez à la section décrivant [tous les paramètres d’installation](#all-installation-parameters).

## <a name="offline-installation"></a>Installation hors connexion

Lors de l’installation, quatre fichiers sont téléchargés : 
* Zip du démon de sécurité IoT Edge (iotedgd) 
* Zip du moteur Moby
* Zip de l’interface de ligne de commande Moby
* Programme d’installation msi du Package redistribuable Visual C++ (Runtime VC)

Vous pouvez télécharger un ou l’ensemble de ces fichiers à l’avance sur l’appareil, puis pointer le script d’installation sur le répertoire contenant les fichiers. Le programme d’installation commence par vérifier le contenu du répertoire, puis télécharge uniquement les composants qui ne s’y trouvent pas. Si les quatre fichiers sont disponibles hors connexion, vous pouvez procéder à l’installation sans connexion Internet. Vous pouvez également utiliser cette fonctionnalité pour remplacer les versions en ligne d’un ou plusieurs composants.  

Pour accéder aux derniers fichiers d’installation ainsi qu’aux versions précédentes, voir les [publications d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Pour procéder à l’installation avec des composants hors connexion, utilisez le paramètre `-OfflineInstallationPath` en spécifiant le chemin d’accès absolu au répertoire des fichiers. Par exemple,

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>Tous les paramètres d’installation

Les sections précédentes ont présenté des scénarios d’installation courants avec des exemples montrant comment utiliser des paramètres pour modifier le script d’installation. Cette section fournit une table de référence des paramètres valides, que vous pouvez utiliser pour installer IoT Edge. Pour plus d’informations, exécutez `get-help Install-SecurityDaemon -full` dans une fenêtre PowerShell. 

Pour installer IoT Edge avec une configuration existante, votre commande d’installation peut utiliser ces paramètres courants : 

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **Manuel** | Aucun | **Paramètre booléen**. Chaque installation doit être déclarée en tant qu’opération manuelle, DPS ou existingconfig.<br><br>Déclare que vous allez fournir une chaîne de connexion d’appareil pour provisionner l’appareil manuellement |
| **Dps** | Aucun | **Paramètre booléen**. Chaque installation doit être déclarée en tant qu’opération manuelle, DPS ou existingconfig.<br><br>Déclare que vous allez fournir l’ID de l’étendue d’un Service Device Provisioning (DPS) et l’ID d’inscription de votre appareil à provisionner via DPS.  |
| **ExistingConfig** | Aucun | **Paramètre booléen**. Chaque installation doit être déclarée en tant qu’opération manuelle, DPS ou existingconfig.<br><br>Déclare qu’un fichier config.yaml existe déjà sur l’appareil, avec ses informations de provisionnement. |
| **DeviceConnectionString** | Chaîne de connexion d’un appareil IoT Edge inscrit dans un IoT Hub, entre apostrophes | **Obligatoire** pour une installation manuelle. Si vous ne fournissez pas de chaîne de connexion dans les paramètres de script, vous serez invité à la spécifier durant l’installation. |
| **ScopeId** | ID de l’étendue d’une instance de Service Device Provisioning associée à votre IoT Hub. | **Obligatoire** pour une installation DPS. Si vous ne fournissez pas d’ID de l’étendue dans les paramètres de script, vous serez invité à le spécifier durant l’installation. |
| **RegistrationID** | ID d’inscription généré par votre appareil | **Obligatoire** pour une installation DPS. Si vous ne fournissez pas d’ID d’inscription dans les paramètres de script, vous serez invité à le spécifier durant l’installation. |
| **ContainerOs** | **Windows** ou **Linux** | Si aucun système d’exploitation de conteneur n’est spécifié, Linux est la valeur par défaut. Pour des conteneurs Windows, un moteur de conteneur est inclus dans l’installation. Pour des conteneurs Linux, vous devez installer un moteur de conteneur avant de commencer l’installation. L’exécution de conteneurs Linux sur Windows est un scénario de développement utile, mais qui n’est pas pris en charge en production. |
| **Proxy** | URL du proxy | Incluez ce paramètre si votre appareil doit passer par un serveur proxy pour accéder à internet. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Table de hachage de paramètres et valeurs | Pendant l’installation, plusieurs requêtes web sont effectuées. Ce champ permet de définir des paramètres pour ces requêtes HTTP. Ce paramètre est utile pour configurer des informations d’identification pour des serveurs proxy. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Chemin du répertoire | Si ce paramètre est inclus, le programme d’installation vérifie la présence dans le répertoire des fichiers zip iotedged, zip du moteur Moby, zip de l’interface de ligne de commande Moby et MSI du Runtime VC. Si les quatre fichiers se trouvent dans le répertoire, vous pouvez installer IoT Edge hors connexion. Vous pouvez également utiliser ce paramètre pour remplacer la version en ligne d’un composant spécifique. |
| **AgentImage** | URI d’image de l’agent IoT Edge | Par défaut, une nouvelle installation d’IoT Edge utilise la dernière balise de déploiement pour l’image d’agent IoT Edge. Ce paramètre permet de définir une balise spécifique pour la version d’image, ou de fournir votre propre image d’agent. Pour plus d’informations, voir [Comprendre les balises IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nom d’utilisateur** | Nom d’utilisateur du registre de conteneurs | Utilisez ce paramètre uniquement si vous définissez le paramètre -AgentImage sur un conteneur dans un registre privé. Fournissez un nom d’utilisateur ayant accès au registre. |
| **Mot de passe** | Chaîne de mot de passe sécurisé | Utilisez ce paramètre uniquement si vous définissez le paramètre -AgentImage sur un conteneur dans un registre privé. Fournissez le mot de passe pour l’accès au registre. | 
| **SkipMobyCli** | Aucun | Applicable uniquement si - ContainerOS est défini sur Windows. N’installez pas l’interface de ligne de commande Moby (docker.exe) dans $MobyInstallDirectory. |

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Vous pouvez vérifier l’état du service IoT Edge comme suit : 

```powershell
Get-Service iotedge
```

Examinez les journaux d’activité du service des 5 dernières minutes à l’aide de la commande suivante :

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Enfin, répertoriez les modules en cours d’exécution à l’aide de la commande suivante :

```powershell
iotedge list
```

Après une nouvelle installation, le module uniquement, vous devez voir est en cours d’exécution **edgeAgent**. Après avoir [déployer les modules IoT Edge](how-to-deploy-modules-portal.md), vous verrez d’autres. 

## <a name="manage-module-containers"></a>Gérer les conteneurs de module

Le service IoT Edge requiert un moteur de conteneur en cours d’exécution sur votre appareil. Lorsque vous déployez un module à un appareil, le runtime IoT Edge utilise le moteur de conteneur pour extraire l’image de conteneur à partir d’un Registre dans le cloud. Le service IoT Edge vous permet d’interagir avec vos modules et récupérer les fichiers journaux, mais vous pouvez être amené à utiliser le moteur de conteneur pour interagir avec le conteneur lui-même. 

Pour plus d’informations sur les concepts de module, consultez [modules comprendre Azure IoT Edge](iot-edge-modules.md). 

Si vous exécutez les conteneurs Windows sur votre appareil Windows IoT Edge, l’installation de IoT Edge inclus le moteur de conteneur Moby. Si vous développez des conteneurs Linux sur votre ordinateur de développement Windows, vous utilisez probablement le bureau de Docker. Le moteur Moby était basé sur les mêmes normes que Docker et a été conçu pour s’exécuter en parallèle sur le même ordinateur que le bureau de Docker. Pour cette raison, si vous souhaitez les conteneurs cible gérées par le moteur Moby, vous devez cibler spécifiquement ce moteur, au lieu de Docker. 

Par exemple, pour répertorier toutes les images Docker, utilisez la commande suivante :

```powershell
docker images
```

Pour répertorier toutes les images Moby, modifiez la même commande avec un pointeur vers le moteur Moby : 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Le moteur d’URI est répertorié dans la sortie du script d’installation, ou vous pouvez le trouver dans la section de paramètres de runtime de conteneur pour le fichier config.yaml. 

![uri moby_runtime dans config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Pour plus d’informations sur les commandes que vous pouvez utiliser pour interagir avec des conteneurs et des images en cours d’exécution sur votre appareil, consultez [les interfaces de ligne de commande Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Windows, utilisez la commande suivante à partir d’une fenêtre PowerShell d’administration. Cette commande supprime le runtime IoT Edge, ainsi que votre configuration existante et les données du moteur Moby. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

Si vous avez l’intention de réinstaller IoT Edge sur votre appareil, omettez les paramètres `-DeleteConfig` et `-DeleteMobyDataRoot` afin de pouvoir réinstaller le démon de sécurité ultérieurement avec les informations de configuration existantes. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous éprouvez des difficultés à installer correctement IoT Edge, voir la page [Résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
