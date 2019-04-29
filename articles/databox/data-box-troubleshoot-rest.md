---
title: Azure Data Box dépannage pour l’utilisation de l’interface REST | Microsoft Docs
description: Décrit comment résoudre les problèmes rencontrés dans Azure Data Box lorsque la copie des données est via l’interface REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: ee2820d78e95924e09a0219753f87d6910c0e736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782954"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Résoudre les problèmes liés au stockage Blob de zone de données Azure

Ces informations de détails de l’article sur la façon de résoudre les problèmes que vous pouvez voir lorsque vous utilisez le stockage d’objets Blob de zone de données via l’interface REST dans votre boîte de données pour copier des données. Surface de ces problèmes lorsque vous utilisez le stockage d’objets Blob de zone de données avec d’autres applications ou les bibliothèques clientes telles que de la bibliothèque de l’Explorateur de stockage Azure, AzCopy ou stockage Azure pour Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Erreurs rencontrées dans l’Explorateur de stockage Azure

Cette section décrit en détail certains des problèmes rencontrés lors de l’utilisation de l’Explorateur de stockage Azure avec stockage d’objets Blob de zone de données.

|Message d’erreur  |Action recommandée |
|---------|---------|
|Impossible de récupérer les ressources enfants. La valeur d’un des en-têtes HTTP n’est pas dans le format correct.|À partir de la **modifier** menu, sélectionnez **API cible Azure Stack**. <br>Redémarrez l’Explorateur de stockage Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Vérifiez que le nom de point de terminaison `<accountname>.blob.<serialnumber>.microsoftdatabox.com` est ajouté au fichier hosts sur ce chemin d’accès : <li>`C:\Windows\System32\drivers\etc\hosts` sur Windows, ou </li><li> `/etc/hosts` sur Linux.</li>|
|Impossible de récupérer les ressources enfants. <br>Détails : un certificat auto-signé |Importer le certificat SSL pour votre appareil dans l’Explorateur de stockage Azure : <li>Téléchargez le certificat à partir du portail Azure. Pour plus d’informations, accédez à [télécharger le certificat](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>À partir de la **modifier** menu, sélectionnez **certificats SSL** , puis sélectionnez **importer les certificats**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Erreurs rencontrées dans AzCopy pour Windows

Cette section décrit en détail certains des problèmes rencontrés lors de l’utilisation d’AzCopy pour Windows avec le stockage Blob de zone de données.

|Message d’erreur  |Action recommandée |
|---------|---------|
|Commande AzCopy se bloque pendant une minute avant d’afficher cette erreur : <br>Impossible d’énumérer directory https://... Le nom distant n’a pas pu être résolu. `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Vérifiez que le nom de point de terminaison `<accountname>.blob.<serialnumber>.microsoftdatabox.com` est ajouté au fichier hosts sur : `C:\Windows\System32\drivers\etc\hosts`.|
|Commande AzCopy se bloque pendant une minute avant d’afficher cette erreur : <br>Emplacement source analyse des erreurs. La connexion sous-jacente a été fermée : N’a pas pu établir relation d’approbation pour le canal sécurisé SSL/TLS.|Importer le certificat SSL pour votre appareil dans le magasin de certificats du système. Pour plus d’informations, accédez à [télécharger le certificat](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Erreurs rencontrées dans AzCopy pour Linux

Cette section décrit en détail certains des problèmes rencontrés lors de l’utilisation d’AzCopy pour Linux avec stockage d’objets Blob de zone de données.

|Message d’erreur  |Action recommandée |
|---------|---------|
|Commande AzCopy se bloque pendant 20 minutes avant d’afficher cette erreur : <br>Erreur d’analyse d’emplacement source `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Aucun périphérique ou adresse|Vérifiez que le nom de point de terminaison `<accountname>.blob.<serialnumber>.microsoftdatabox.com` est ajouté au fichier hosts sur : `/etc/hosts`.|
|Commande AzCopy se bloque pendant 20 minutes avant d’afficher cette erreur : <br>Emplacement source de l’analyse erreur... La connexion SSL n’a pas pu être établie.|Importer le certificat SSL pour votre appareil dans le magasin de certificats du système. Pour plus d’informations, accédez à [télécharger le certificat](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Erreurs détectées dans la bibliothèque de stockage Azure pour Python

Cette section détaille certains des principaux problèmes rencontrés lors du déploiement de Data Box Disk en cas d’utilisation d’un client Linux pour la copie de données.

|Message d’erreur  |Action recommandée |
|---------|---------|
|La valeur d’un des en-têtes HTTP n’est pas dans le format correct. |La version installée de la bibliothèque de stockage Microsoft Azure pour Python n’est pas pris en charge par Data Box. Consultez les exigences de stockage Blob de zone de données Azure pour les versions prises en charge.|
|… [SSL : CERTIFICATE_VERIFY_FAILED] …|Avant d’exécuter Python, définissez la variable d’environnement REQUESTS_CA_BUNDLE pour le chemin d’accès du fichier de certificat SSL codée en Base64 (voir comment [télécharger le certificat]()). <br>Par exemple : <br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Vous pouvez également ajouter le certificat au magasin de certificats du système et puis définissez cette variable d’environnement pour le chemin d’accès de ce magasin. <br> Par exemple, sur Ubuntu : <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Erreurs courantes

Ces erreurs ne sont pas spécifiques à n’importe quelle application.

|Message d’erreur  |Action recommandée |
|---------|---------|
|La connexion expire. |Connectez-vous à l’appareil Data Box et vérifiez qu’il est déverrouillé. Chaque fois que le redémarrage de l’appareil, il reste verrouillé jusqu'à ce qu’un utilisateur est connecté.|

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [configuration système requise du stockage Blob de données boîte](data-box-system-requirements-rest.md).
