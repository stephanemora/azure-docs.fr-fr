---
title: Codes d’erreur du client pour Device Update pour Azure IoT Hub | Microsoft Docs
description: Ce document fournit un tableau des codes d’erreur du client pour divers composants Device Update.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200337"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Codes d’erreur de Device Update pour IoT Hub

Ce document fournit un tableau des codes d’erreur pour divers composants Device Update. Celui-ci est destiné à être utilisé comme référence pour les utilisateurs qui souhaitent essayer d’analyser leurs propres codes d’erreur afin de diagnostiquer et de résoudre les problèmes.

Il existe deux principaux composants côté client qui peuvent générer des codes d’erreur : l’agent Device Update et l’agent d’optimisation de la distribution.

## <a name="device-update-agent"></a>Agent Device Update

### <a name="resultcode-and-extendedresultcode"></a>ResultCode et ExtendedResultCode

L’interface PnP principale de Device Update pour IoT Hub signale `ResultCode` et `ExtendedResultCode`, qui peuvent être utilisés pour diagnostiquer les défaillances. [En savoir plus](device-update-plug-and-play.md) sur l’interface PnP principale de Device Update.

#### <a name="resultcode"></a>ResultCode

`ResultCode` est un code d’état général qui suit la convention des codes d’état http.
[Découvrez-en davantage](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) sur les codes d’état http.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` est un entier accompagné d’informations d’erreur encodées.

Vous verrez probablement le code `ExtendedResultCode` en tant qu’entier signé dans l’interface PnP. Pour décoder le code `ExtendedResultCode`, convertissez l’entier signé en valeur hexadécimale non signée. Seuls les 4 premiers octets du code `ExtendedResultCode` sont utilisés et sont de la forme `F` `FFFFFFF` où le premier quartet est le **code d’installation** et le reste des bits correspondent au **code d’erreur**.

**Codes d’installation**

| Code d’installation     | Description  |
|-------------------|--------------|
| D                 | Erreur générée à partir du kit SDK DO|
| E                 | Le code d’erreur est un numéro errno |


Par exemple :

`ExtendedResultCode` a la valeur `-536870781`.

La représentation hexadécimale non signée de `-536870781` est `FFFFFFFF E0000083`.

| Ignorer    | Code d’installation  | Code d'erreur   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` en base hexadécimale est `131` en base décimale et représente la valeur errno pour `ENOLCK`.

## <a name="delivery-optimization-agent"></a>Agent d’optimisation de la distribution
Le tableau suivant répertorie les codes d’erreur relatifs au composant d’optimisation de la distribution (DO) du client Device Update. Le composant DO est chargé de télécharger le contenu de mise à jour sur l’appareil IoT.

Le code d’erreur DO peut être obtenu en examinant les exceptions levées en réponse à un appel d’API. Tous les codes d'erreur DO peuvent être identifiés par le préfixe 0x80D0.

| Code d'erreur  | Erreur de chaîne                       | Type                 | Description |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | n/a                  | L’optimisation de la distribution n’a pas pu fournir le service |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Télécharger le travail         | Le téléchargement d’un fichier n’a pas progressé dans la période définie |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Télécharger le travail         | Méthode SetProperty() ou GetProperty() appelée avec un ID de propriété inconnu |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Télécharger le travail         | Impossible d’appeler la méthode SetProperty() sur une propriété en lecture seule |
| 0x80D02013L | DO_E_INVALID_STATE                 | Télécharger le travail         | L’opération demandée n’est pas autorisée dans l’état actuel du travail. Le travail a peut-être été annulé ou a terminé son transfert. Il est maintenant en lecture seule. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Télécharger le travail         | Impossible de démarrer un téléchargement, car aucun récepteur de téléchargement (fichier local ou interface de flux) n’a été spécifié |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | Interface IDODownload| Le téléchargement a été démarré sans fournir d’URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Conditions transitoires | Téléchargement suspendu en raison d’une perte de connectivité réseau |

## <a name="device-update-content-service"></a>Service de contenu Device Update
Le tableau suivant liste les codes d’erreur relatifs au composant de service de contenu du service Device Update. Le composant de service de contenu est responsable de la gestion de l’importation du contenu de mise à jour.

| Code d'erreur                    | Erreur de chaîne                                                               | Étapes suivantes                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| « UpdateAlreadyExists »         | Une mise à jour avec la même identité existe déjà.                              | Veillez à importer une mise à jour qui n’a pas encore été importée dans cette instance Device Update pour IoT Hub. |
| « DuplicateContentImport »      | Contenu identique importé simultanément plusieurs fois.                  | Identique pour UpdateAlreadyExists. |
| « CannotProcessImportManifest » | Erreur de traitement du manifeste d’importation.                                          | Consultez la documentation sur l’[importation des concepts](./import-concepts.md) et l’[importation des mises à jour](./import-update.md) pour connaître le formatage correct du manifeste d’importation. |
| « CannotDownload »              | Impossible de télécharger le manifeste d’importation.                                           | Vérifiez que l’URL du fichier manifeste d’importation est toujours valide. |
| « CannotParse »                 | Impossible d’analyser le manifeste d’importation.                                              | Vérifiez la précision de votre manifeste d’importation par rapport au schéma défini dans la documentation sur l’[importation des mises à jour](./import-update.md). |
| « UnsupportedVersion »          | La version du schéma du manifeste d’importation n’est pas prise en charge.                           | Veillez à ce que votre manifeste d’importation utilise le dernier schéma défini dans la documentation sur l’[importation des mises à jour](./import-update.md). |
| « UpdateLimitExceeded »         | Erreur d’importation de mise à jour en raison d’une limite dépassée.                              | Vous avez atteint une limite quant au nombre de fournisseurs, de noms ou de versions différents autorisés dans votre instance Device Update pour IoT Hub. Supprimez certaines mises à jour de votre instance et réessayez. |
| « UpdateProvider »              | Impossible d’importer un nouveau fournisseur de mise à jour.                                       | Vous avez atteint une limite quant au nombre de __fournisseurs__ différents autorisés dans votre instance Device Update pour IoT Hub. Supprimez certaines mises à jour de votre instance et réessayez. |
| « UpdateName »                  | Impossible d’importer un nouveau nom de mise à jour pour le fournisseur spécifié.                | Vous avez atteint une limite quant au nombre de __noms__ différents autorisés sous un fournisseur dans votre instance Device Update pour IoT Hub. Supprimez certaines mises à jour de votre instance et réessayez. |
| « UpdateVersion »               | Impossible d’importer une nouvelle version de mise à jour pour le fournisseur et le nom spécifiés.    | Vous avez atteint une limite quant au nombre de __versions__ différentes autorisées sous un fournisseur et un nom dans votre instance Device Update pour IoT Hub. Supprimez de votre instance certaines mises à jour avec ce nom, puis réessayez. |
| « UpdateProviderCompatibility » | Impossible d’importer un fournisseur de mise à jour supplémentaire avec la compatibilité spécifiée. | Lors de la définition des propriétés de compatibilité du fabricant de l’appareil et du modèle d’appareil dans un manifeste d’importation, gardez à l’esprit que Device Update pour IoT Hub prend en charge une combinaison unique de fournisseur et de nom pour un fabricant/modèle donné. Cela signifie que si vous essayez d’utiliser les mêmes propriétés de compatibilité de fabricant/modèle avec plusieurs combinaisons de fournisseur/nom, vous verrez ces erreurs. Pour résoudre cela, veillez à ce que toutes les mises à jour pour un appareil donné (telles que définies par fabricant/modèle) utilisent le même fournisseur et le même nom. Bien que cela ne soit pas obligatoire, vous pouvez envisager de définir un fournisseur identique au fabricant et un nom identique au modèle, pour des raisons de simplicité. |
| « UpdateNameCompatibility »     | Impossible d’importer un nom de mise à jour supplémentaire avec la compatibilité spécifiée.     | Identique à UpdateProviderCompatibility.ContentLimitNamespaceCompatibility. |
| « UpdateVersionCompatibility »  | Impossible d’importer une version de mise à jour supplémentaire avec la compatibilité spécifiée.  | Identique à UpdateProviderCompatibility.ContentLimitNamespaceCompatibility. |
| « CannotProcessUpdateFile »     | Erreur lors du traitement du fichier source.                                              |                                    |
| « ContentFileCannotDownload »   | Impossible de télécharger le fichier source.                                               | Vérifiez que l’URL du ou des fichiers de mise à jour est toujours valide. |

**[Étape suivante : Résoudre les problèmes liés à Device Update](.\troubleshoot-device-update.md)**
