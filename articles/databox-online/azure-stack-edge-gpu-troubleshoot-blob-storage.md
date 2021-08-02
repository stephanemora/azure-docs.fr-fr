---
title: Résoudre les problèmes liés au stockage d’objets blob pour Azure Stack Edge Pro GPU | Microsoft Docs
description: Décrit comment résoudre les problèmes liés au stockage d’objets blob pour un appareil Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: b54fcdc25e6278aa4e98eebde948b5f67525d080
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987814"
---
# <a name="troubleshoot-blob-storage-issues-for-an-azure-stack-edge-device"></a>Résoudre les problèmes de stockage d’objets blob pour un appareil Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment résoudre les problèmes liés au stockage d’objets blob pour un appareil Azure Stack Edge. 

## <a name="errors-for-blob-storage-on-device"></a>Erreurs de stockage d’objets blob sur l’appareil 

Voici les erreurs liées au stockage d’objets blob sur un appareil Azure Stack Edge.

| **Problème / Erreurs** |  **Résolution :** | 
|--------------------|-----------------|
|Unable to retrieve child resources. (Impossible de récupérer des ressources enfants.) The value for one of the HTTP headers is not in the correct format. (Le format de la valeur d’un des en-têtes HTTP est incorrect.)| Dans le menu **Modifier**, sélectionnez **Target Azure Stack APIs** (Cibler des API Azure Stack). Ensuite, redémarrez l’Explorateur Stockage Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Vérifiez que le nom de point de terminaison `<accountname>.blob.<serialnumber>.microsoftdatabox.com` est ajouté au fichier hosts sur ce chemin : `C:\Windows\System32\drivers\etc\hosts` sur Windows, ou `/etc/hosts` sur Linux.|
|Unable to retrieve child resources. (Impossible de récupérer des ressources enfants.)<br> Détails : certificat auto-signé |Importez le certificat SSL pour votre appareil dans l’Explorateur Stockage Azure : <ol><li>[Générez et téléchargez le certificat](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>Dans le menu **Modifier**, sélectionnez Certificats SSL, puis sélectionnez **Importer les certificats**.</li></ol>|
|La commande AzCopy semble ne plus répondre pendant une minute avant d’afficher cette erreur :<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Vérifiez que le nom de point de terminaison `<accountname>.blob.<serialnumber>.microsoftdatabox.com` est ajouté au fichier hosts sur : `C:\Windows\System32\drivers\etc\hosts`.|
|La commande AzCopy semble ne plus répondre pendant une minute avant d’afficher cette erreur :<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importez le certificat SSL pour votre appareil dans l’Explorateur Stockage Azure : <ol><li>[Générez et téléchargez le certificat](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>Dans le menu **Modifier**, sélectionnez Certificats SSL, puis sélectionnez **Importer les certificats**.</li></ol>|
|La commande AzCopy semble ne plus répondre pendant 20 minutes avant d’afficher cette erreur :<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Vérifiez que le nom de point de terminaison `<accountname>.blob.<serialnumber>.microsoftdatabox.com` est ajouté au fichier hosts sur : `/etc/hosts`.|
|La commande AzCopy semble ne plus répondre pendant 20 minutes avant d’afficher cette erreur :<br>`Error parsing source location… The SSL connection could not be established`. |Importez le certificat SSL pour votre appareil dans l’Explorateur Stockage Azure : <ol><li>[Générez et téléchargez le certificat](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>Dans le menu **Modifier**, sélectionnez Certificats SSL, puis sélectionnez **Importer les certificats**.</li></ol>|
|La commande AzCopy semble ne plus répondre pendant 20 minutes avant d’afficher cette erreur :<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Vérifiez que le nom de point de terminaison `<accountname>.blob.<serialnumber>.microsoftdatabox.com` est ajouté au fichier hosts sur : `/etc/hosts`.|
|La commande AzCopy semble ne plus répondre pendant 20 minutes avant d’afficher cette erreur : `Error parsing source location… The SSL connection could not be established`.|Importez le certificat SSL pour votre appareil dans le magasin de certificats du système. Pour plus d’informations, consultez [Télécharger le certificat](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|The value for one of the HTTP headers is not in the correct format. (Le format de la valeur d’un des en-têtes HTTP est incorrect.)|La version installée de la bibliothèque Stockage Microsoft Azure pour Python n’est pas prise en charge par Azure Stack Edge. Pour connaître les versions de bibliothèques prises en charge, consultez [Bibliothèques clientes Azure prises en charge](azure-stack-edge-gpu-system-requirements-rest.md#supported-azure-client-libraries).|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …| Avant d’exécuter Python, définissez la variable d’environnement REQUESTS_CA_BUNDLE sur le chemin d’accès du fichier de certificat SSL codé en Base64 (consultez comment [Télécharger le certificat](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)). Par exemple, exécutez :<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Vous pouvez également ajouter le certificat au magasin de certificats du système, puis définir cette variable d’environnement sur le chemin de ce magasin. Par exemple, sur Ubuntu, exécutez :<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`|
|The connection times out. (La connexion arrive à expiration.)|Connectez-vous à votre appareil, puis vérifiez qu’il est déverrouillé. Chaque fois que l’appareil redémarre, il reste verrouillé jusqu’à ce qu’un utilisateur se connecte.|


## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les erreurs de chargement et d’actualisation des appareils](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-device-upload-and-refresh-errors).
