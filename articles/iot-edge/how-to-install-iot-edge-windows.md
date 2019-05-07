---
title: Installer Azure IoT Edge sur Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Windows 10, Windows Server et Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: e48ab075264423479e792848af522a890736a403
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152688"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installer le runtime Azure IoT Edge sur Windows

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. 

Pour en savoir plus sur le runtime IoT Edge, voir [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes pour installer le runtime Azure IoT Edge sur votre Windows x64 (Intel/AMD) système à l’aide de conteneurs de Windows.

> [!NOTE]
> Un problème de système d’exploitation Windows connu empêche la transition pour la mise en veille et veille prolongée d’états d’alimentation lors de l’exécutant des modules IoT Edge (processus isolé des conteneurs Windows Nano Server). Ce problème a un impact sur la durée de la batterie sur l’appareil.
>
> Pour résoudre ce problème, utilisez la commande `Stop-Service iotedge` pour arrêter tous les modules IoT Edge en cours d’exécution avant d’utiliser ces États d’alimentation. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

L’utilisation de conteneurs Linux sur les systèmes Windows n’est pas une configuration de production recommandée ni prise en charge pour Azure IoT Edge. Cependant, ils peuvent être utilisés à des fins de développement et de test. Pour plus d’informations, consultez [utiliser IoT Edge sur Windows pour exécuter des conteneurs Linux](how-to-install-iot-edge-windows-with-linux.md).

Pour plus d’informations sur ce qui est inclus dans la dernière version de IoT Edge, consultez [Azure IoT Edge libère](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Conditions préalables

Cette section a pour but de vous aider à déterminer si votre appareil Windows peut prendre en charge IoT Edge, ainsi qu’à le préparer à un moteur de conteneur avant installation. 

### <a name="supported-windows-versions"></a>Versions de Windows prises en charge

Pour les scénarios de développement et de test, Azure IoT Edge avec les conteneurs Windows peut être installé sur n’importe quelle version de Windows 10 ou Windows Server 2019 (build 17763) qui prend en charge la fonctionnalité de conteneurs. Pour lequel les systèmes d’exploitation sont actuellement prises en charge pour les scénarios de production d’informations, consultez [Azure IoT Edge systèmes pris en charge](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Préparer pour un moteur de conteneur 

Azure IoT Edge s’appuie sur un moteur de conteneur [compatible avec OCI](https://www.opencontainers.org/). En production, utilisez le moteur de Moby inclus dans le script d’installation pour exécuter des conteneurs Windows sur votre appareil Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Installer IoT Edge sur un nouvel appareil

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package implique l’acceptation de ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

Un script PowerShell télécharge et installe le démon de sécurité Azure IoT Edge. Le démon de sécurité démarre ensuite le premier des deux modules de runtime, l’agent IoT Edge qui permet de déployer à distance d’autres modules. 

Lorsque vous installez le runtime IoT Edge pour la première fois sur un appareil, vous devez fournir à celui-ci une identité d’un hub IoT. Un seul appareil IoT Edge peut être configuré manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning pour provisionner automatiquement des appareils, ce qui s’avère particulièrement utile lorsque vous devez configurer de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement. 

Les sections suivantes décrivent les cas et paramètres d’utilisation courants du script d’installation d’IoT Edge sur un nouvel appareil. 

### <a name="option-1-install-and-manually-provision"></a>Option 1 : Installer et approvisionner manuellement

Dans cette première option, vous fournissez un **chaîne de connexion d’appareil** générés par IoT Hub pour approvisionner l’appareil. 

Cet exemple illustre une installation manuelle avec les conteneurs Windows :

1. Si vous n’avez pas déjà, inscrire un nouvel appareil IoT Edge et récupérer le **chaîne de connexion de périphérique**. Copiez la chaîne de connexion pour une utilisation ultérieure dans cette section. Vous pouvez effectuer cette étape en utilisant les outils suivants :

   * [Portail Azure](how-to-register-device-portal.md)
   * [Interface de ligne de commande Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Exécutez PowerShell ISE en tant qu’administrateur.

3. Le **IoTEdge de déployer** commande vérifie que votre machine Windows se trouve sur une version prise en charge et Active la fonctionnalité de conteneurs et télécharge ensuite le runtime moby et le runtime IoT Edge. Par défaut, la commande à l’aide de conteneurs de Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. À ce stade, les appareils IoT Core peuvent redémarrer automatiquement. Autres appareils Windows 10 ou Windows Server peuvent vous inviter à redémarrer. Dans ce cas, redémarrez votre appareil maintenant. Une fois que votre appareil est prêt, exécutez PowerShell en tant qu’administrateur à nouveau.

5. Le **Initialize-IoTEdge** commande configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande provisionnement manuel avec les conteneurs Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Lorsque vous y êtes invité, fournissez la chaîne de connexion d’appareil que vous avez récupéré à l’étape 1. La chaîne de connexion d’appareil associe l’appareil physique avec un ID d’appareil dans IoT Hub. 

   La chaîne de connexion du périphérique prend le format suivant et ne doit pas inclure de guillemets : `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Utilisez les étapes de [vérifier la réussite de l’installation](#verify-successful-installation) pour vérifier l’état de IoT Edge sur votre appareil. 

Lorsque vous installez et provisionnez un appareil manuellement, vous pouvez utiliser des paramètres supplémentaires pour modifier l’installation, à savoir :
* Diriger le trafic pour qu’il transite par un serveur proxy
* Pointer le programme d’installation sur un répertoire hors connexion
* Déclarer une image conteneur d’agent spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé

Pour plus d’informations sur ces options d’installation, passez directement pour en savoir plus sur [tous les paramètres d’installation](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Option 2 : Installer et approvisionner automatiquement

Dans cette deuxième option, vous provisionnez l’appareil en utilisant le Service IoT Hub Device Provisioning. Fournissez l’**ID de l’étendue** d’une instance du Service Device Provisioning, et l’**ID d’inscription** de votre appareil.

L’exemple suivant illustre une installation automatique avec les conteneurs Windows :

1. Suivez les étapes de la rubrique [Créer et provisionner un appareil Edge avec TPM simulé sur Windows](how-to-auto-provision-simulated-device-windows.md) pour configurer le service Device Provisioning et récupérer son **ID d’étendue**, simuler un appareil TPM et récupérer son **ID d’inscription**, puis créez une inscription individuelle. Une fois que votre appareil est inscrit dans votre IoT hub, suivez ces étapes d’installation.  

   >[!TIP]
   >Gardez ouverte la fenêtre dans laquelle s’exécute le simulateur TPM durant l’installation et les tests. 

2. Exécutez PowerShell ISE en tant qu’administrateur.

3. Le **IoTEdge de déployer** commande vérifie que votre machine Windows se trouve sur une version prise en charge et Active la fonctionnalité de conteneurs et télécharge ensuite le runtime moby et le runtime IoT Edge. Par défaut, la commande à l’aide de conteneurs de Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. À ce stade, les appareils IoT Core peuvent redémarrer automatiquement. Autres appareils Windows 10 ou Windows Server peuvent vous inviter à redémarrer. Dans ce cas, redémarrez votre appareil maintenant. Une fois que votre appareil est prêt, exécutez PowerShell en tant qu’administrateur à nouveau.

6. Le **Initialize-IoTEdge** commande configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande provisionnement manuel avec les conteneurs Windows. Utiliser le `-Dps` indicateur à utiliser le Service Device Provisioning au lieu de provisionnement manuel.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Lorsque vous y êtes invité, fournissez l’ID de portée à partir de votre Service Device Provisioning et l’ID d’inscription à partir de votre appareil, que qui vous devez récupérée à l’étape 1.

8. Utilisez les étapes de [vérifier la réussite de l’installation](#verify-successful-installation) pour vérifier l’état de IoT Edge sur votre appareil. 

Lorsque vous installez et provisionnez un appareil manuellement, vous pouvez utiliser des paramètres supplémentaires pour modifier l’installation, à savoir :
* Diriger le trafic pour qu’il transite par un serveur proxy
* Pointer le programme d’installation sur un répertoire hors connexion
* Déclarer une image conteneur d’agent spécifique, et fournir des informations d’identification si l’image se trouve dans un registre privé

Pour plus d’informations sur ces options d’installation, poursuivez la lecture de cet article, ou passez à la section décrivant [tous les paramètres d’installation](#all-installation-parameters).

## <a name="offline-installation"></a>Installation hors connexion

Lors de l’installation, deux fichiers sont téléchargés : 
* Cab Microsoft Azure IoT Edge, qui contient le démon de sécurité IoT Edge (iotedged), le moteur de conteneur Moby et le Moby CLI.
* Programme d’installation msi du Package redistribuable Visual C++ (Runtime VC)

Vous pouvez télécharger un ou les deux de ces fichiers à l’avance à l’appareil, puis pointer le script d’installation vers le répertoire qui contient les fichiers. Le programme d’installation commence par vérifier le contenu du répertoire, puis télécharge uniquement les composants qui ne s’y trouvent pas. Si tous les fichiers sont disponibles hors connexion, vous pouvez installer sans connexion internet. Vous pouvez également utiliser cette fonctionnalité à installer une version spécifique des composants.  

Pour les derniers fichiers d’installation IoT Edge, ainsi que les versions précédentes, consultez [Azure IoT Edge libère](https://github.com/Azure/azure-iotedge/releases).

Pour installer les composants en mode hors connexion, utilisez le `-OfflineInstallationPath` paramètre en tant que partie de la déployer-IoTEdge commande et fournir le chemin d’accès absolu au répertoire des fichiers. Par exemple,

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Vous pouvez également utiliser le paramètre de chemin d’accès d’installation hors connexion avec la commande de mise à jour-IoTEdge, introduite plus loin dans cet article. 

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Vérifiez l’état du service IoT Edge. Il doit être répertorié comme en cours d’exécution.  

```powershell
Get-Service iotedge
```

Examinez les journaux d’activité du service des 5 dernières minutes. Si vous venez d’installer le runtime IoT Edge, vous pouvez voir une liste d’erreurs à partir de la durée entre en cours d’exécution **déployer-IoTEdge** et **Initialize-IoTEdge**. Ces erreurs sont attendus, comme le service tente de démarrer avant d’en cours de configuration. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Répertoriez les modules en cours d’exécution. Après une nouvelle installation, le module uniquement, vous devez voir est en cours d’exécution **edgeAgent**. Après avoir [déployer les modules IoT Edge](how-to-deploy-modules-portal.md), vous verrez d’autres. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gérer les conteneurs de module

Le service IoT Edge requiert un moteur de conteneur en cours d’exécution sur votre appareil. Lorsque vous déployez un module à un appareil, le runtime IoT Edge utilise le moteur de conteneur pour extraire l’image de conteneur à partir d’un Registre dans le cloud. Le service IoT Edge vous permet d’interagir avec vos modules et récupérer les fichiers journaux, mais vous pouvez être amené à utiliser le moteur de conteneur pour interagir avec le conteneur lui-même. 

Pour plus d’informations sur les concepts de module, consultez [modules comprendre Azure IoT Edge](iot-edge-modules.md). 

Si vous exécutez les conteneurs Windows sur votre appareil Windows IoT Edge, l’installation de IoT Edge inclus le moteur de conteneur Moby. Le moteur Moby était basé sur les mêmes normes que Docker et a été conçu pour s’exécuter en parallèle sur le même ordinateur que le bureau de Docker. Pour cette raison, si vous souhaitez les conteneurs cible gérées par le moteur Moby, vous devez cibler spécifiquement ce moteur, au lieu de Docker. 

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

## <a name="update-an-existing-installation"></a>Mettre à jour une installation existante

Si vous avez déjà installé le runtime IoT Edge sur un appareil avant et approvisionné avec une identité à partir d’IoT Hub, puis vous pouvez mettre à jour le runtime sans avoir à entrer à nouveau vos informations de périphérique. 

Pour plus d’informations, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).

Cet exemple présente une installation qui pointe vers un fichier de configuration existant, et utilise des conteneurs Windows : 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Lorsque vous mettez à jour IoT Edge, vous pouvez utiliser des paramètres supplémentaires pour modifier la mise à jour, y compris :
* Diriger le trafic à passer par un serveur proxy, ou
* Pointer le programme d’installation sur un répertoire hors connexion 
* Le redémarrage sans invite si nécessaire

Vous ne pouvez pas déclarer une image de conteneur de l’agent IoT Edge avec les paramètres de script, car cette information est déjà définie dans le fichier de configuration de l’installation précédente. Si vous souhaitez modifier l’image conteneur d’agent, faites-le dans le fichier config.yaml. 

Pour plus d’informations sur ces options de mise à jour, utilisez la commande `Get-Help Update-IoTEdge -full` ou consultez [tous les paramètres d’installation](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Windows, utilisez la commande suivante à partir d’une fenêtre PowerShell d’administration. Cette commande supprime le runtime IoT Edge, ainsi que votre configuration existante et les données du moteur Moby. 

```powershell
Uninstall-IoTEdge
```

La commande de désinstallation-IoTEdge ne fonctionne pas sur Windows IoT Core. Pour supprimer les appareils Windows IoT Core IoT Edge, vous devez redéployer votre image Windows IoT Core. 

Pour plus d’informations sur les options de désinstallation, utilisez la commande `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Tous les paramètres d’installation

Les sections précédentes ont présenté des scénarios d’installation courants avec des exemples montrant comment utiliser des paramètres pour modifier le script d’installation. Cette section fournit des tables de référence des paramètres communs utilisées pour installer, mettre à jour ou désinstaller IoT Edge. 

### <a name="deploy-iotedge"></a>IoTEdge déployer

La commande déployer-IoTEdge télécharge et déploie le démon de sécurité IoT Edge et ses dépendances. La commande de déploiement accepte ces paramètres communs, entre autres. Pour obtenir la liste complète, utilisez la commande `Get-Help Deploy-IoTEdge -full`.  

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Si aucun système d’exploitation de conteneur n’est spécifié, Windows est la valeur par défaut.<br><br>Pour les conteneurs Windows, IoT Edge utilise le moteur de conteneur moby inclus dans l’installation. Pour des conteneurs Linux, vous devez installer un moteur de conteneur avant de commencer l’installation. |
| **Proxy** | URL du proxy | Incluez ce paramètre si votre appareil doit passer par un serveur proxy pour accéder à internet. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Chemin du répertoire | Si ce paramètre est inclus, le programme d’installation vérifie le répertoire indiqué pour le cab de IoT Edge et les fichiers MSI du Runtime VC requis pour l’installation. N’importe quel introuvable dans le répertoire de fichiers sont téléchargés. Si les deux fichiers sont dans le répertoire, vous pouvez installer IoT Edge sans connexion internet. Vous pouvez également utiliser ce paramètre pour utiliser une version spécifique. |
| **InvokeWebRequestParameters** | Table de hachage de paramètres et valeurs | Pendant l’installation, plusieurs requêtes web sont effectuées. Ce champ permet de définir des paramètres pour ces requêtes HTTP. Ce paramètre est utile pour configurer des informations d’identification pour des serveurs proxy. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Aucun | Cet indicateur permet de redémarrer l’ordinateur sans invite, le script de déploiement si nécessaire. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

La commande Initialize-IoTEdge configure IoT Edge avec votre chaîne de connexion d’appareil et les détails opérationnels. La plupart des informations générées par cette commande est ensuite stockée dans le fichier iotedge\config.yaml. La commande d’initialisation accepte ces paramètres communs, entre autres. Pour obtenir la liste complète, utilisez la commande `Get-Help Initialize-IoTEdge -full`. 

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **Manuel** | Aucun | **Paramètre booléen**. Si aucun type d’approvisionnement n’est spécifié, la valeur par défaut est manuelle.<br><br>Déclare que vous allez fournir une chaîne de connexion d’appareil pour provisionner l’appareil manuellement |
| **Dps** | Aucun | **Paramètre booléen**. Si aucun type d’approvisionnement n’est spécifié, la valeur par défaut est manuelle.<br><br>Déclare que vous allez fournir l’ID de l’étendue d’un Service Device Provisioning (DPS) et l’ID d’inscription de votre appareil à provisionner via DPS.  |
| **DeviceConnectionString** | Chaîne de connexion d’un appareil IoT Edge inscrit dans un IoT Hub, entre apostrophes | **Obligatoire** pour une installation manuelle. Si vous ne fournissez pas de chaîne de connexion dans les paramètres de script, vous serez invité à la spécifier durant l’installation. |
| **ScopeId** | ID de l’étendue d’une instance de Service Device Provisioning associée à votre IoT Hub. | **Obligatoire** pour une installation DPS. Si vous ne fournissez pas d’ID de l’étendue dans les paramètres de script, vous serez invité à le spécifier durant l’installation. |
| **RegistrationID** | ID d’inscription généré par votre appareil | **Obligatoire** pour une installation DPS. Si vous ne fournissez pas d’ID d’inscription dans les paramètres de script, vous serez invité à le spécifier durant l’installation. |
| **ContainerOs** | **Windows** ou **Linux** | Si aucun système d’exploitation de conteneur n’est spécifié, Windows est la valeur par défaut.<br><br>Pour les conteneurs Windows, IoT Edge utilise le moteur de conteneur moby inclus dans l’installation. Pour des conteneurs Linux, vous devez installer un moteur de conteneur avant de commencer l’installation. |
| **InvokeWebRequestParameters** | Table de hachage de paramètres et valeurs | Pendant l’installation, plusieurs requêtes web sont effectuées. Ce champ permet de définir des paramètres pour ces requêtes HTTP. Ce paramètre est utile pour configurer des informations d’identification pour des serveurs proxy. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI d’image de l’agent IoT Edge | Par défaut, une nouvelle installation d’IoT Edge utilise la dernière balise de déploiement pour l’image d’agent IoT Edge. Ce paramètre permet de définir une balise spécifique pour la version d’image, ou de fournir votre propre image d’agent. Pour plus d’informations, voir [Comprendre les balises IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nom d’utilisateur** | Nom d’utilisateur du registre de conteneurs | Utilisez ce paramètre uniquement si vous définissez le paramètre -AgentImage sur un conteneur dans un registre privé. Fournissez un nom d’utilisateur ayant accès au registre. |
| **Mot de passe** | Chaîne de mot de passe sécurisé | Utilisez ce paramètre uniquement si vous définissez le paramètre -AgentImage sur un conteneur dans un registre privé. Fournissez le mot de passe pour l’accès au registre. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Si aucun conteneur de que système d’exploitation est spécifié, Windows est la valeur par défaut. Pour des conteneurs Windows, un moteur de conteneur est inclus dans l’installation. Pour des conteneurs Linux, vous devez installer un moteur de conteneur avant de commencer l’installation. |
| **Proxy** | URL du proxy | Incluez ce paramètre si votre appareil doit passer par un serveur proxy pour accéder à internet. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Table de hachage de paramètres et valeurs | Pendant l’installation, plusieurs requêtes web sont effectuées. Ce champ permet de définir des paramètres pour ces requêtes HTTP. Ce paramètre est utile pour configurer des informations d’identification pour des serveurs proxy. Pour plus d’informations, voir [Configurer un appareil IoT Edge de façon à ce qu’il communique via un serveur proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Chemin du répertoire | Si ce paramètre est inclus, le programme d’installation vérifie le répertoire indiqué pour le cab de IoT Edge et les fichiers MSI du Runtime VC requis pour l’installation. N’importe quel introuvable dans le répertoire de fichiers sont téléchargés. Si les deux fichiers sont dans le répertoire, vous pouvez installer IoT Edge sans connexion internet. Vous pouvez également utiliser ce paramètre pour utiliser une version spécifique. |
| **RestartIfNeeded** | Aucun | Cet indicateur permet de redémarrer l’ordinateur sans invite, le script de déploiement si nécessaire. |


### <a name="uninstall-iotedge"></a>IoTEdge désinstaller

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| **Force** | Aucun | Cet indicateur de force la désinstallation dans le cas où la précédente tentative de désinstallation a échoué. 
| **RestartIfNeeded** | Aucun | Cet indicateur permet le script de désinstallation redémarrer l’ordinateur sans invite, si nécessaire. |


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous éprouvez des difficultés à installer correctement IoT Edge, voir la page [Résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
