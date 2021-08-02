---
title: Exécuter des diagnostics et collecter des journaux pour résoudre les problèmes liés aux appareils Azure Stack Edge | Microsoft Docs
description: Décrit comment exécuter des diagnostics et utiliser les journaux pour résoudre les problèmes liés aux appareils GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 82f8fe0574ec98c71ace2aaddda2d0bc2bc6e99f
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006344"
---
# <a name="run-diagnostics-collect-logs-to-troubleshoot-azure-stack-edge-device-issues"></a>Exécuter des diagnostics et collecter des journaux pour résoudre les problèmes liés aux appareils Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment exécuter des diagnostics, collecter un package de support, rassembler des journaux de sécurité avancée et consulter les journaux pour résoudre les problèmes de chargement et d’actualisation sur votre appareil Azure Stack Edge.


## <a name="run-diagnostics"></a>Exécuter les diagnostics

Pour diagnostiquer et corriger les erreurs d’appareil, vous pouvez exécuter les tests de diagnostics. Pour exécuter ces tests, procédez comme suit dans l’interface utilisateur web locale de votre appareil.

1. Dans l’interface utilisateur web locale, accédez à **Dépannage > Tests de diagnostic**. Sélectionnez le test que vous souhaitez exécuter, puis cliquez sur **Exécuter le test**. Le test diagnostique les éventuels problèmes dans vos paramètres de réseau, d’appareil, de proxy web, d’horodatage ou cloud. Vous êtes averti que des tests sont en cours d’exécution sur l’appareil.

    ![Sélectionner les tests ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Une fois les tests terminés, les résultats correspondants s’affichent. 

    ![Afficher les résultats des tests](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Si un test échoue, l’URL de l’action recommandée vous est présentée. Sélectionnez cette URL pour visualiser l’action recommandée.
 
    ![Passer en revue les avertissements des tests ayant échoué](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Collecter un package de support

Un package de journaux d’activité contient tous les journaux d’activité pertinents qui peuvent aider l’équipe du Support Microsoft à résoudre les problèmes des appareils. Vous pouvez générer un package de journaux par le biais de l’interface utilisateur web locale.

Pour collecter un package de support, procédez comme suit. 

1. Dans l’interface utilisateur web locale, accédez à **Dépannage > Support**. Sélectionnez **Créer un package de support**. Le système commence à collecter le package de support. La collection du package peut nécessiter plusieurs minutes.

    ![Sélectionner Ajouter un utilisateur](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Une fois le package de support créé, sélectionnez **Télécharger le package de support**. Un package compressé est téléchargé dans le chemin d’accès que vous avez choisi. Vous pouvez décompresser le package et visualiser les fichiers journaux système.

    ![Sélectionner Ajouter un utilisateur 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Collecter les journaux de sécurité avancée

Les journaux de sécurité avancée peuvent être des journaux d’intrusion logicielle ou d’intrusion matérielle pour votre appareil Azure Stack Edge Pro.

### <a name="software-intrusion-logs"></a>Journaux d’intrusion logicielle

L’intrusion logicielle ou les journaux de pare-feu par défaut sont collectés pour le trafic entrant et sortant. 

- Lorsque l’appareil est mis en image à l’usine, la journalisation par défaut du pare-feu est activée. Ces journaux sont regroupés par défaut dans le package de support lorsque vous créez ce type de package via l’interface utilisateur locale ou l’interface Windows PowerShell de l’appareil.

- Si seuls les journaux de pare-feu sont demandés dans le package de support pour vérifier une intrusion logicielle (NW) dans l’appareil, utilisez l’option `-Include FirewallLog` lors de la création du package de support. 

- Si aucune option d’inclusion particulière n’est fournie, le journal du pare-feu est inclus par défaut dans le package de support.

- Dans le package de support, le journal du pare-feu est le fichier `pfirewall.log`, il se trouve dans le dossier racine. Voici un exemple du journal d’intrusion logicielle de l’appareil Azure Stack Edge Pro. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Journaux d’intrusion matérielle

Pour détecter toute intrusion matérielle dans l’appareil, les événements de châssis, tels que l’ouverture ou la fermeture du châssis, sont consignés. 

- Le journal des événements système de l’appareil est lu à l’aide de l’applet de commande `racadm`. Ces événements sont ensuite filtrés pour les événements liés au châssis, dans un fichier `HWIntrusion.txt`.

- Pour récupérer uniquement le journal d’intrusion matérielle dans le package de support, utilisez l’option `-Include HWSelLog` lors de la création du package de support. 

- Si aucune option d’inclusion particulière n’est fournie, le journal d’intrusion matérielle est inclus par défaut dans le package de support.

- Dans le package de support, le journal d’intrusion matérielle est le fichier `HWIntrusion.txt`, il se trouve dans le dossier racine. Voici un exemple du journal d’intrusion matérielle de l’appareil Azure Stack Edge Pro. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="troubleshoot-device-upload-and-refresh-errors"></a>Résoudre les erreurs de chargement et d’actualisation des appareils

Toutes les erreurs rencontrées lors des processus de chargement et d’actualisation figurent dans les fichiers d’erreurs correspondants.

1. Pour visualiser les fichiers d’erreurs, accédez à votre partage, puis sélectionnez-le pour afficher le contenu. 


2. Sélectionnez le _dossier Microsoft Data Box Edge_. Ce dossier comporte deux sous-dossiers :

    - dossier Upload contenant les fichiers journaux des erreurs de chargement ;
    - dossier Refresh stockant les fichiers journaux des erreurs d’actualisation.

    Voici un exemple de fichier journal d’actualisation.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Lorsque ce fichier signale une erreur (présentée dans cet exemple), notez le code de l’erreur, 16001 dans le cas présent. Recherchez ci-après la description de ce code d’erreur en fonction de la référence de l’erreur.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes d’activation d’appareil](azure-stack-edge-gpu-troubleshoot-activation.md).
- [Résoudre les problèmes liés à Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md).
- [Résoudre les problèmes liés à Stockage Blob](azure-stack-edge-gpu-troubleshoot-blob-storage.md).
- [Résoudre les problèmes de calcul dans IoT Edge](azure-stack-edge-gpu-troubleshoot-iot-edge.md).