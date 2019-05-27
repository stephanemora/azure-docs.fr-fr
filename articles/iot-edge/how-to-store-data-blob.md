---
title: Stocker des objets blob de blocs sur des appareils - Azure IoT Edge | Microsoft Docs
description: Comprendre les fonctionnalités de hiérarchisation et time-to-live, consultez les opérations de stockage blob pris en charge et vous connecter à votre compte de stockage d’objets blob.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991468"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Stocker des données à la périphérie avec le Stockage Blob Azure sur IoT Edge (préversion)

Le Stockage Blob Azure sur IoT Edge fournit une solution de stockage des [objets blob de blocs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) en périphérie. Un module de stockage d’objets blob sur votre appareil IoT Edge se comporte comme un service d’objets blob de blocs Azure, à ceci près que ces derniers sont stockés localement sur votre appareil IoT Edge. Vous pouvez accéder à vos objets blob avec les mêmes méthodes que celles du kit SDK de stockage Azure, ou aux appels d’API objets blob de blocs auxquels vous êtes déjà habitué.

> [!NOTE]
> Le Stockage Blob Azure sur IoT Edge est disponible en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce module est fourni avec **la hiérarchisation** et **time-to-live** fonctionnalités.

> [!NOTE]
> Actuellement la hiérarchisation et la fonctionnalité time-to-live sont uniquement disponibles dans Linux AMD64 et Linux ARM32.

**La hiérarchisation** est une fonctionnalité configurable, ce qui vous permet de télécharger automatiquement les données à partir de votre stockage blob local vers Azure avec prise en charge de connectivité internet intermittentes. Il vous permet de :

- On/off activer la fonctionnalité de hiérarchisation
- Choisissez l’ordre dans lequel les données sont copiées vers Azure comme NewestFirst ou OldestFirst
- Spécifiez le compte de stockage Azure auquel vous souhaitez que vos données sont téléchargées.
- Spécifiez les conteneurs que vous voulez télécharger dans Azure. Ce module vous permet de spécifier des noms de conteneur source et cible.
- Complète la hiérarchisation des objets blob (à l’aide de `Put Blob` opération) et bloquer la hiérarchisation au niveau (à l’aide de `Put Block` et `Put Block List` operations).

Ce module utilise bloc niveau la hiérarchisation, lorsque votre objet blob se compose de blocs. Voici quelques-uns des scénarios courants :

- Votre application met à jour des blocs d’un objet blob précédemment téléchargé, ce module télécharge uniquement les blocs de mise à jour et pas l’objet blob entier.
- Le module est téléchargement d’objet blob et de connexion internet disparaît, lorsque la connectivité est de retour à nouveau qu’il télécharge uniquement les blocs restants et pas l’objet blob entier.

Si un arrêt inattendu du processus (par exemple, la panne de courant) se produit pendant le chargement d’un objet blob, tous les blocs qui ont été dus pour le téléchargement seront téléchargés à nouveau, lorsque le module repasse en ligne.

**Time-to-live** (TTL) est une fonctionnalité configurable où le module supprime automatiquement vos objets BLOB à partir du stockage local lorsque la durée spécifiée (exprimée en minutes) expire. Durée de vie vous permet de :

- On/off activer la fonctionnalité de hiérarchisation
- Spécifiez la durée de vie en minutes

Les scénarios où les données comme des vidéos, des images, des données de finance, données de l’hôpital ou toutes les données qui doivent être stockées localement, plus loin et qui peut être traitées localement ou transférées vers le cloud sont de bons exemples d’utiliser ce module.

Cet article fournit des instructions pour le déploiement d’un stockage Blob Azure sur le conteneur IoT Edge qui exécute un service d’objets blob sur votre appareil IoT Edge.

> [!NOTE]
> Les termes « la hiérarchisation automatique » et « expiration automatique » utilisé dans la vidéo ont été remplacées par « hiérarchisation » et « time-to-live ».

Regardez la vidéo de présentation rapide
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Conditions préalables

Un appareil Azure IoT Edge :

- Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil IoT Edge en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou [les appareils Windows](quickstart.md).

- Le module de stockage Blob Azure sur IoT Edge prend en charge les configurations d’appareil suivantes :

  | Système d’exploitation | Architecture |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Standard (mise à jour d’octobre) | AMD64 |
  | Windows 10 IoT Enterprise (mise à jour d’octobre) | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

Ressources cloud :

Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure.

## <a name="tiering-and-time-to-live-properties"></a>Propriétés de la hiérarchisation et time-to-live

Utilisez les propriétés souhaitées pour définir la hiérarchisation et propriétés time-to-live. Ils peuvent être définies pendant le déploiement ou modifiées ultérieurement en modifiant la représentation de module sans avoir à redéployer. Nous recommandons de vérifier la représentation de « Module » pour `reported configuration` et `configurationValidation` s’assurer que les valeurs sont correctement propagées.

### <a name="tiering-properties"></a>Propriétés de la hiérarchisation

Le nom de ce paramètre est `tieringSettings`

| Champ | Valeurs possibles | Explication |
| ----- | ----- | ---- |
| tieringOn | true, false | Par défaut, il est défini sur `false`, si vous souhaitez l’activer sur affectez-lui la valeur `true`|
| backlogPolicy | NewestFirst, OldestFirst | Vous permet de choisir l’ordre dans lequel les données sont copiées vers Azure. Par défaut, il est défini sur `OldestFirst`. L’ordre est déterminé par l’heure de dernière modification d’objet Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` une chaîne de connexion qui vous permet de spécifier le compte de stockage Azure auquel vous souhaitez que vos données est téléchargée. Spécifiez `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Ajouter EndpointSuffix approprié de Azure où les données sont chargées, elle varie selon globale d’Azure Government Azure et Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}` <br><br> `"<source container name1>": {"target": "%d-%c"}` | Vous permet de spécifier les noms de conteneur que vous voulez télécharger dans Azure. Ce module vous permet de spécifier des noms de conteneur source et cible. Si vous ne spécifiez pas le nom du conteneur cible, il affecte automatiquement le nom du conteneur en tant que `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Vous pouvez créer des chaînes de modèle pour le nom du conteneur cible, consultez la colonne de valeurs possibles. <br>* %h -> nom du Hub IoT (3 et 50 caractères). <br>* %d -> ID d’appareil IoT (1 à 129 caractères). <br>* %m -> nom du module (1 à 64 caractères). <br>* %c -> nom du conteneur source (3 à 63 caractères). <br><br>Taille maximale du nom du conteneur est 63 caractères, tout en attribuer automatiquement le nom du conteneur cible si la taille du conteneur dépasse 63 caractères, qu'il va réduire chaque section (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) et 15 caractères. |

### <a name="time-to-live-properties"></a>Propriétés de Time-to-live

Le nom de ce paramètre est `ttlSettings`

| Champ | Valeurs possibles | Explication |
| ----- | ----- | ---- |
| ttlOn | true, false | Par défaut, il est défini sur `false`, si vous souhaitez l’activer sur affectez-lui la valeur `true`|
| timeToLiveInMinutes | `<minutes>` | Spécifiez la durée de vie en minutes. Le module supprime automatiquement vos objets BLOB à partir du stockage local lors de la durée de vie expire |

## <a name="configure-log-files"></a>Configurer des fichiers journaux

Pour plus d’informations sur la configuration des fichiers journaux pour votre module, consultez ces [meilleures pratiques de production](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Se connecter au module de stockage d’objets blob

Vous pouvez utiliser le nom du compte et la clé de compte que vous avez configurés pour que votre module accède au stockage d’objets blob sur votre appareil IoT Edge.

Spécifiez votre appareil IoT Edge en tant que point de terminaison d’objets blob pour toutes les demandes de stockage que vous lui adressez. Vous pouvez [Créer une chaîne de connexion pour un point de terminaison de stockage explicite](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) en utilisant les informations de l’appareil IoT Edge et le nom du compte que vous avez configuré.

- Pour les modules qui sont déployés sur le même périphérique qu’où le stockage d’objets Blob Azure sur le module IoT Edge est en cours d’exécution, le point de terminaison d’objet blob est : `http://<module name>:11002/<account name>`.
- Pour les modules qui sont déployés sur un appareil différent de celui où le stockage d’objets Blob Azure sur le module IoT Edge est en cours d’exécution, selon votre configuration de l’objet blob de point de terminaison fait partie de :
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Exemples de démarrage rapide de stockage d’objets Blob Azure

La documentation du stockage Blob Azure comprend des guides de démarrage rapide qui fournissent des exemples de code dans différents langages. Vous pouvez exécuter ces exemples pour tester le stockage d’objets Blob Azure IoT Edge en changeant le point de terminaison d’objet blob pour vous connecter à votre module de stockage d’objets blob.

Les guides de démarrage rapide suivants utilisent des langages qui sont également pris en charge par IoT Edge, vous pouvez donc les déployer en tant que modules IoT Edge en même temps que le module de stockage d’objets blob :

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Se connecter à votre stockage local avec l’Explorateur de stockage Azure

Vous pouvez utiliser **Azure Storage Explorer** pour vous connecter à votre compte de stockage local. Cette option est disponible uniquement avec [Explorateur de stockage Azure version 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> Vous pouvez rencontrer des erreurs lors de l’exécution des étapes suivantes, telles que l’ajout d’une connexion à un compte de stockage local, ou créer des conteneurs dans le compte de stockage local. Veuillez ignorer et actualiser.

1. Téléchargez et installez l’Explorateur de stockage Azure

1. Se connecter au stockage Azure à l’aide d’une chaîne de connexion

1. Fournir la chaîne de connexion : `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Parcourez les étapes pour vous connecter.

1. Créer le conteneur à l’intérieur de votre compte de stockage local

1. Démarrer le téléchargement de fichiers en tant qu’objets BLOB de blocs.
   > [!NOTE]
   > Ce module ne prend pas en charge les objets BLOB de pages.

1. Vous pouvez choisir de se connecter à vos comptes de stockage Azure où vous chargez les données. Il vous donne une vue unique pour votre compte de stockage local et le compte de stockage Azure

## <a name="supported-storage-operations"></a>Opérations de stockage prises en charge

Modules de stockage d’objets BLOB sur IoT Edge utilisent les mêmes SDK de stockage Azure et sont cohérentes avec la version 2017-04-17 de l’API de stockage Azure pour les points de terminaison blob de bloc. Les versions ultérieures dépendent des besoins des clients.

Étant donné que toutes les opérations de stockage d’objets Blob Azure sont pris en charge par le stockage Blob Azure sur IoT Edge, cette section répertorie l’état de chacun d’eux.

### <a name="account"></a>Compte

Prises en charge :

- Répertorier les conteneurs

Non prises en charge :

- Obtenir et définir les propriétés du service BLOB
- Demande d’objet blob préliminaire
- Obtenir les statistiques du service BLOB
- Obtenir les informations de compte

### <a name="containers"></a>Containers

Prises en charge :

- Créer et supprimer un conteneur
- Obtenir les métadonnées et les propriétés de conteneur
- Liste des objets blob
- Obtenir et définir une ACL de conteneur
- Définir les métadonnées de conteneur

Non prises en charge :

- Louer à bail un conteneur

### <a name="blobs"></a>Objets blob

Prises en charge :

- Placer, obtenir et supprimer un objet blob
- Obtenir et définir les propriétés d’un objet blob
- Obtenir et définir les métadonnées d’un objet blob

Non prises en charge :

- Louer à bail un objet blob
- Faire une capture instantanée d’un objet blob
- Copier et abandonner la copie d’un objet blob
- Annuler la suppression d’un objet blob
- Définir un niveau d’objet blob

### <a name="block-blobs"></a>Objets blob de blocs

Prises en charge :

- Placer un bloc
- Placer et obtenir une liste de blocs

Non prises en charge :

- Placer un bloc à partir d’une URL

## <a name="feedback"></a>Commentaires

Vos commentaires sont importants pour nous faire de ce module et ses fonctionnalités utiles et faciles à utiliser. Partagez vos commentaires et dites-nous comment nous pouvons améliorer.

Vous pouvez nous contacter au absiotfeedback@microsoft.com

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [déployer le stockage Blob Azure sur IoT Edge](how-to-deploy-blob.md)
