---
title: Scripts PowerShell pour Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Informations de référence sur les scripts PowerShell pour Azure IoT Edge pour Linux sur Windows pour déployer, approvisionner et obtenir l’état IoT Edge pour Linux sur des machines virtuelles Windows.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f0af571f67862c91371b01bee6227d5fb6b291be
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743537"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Scripts PowerShell pour IoT Edge pour Linux sur Windows

Découvrez les scripts PowerShell qui déploient, approvisionnent et obtiennent l’état de votre IoT Edge pour Linux sur une machine virtuelle Windows.

Les commandes décrites dans cet article proviennent du fichier `AzureEFLOW.psm1`, qui se trouve sur votre système dans votre répertoire `WindowsPowerShell` sous `C:\Program Files\WindowsPowerShellModules\AzureEFLOW`.

## <a name="deploy-eflow"></a>Deploy-Eflow

La commande **Deploy-Eflow** est la principale méthode de déploiement. La commande de déploiement crée la machine virtuelle, approvisionne des fichiers et déploie le module d’agent IoT Edge. Si aucun des paramètres n’est requis, vous pouvez les utiliser pour approvisionner votre appareil IoT Edge pendant le déploiement et modifier les paramètres de la machine virtuelle lors de la création. Pour obtenir la liste complète, utilisez la commande `Get-Help Deploy-Eflow -full`.  

>[!NOTE]
>Pour le type d’approvisionnement, **X509** fait actuellement référence exclusivement à l’approvisionnement X509 à l’aide d’un [Service Azure IoT Hub Device Provisioning](../iot-dps/about-iot-dps.md). La méthode d’approvisionnement X509 manuelle n’est pas prise en charge actuellement.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| eflowVhdxDir | Chemin du répertoire | Répertoire dans lequel le déploiement stocke le fichier VHDX pour la machine virtuelle. |
| provisioningType | **manuel**, **TPM**, **X509** ou **symétrique** |  Définit le type d’approvisionnement que vous souhaitez utiliser pour votre appareil IoT Edge. |
| devConnString | Chaîne de connexion d’un appareil IoT Edge existant | Chaîne de connexion d’appareil pour l’approvisionnement manuel d’un appareil IoT Edge (**manuel**). |
| symmKey | Clé primaire pour une inscription DPS existante ou clé primaire d’un appareil IoT Edge existant inscrit à l’aide de clés symétriques | Clé symétrique pour l’approvisionnement d’un appareil IoT Edge (**X509** ou **symétrique**). |
| scopeId | ID d’étendue pour une instance DPS existante. | ID d’étendue pour l’approvisionnement d’un appareil IoT Edge (**X509** ou **symétrique**). |
| registrationId | ID d’inscription d’un appareil IoT Edge existant | ID d’inscription pour l’approvisionnement d’un appareil IoT Edge (**X509** ou **symétrique**). |
| identityCertLocVm | Chemin d’accès au répertoire ; doit se trouver dans un dossier qui peut être détenu par le service `iotedge` | Chemin d’accès de destination absolu du certificat d’identité sur votre machine virtuelle pour l’approvisionnement d’un appareil IoT Edge (**X509** ou **symétrique**). |
| identityCertLocWin | Chemin du répertoire | Chemin d’accès source absolu du certificat d’identité dans Windows pour l’approvisionnement d’un appareil IoT Edge (**X509** ou **symétrique**). |
| identityPkLocVm |  | Chemin d’accès au répertoire ; doit se trouver dans un dossier qui peut être détenu par le service `iotedge` | Chemin d’accès de destination absolu de la clé privée d’identité sur votre machine virtuelle pour l’approvisionnement d’un appareil IoT Edge (**X509** ou **symétrique**). |
| identityPkLocWin | Chemin du répertoire | Chemin d’accès source absolu de la clé privée d’identité dans Windows pour l’approvisionnement d’un appareil IoT Edge (**X509** ou **symétrique**). |
| vmSizeDefintion | Pas plus de 30 caractères | Définition du nombre de cœurs et de RAM disponible pour la machine virtuelle. **Valeur par défaut** : Standard_K8S_v1. |
| vmDiskSize | Entre 8 Go et 256 Go | Taille de disque maximale du disque dur virtuel de taille dynamique. **Valeur par défaut** : 16 Go. |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |
| vnetType | **Transparent** ou **ICS** | Type de commutateur virtuel. **Valeur par défaut** : Transparent. |
| vnetName | Pas plus de 64 caractères | Nom du commutateur virtuel. **Valeur par défaut** : Externe. |
| enableVtpm | None | **Paramètre booléen**. Créez la machine virtuelle avec le TPM activé ou désactivé. |
| mobyPackageVersion | Pas plus de 30 caractères |  Version du package Moby à vérifier ou à installer sur la machine virtuelle.  **Valeur par défaut :** 19.03.11. |
| iotedgePackageVersion | Pas plus de 30 caractères | Version du package IoT Edge à vérifier ou à installer sur la machine virtuelle. **Valeur par défaut :** 1.1.0. |
| installPackages | None | **Paramètre booléen**. Lorsqu’il est activé, le script tente d’installer les packages Moby et IoT Edge plutôt que de vérifier seulement si les packages sont présents. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

La commande **Verify-EflowVm** est une fonction exposée pour vérifier si IoT Edge pour Linux sur une machine virtuelle Windows a été créé. Il accepte uniquement des paramètres communs et retourne la valeur **true** si la machine virtuelle a été créée et **false** dans le cas contraire. Pour toute information supplémentaire, utilisez la commande `Get-Help Verify-EflowVm -full`.

## <a name="provision-eflowvm"></a>Provision-EflowVm

La commande **Provision-EflowVm** ajoute les informations d’approvisionnement de votre appareil IoT Edge au fichier `config.yaml` IoT Edge de la machine virtuelle. L’approvisionnement peut également être effectué pendant la phase de déploiement en définissant des paramètres dans la commande **Deploy-Eflow**. Pour toute information supplémentaire, utilisez la commande `Get-Help Provision-EflowVm -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |
| provisioningType | **manuel**, **TPM**, **X509** ou **symétrique** |  Définit le type d’approvisionnement que vous souhaitez utiliser pour votre appareil IoT Edge. |
| devConnString | Chaîne de connexion d’un appareil IoT Edge existant | Chaîne de connexion d’appareil pour l’approvisionnement manuel d’un appareil IoT Edge (**manuel**). |
| symmKey | Clé primaire pour une inscription DPS existante ou clé primaire d’un appareil IoT Edge existant inscrit à l’aide de clés symétriques | Clé symétrique pour l’approvisionnement d’un appareil IoT Edge (**DPS**, **symétrique**). |
| scopeId | ID d’étendue pour une instance DPS existante. | ID d’étendue pour l’approvisionnement d’un appareil IoT Edge (**DPS**). |
| registrationId | ID d’inscription d’un appareil IoT Edge existant | ID d’inscription pour l’approvisionnement d’un appareil IoT Edge (**DPS**). |
| identityCertLocVm | Chemin d’accès au répertoire ; doit se trouver dans un dossier qui peut être détenu par le service `iotedge` | Chemin d’accès de destination absolu du certificat d’identité sur votre machine virtuelle pour l’approvisionnement d’un appareil IoT Edge (**DPS**, **X509**). |
| identityCertLocWin | Chemin du répertoire | Chemin d’accès source absolu du certificat d’identité dans Windows pour l’approvisionnement d’un appareil IoT Edge (**DPS**, **X509**). |
| identityPkLocVm |  | Chemin d’accès au répertoire ; doit se trouver dans un dossier qui peut être détenu par le service `iotedge` | Chemin d’accès de destination absolu de la clé privée d’identité sur votre machine virtuelle pour l’approvisionnement d’un appareil IoT Edge (**DPS**, **X509**). |
| identityPkLocWin | Chemin du répertoire | Chemin d’accès source absolu de la clé privée d’identité dans Windows pour l’approvisionnement d’un appareil IoT Edge (**DPS**, **X509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

La commande **Get-EflowVmName** est utilisée pour interroger le nom d’hôte actuel de la machine virtuelle. Cette commande permet de tenir compte du fait que le nom d’hôte Windows peut changer dans le temps. Elle accepte uniquement des paramètres communs et retourne le nom d’hôte actuel de la machine virtuelle. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmName -full`.

## <a name="get-eflowlogs"></a>Get-EflowLogs

La commande **Get-EflowLogs** est utilisée pour collecter et regrouper les journaux d’IoT Edge pour le déploiement Linux sur Windows. Elle génère les journaux regroupés sous la forme d’un dossier `.zip`. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowLogs -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

La commande **Get-EflowVmTpmProvisioningInfo** est utilisée pour collecter et afficher les informations d’approvisionnement vTPM de la machine virtuelle. Si la machine virtuelle a été créée sans vTPM, la commande signale qu’elle n’a pas pu trouver les informations d’approvisionnement TPM. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmTpmProvisioningInfo -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

La commande **Get-EflowVmAddr** est utilisée pour rechercher et afficher les adresses IP et Mac de la machine virtuelle. Elle accepte uniquement des paramètres communs. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmAddr -full`.

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

La commande **Get-EflowVmSystemInformation** est utilisée pour collecter et afficher des informations système de la machine virtuelle, telles que la mémoire et l’utilisation du stockage. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmSystemInformation -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

La commande **Get-EflowVmEdgeInformation** est utilisée pour collecter et afficher des informations IoT Edge de la machine virtuelle, telles que la version d’IoT Edge exécutée par la machine virtuelle. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmEdgeInformation -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

La commande **Get-EflowVmEdgeModuleList** est utilisée pour interroger et afficher la liste des modules IoT Edge exécutés sur la machine virtuelle. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmEdgeModuleList -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

La commande **Get-EflowVmEdgeStatus** est utilisée pour interroger et afficher l’état du runtime IoT Edge sur la machine virtuelle. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmEdgeStatus -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

La commande **Get-EflowVmUserName** est utilisée pour interroger et afficher le nom d’utilisateur de la machine virtuelle qui a été utilisé pour créer la machine virtuelle à partir du Registre. Elle accepte uniquement des paramètres communs. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmUserName -full`.

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

La commande **Get-EflowVmSshKey** est utilisée pour interroger et afficher la clé SSH utilisée par la machine virtuelle. Elle accepte uniquement des paramètres communs. Pour toute information supplémentaire, utilisez la commande `Get-Help Get-EflowVmSshKey -full`.

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

La commande **Ssh-EflowVm** est utilisée pour la connexion SSH à la machine virtuelle. Pour toute information supplémentaire, utilisez la commande `Get-Help Ssh-EflowVm -full`.

| Paramètre | Valeurs acceptées | Commentaires |
| --------- | --------------- | -------- |
| vmUser | Pas plus de 30 caractères | Nom d’utilisateur pour la connexion à la machine virtuelle. |

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser ces commandes dans l’article suivant :

* [Installer Azure IoT Edge pour Linux sur Windows](how-to-install-iot-edge-windows.md)

* Reportez-vous aux [informations de référence sur le script PowerShell IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-scripts.md#Deploy-Eflow) pour toutes les commandes disponibles via PowerShell.
