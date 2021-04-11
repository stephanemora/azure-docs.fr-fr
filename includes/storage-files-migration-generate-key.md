---
title: Fichier include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075569"
---
Les clés de chiffrement des données de service permettent de chiffrer les données client confidentielles, telles que les identifiants du compte de stockage, qui sont envoyées à partir votre service StorSimple Manager sur le périphérique StorSimple. Vous devrez modifier ces clés périodiquement si votre service informatique applique une politique de rotation des clés sur les périphériques de stockage. Le processus de modification de la clé peut être légèrement différent selon qu'il y a un ou plusieurs périphériques gérés par le service StorSimple Manager. Pour plus d’informations, accédez à [StorSimple security and data protection](../articles/storsimple/storsimple-8000-security.md) (Sécurité et protection des données StorSimple).

La modification de la clé de chiffrement est un processus en 3 étapes :

1. À l’aide des scripts Windows PowerShell pour Azure Resource Manager, autorisez un appareil à modifier la clé de chiffrement des données de service.
2. Utiliser Windows PowerShell pour StorSimple pour démarrer la modification de la clé de chiffrement des données du service.
3. Si vous avez plusieurs périphériques StorSimple, mettez à jour la clé de chiffrement des données sur les autres périphériques.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Étape 1 : Utiliser un script Windows PowerShell pour autoriser un appareil à modifier la clé de chiffrement des données de service
En règle générale, l’administrateur de l’appareil demande que l’administrateur du service autorise un appareil à modifier les clés de chiffrement des données du service. Ensuite, l’administrateur du service autorise l’appareil à modifier la clé.

Cette étape est effectuée en utilisant le script basé sur Azure Resource Manager. L’administrateur de services fédérés peut sélectionner un appareil pouvant être autorisé. Après cela, l’appareil est autorisé à démarrer le processus de modification de la clé de chiffrement des données du service. 

Pour plus d’informations sur l’utilisation du script, accédez à [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1).

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Quels appareils peuvent être autorisés à modifier les clés de chiffrement des données du service ?
Un appareil doit respecter les critères suivants avant d’être autorisé à démarrer des modifications de clé de chiffrement des données du service :

* L’appareil doit être en ligne pour bénéficier de l’autorisation de modification de clé de chiffrement des données du service.
* Si le changement de clé n’a pas été démarré, vous devez attendre 30 minutes avant d’autoriser à nouveau le même appareil.
* Vous pouvez autoriser un autre appareil, sous réserve que la modification de la clé n’a pas été démarrée par l’appareil précédemment autorisé. Une fois le nouvel appareil autorisé, l’ancien appareil ne peut plus démarrer la modification.
* Vous ne pouvez pas autoriser un appareil alors que la substitution de la clé de chiffrement des données du service est en cours.
* Vous pouvez autoriser un appareil lorsque certains appareils inscrits auprès du service ont substitué le chiffrement tandis que d’autres ne l’ont pas fait. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Étape 2 : Utiliser Windows PowerShell pour StorSimple pour démarrer la modification de la clé de chiffrement des données du service
Cette étape s’effectue dans l’interface Windows PowerShell pour StorSimple, sur l’appareil StorSimple autorisé.

> [!NOTE]
> Aucune opération ne peut être effectuée dans le portail Azure de votre service StorSimple Manager avant la fin de la substitution de la clé.


Si vous utilisez la console série de l’appareil pour vous connecter à l’interface Windows PowerShell, procédez comme suit.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Pour démarrer la modification de la clé de chiffrement des données du service
1. Sélectionnez Option 1 pour vous connecter avec un accès complet.
2. À l’invite de commandes, tapez :
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Une fois l’applet de commande terminée, vous obtenez une nouvelle clé de chiffrement des données du service. Copiez et enregistrez cette clé pour l’utiliser lors de l’étape 3 de cette procédure. Cette clé permet de mettre à jour tous les appareils restants inscrits auprès du service StorSimple Manager.
   
   > [!NOTE]
   > Ce processus doit être démarré dans les quatre heures suivant l’autorisation d’un appareil StorSimple.
   > 
   > 
   
   Cette nouvelle clé est ensuite envoyée au service pour être transmise à tous les appareils inscrits auprès du service. Une alerte s’affiche alors sur le tableau de bord du service. Le service désactive toutes les opérations sur les appareils inscrits et l’administrateur de l’appareil doit alors mettre à jour la clé de chiffrement des données du service sur les autres appareils. Toutefois, les E/S (hôtes envoyant des données vers le cloud) ne sont pas interrompues.
   
   Si vous avez un seul appareil inscrit auprès de votre service, le processus de substitution est maintenant terminé et vous pouvez ignorer l’étape suivante. Si vous avez plusieurs appareils inscrits auprès de votre service, passez à l’étape 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Étape 3 : Mettre à jour la clé de chiffrement sur les autres appareils StorSimple
Si vous avez plusieurs appareils inscrits auprès du service StorSimple Manager, cette procédure s’effectue dans l’interface Windows PowerShell de votre appareil StorSimple. La clé que vous avez obtenue à l’étape 2 doit être utilisée pour mettre à jour tous les appareils StorSimple restant inscrits auprès du service StorSimple Manager.

Procédez comme suit pour mettre à jour le chiffrement des données du service sur votre appareil.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Pour mettre à jour la clé de chiffrement des données de service sur des appareils physiques
1. Utilisez Windows PowerShell pour StorSimple pour vous connecter à la console. Sélectionnez Option 1 pour vous connecter avec un accès complet.
2. À l’invite de commandes, tapez : `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Fournir la clé de chiffrement de données de service que vous avez obtenue à l’[étape 2 : Utiliser Windows PowerShell pour StorSimple pour démarrer la modification de la clé de chiffrement des données du service](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Pour mettre à jour la clé de chiffrement des données de service sur toutes les appliances cloud 8010/8020
1. Téléchargez et installez le script PowerShell [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1). 
2. Ouvrez PowerShell et, à l’invite de commandes, tapez : `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ce script permet de garantir que la clé de chiffrement des données de service est définie sur toutes les appliances cloud 8010/8020 sous Device Manager.