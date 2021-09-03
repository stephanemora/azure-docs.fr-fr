---
title: Résoudre les problèmes liés à la configuration d'Azure Resource Manager sur Azure Stack Edge Pro GPU | Microsoft Docs
description: Explique comment résoudre les problèmes liés à la configuration d'Azure Resource Manager sur les appareils Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: d8226d780c3caabb94f0780f3a4296765207f1b1
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987808"
---
# <a name="troubleshoot-azure-resource-manager-issues-on-an-azure-stack-edge-device"></a>Résoudre les problèmes liés à Azure Resource Manager sur un appareil Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment résoudre les problèmes liés à Azure Resource Manager qui peuvent interférer avec la gestion des ressources sur votre appareil Azure Stack Edge. Azure Resource Manager fournit une couche de gestion qui vous permet de créer, de mettre à jour et de supprimer des ressources sur votre compte Azure.
 
## <a name="azure-resource-manager-configuration-errors"></a>Erreurs de configuration d'Azure Resource Manager

Les erreurs suivantes peuvent révéler un problème au niveau de la configuration d'Azure Resource Manager. 

| **Problème / Erreurs** |  **Résolution :** | 
|------------|-----------------|
|Problèmes d’ordre général|<li>[Vérifiez que l'appareil est correctement configuré](#verify-the-device-is-configured-properly).<li> [Vérifiez que le client est correctement configuré.](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment : Une erreur s’est produite lors de l’envoi de la requête.<br>À la ligne : 1 caractère : 1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|Votre appareil n'est pas accessible ou n'est pas correctement configuré. Vérifiez que l'appareil et le client sont correctement configurés. Pour obtenir de l’aide, consultez la ligne **Problèmes d’ordre général** dans ce tableau.|
|Le service a retourné une erreur. Pour plus d’informations, consultez InnerException : Le serveur a clos la connexion sous-jacente : Impossible d’établir une relation de confiance pour le canal sécurisé SSL/TLS. |  Une erreur s'est produite lors de la création et de l'installation du certificat sur votre appareil. Pour plus d'informations, consultez [Créer et installer des certificats](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates). |
|L’opération a retourné un code d’état non valide : « ServiceUnavailable ». <br> Response status code does not indicate success: 503 (Service indisponible). | Les causes possibles de cette erreur sont décrites ci-dessous :<li>ArmStsPool est à l’état d’arrêt.</li><li>Azure Resource Manager ou le site web du service d'émission de jeton de sécurité est hors service.</li><li>La ressource de cluster Azure Resource Manager est inactive.</li><br>Le redémarrage de l'appareil peut permettre de résoudre le problème. Pour un débogage approfondi, [collectez un package de support](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).|
|AADSTS50126 : Nom d’utilisateur ou mot de passe non valide.<br>ID de suivi : 29317da9-52fc-4ba0-9778-446ae5625e5a<br>ID de corrélation : 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Timestamp : 2019-11-15 09:21:57Z : Le serveur distant a retourné une erreur : (400) Requête incorrecte.<br>À la ligne : 1 caractère : 1 |Les causes possibles de cette erreur sont décrites ci-dessous :<li>En cas de nom d'utilisateur et de mot de passe invalides, vérifiez que vous avez [réinitialisé le mot de passe Azure Storage Manager à partir du portail Azure](./azure-stack-edge-gpu-set-azure-resource-manager-password.md), puis utilisez le mot de passe approprié.<li>En cas d'ID de locataire non valide, vérifiez que celui-ci est défini sur `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|connect-AzureRmAccount : AADSTS90056 : La ressource est désactivée ou n’existe pas. Vérifiez le code de votre application pour vous assurer que vous avez spécifié l’URL de ressource exacte pour la ressource à laquelle vous essayez d’accéder.<br>ID de trace : e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>ID de corrélation : 75c8ef5a-830e-48b5-b039-595a96488ff9 Timestamp : 2019-11-18 07:00:51Z : Le serveur distant a retourné une erreur : (400) Incorrect |Les points de terminaison Azure Resource Manager utilisés dans la commande `Add-AzureRmEnvironment` sont incorrects.<br>Pour rechercher les points de terminaison Azure Resource Manager, cochez **Points de terminaison des appareils** sur la page **Appareil** de l'interface utilisateur web locale de votre appareil.<br>Pour obtenir les instructions PowerShell, consultez [Configurer l'environnement Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md#step-7-set-azure-resource-manager-environment). |
|Impossible d’accéder aux points de terminaison à partir du cloud.<br>Vérifiez que vous disposez d'une connexion réseau. Détail de l’erreur : HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005) : Nombre maximal de nouvelles tentatives dépassé avec l’URL :/metadata/endpoints?api-version=2015-01-01 (Provoquée par SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |Cette erreur apparaît principalement dans un environnement Mac ou Linux. L'erreur se produit parce qu'un certificat au format PEM est absent du magasin de certificats Python. |


## <a name="troubleshoot-general-issues-with-azure-resource-manager"></a>Résoudre les problèmes généraux liés à Azure Resource Manager

Pour les problèmes généraux liés à Azure Resource Manager, assurez-vous que votre appareil et le client sont correctement configurés. Pour accéder aux procédures de bout en bout, consultez [Se connecter à Azure Resource Manager sur un appareil Azure Stack Edge Pro GPU](azure-stack-edge-gpu-connect-resource-manager.md).


### <a name="verify-the-device-is-configured-properly"></a>Vérifier que l’appareil est configuré correctement

1. À partir de l’interface utilisateur locale, vérifiez que le réseau de l’appareil est correctement configuré.

2. [Vérifiez que les certificats sont à jour pour tous les points de terminaison](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

3. Récupérez le point de terminaison de gestion et de connexion d’Azure Resource Manager dans la page **Appareil** de l’interface utilisateur locale.

4. Vérifiez que l’appareil est activé et inscrit dans Azure.


### <a name="verify-the-client-is-configured-properly"></a>Vérifier que le client est configuré correctement

1. [Vérifiez que la bonne version de PowerShell est installée](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client).

2. [Vérifiez que les modules PowerShell adéquats sont installés](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client).

3. Vérifiez que les points de terminaison d’Azure Resource Manager et ceux de connexion sont accessibles. Vous pouvez essayer d’effectuer un test ping sur les points de terminaison. Par exemple :

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   S'ils ne sont pas joignables, [ajoutez des entrées de fichier DNS / hôte](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. [Vérifiez que les certificats clients sont installés](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell).

5. Si vous utilisez PowerShell, activez la préférence de débogage pour afficher les messages détaillés en exécutant la commande PowerShell suivante : 

    `$debugpreference = "continue"`


## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes d’activation d’appareil](azure-stack-edge-gpu-troubleshoot-activation.md).
- [Résolution des problèmes de l’appareil](azure-stack-edge-gpu-troubleshoot.md)