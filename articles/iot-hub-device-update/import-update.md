---
title: Guide pratique pour importer une nouvelle mise à jour | Microsoft Docs
description: Guide pratique pour importer une nouvelle mise à jour dans Device Update pour IoT Hub.
author: andbrown
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d8757f3076f784576f95bbdfc30abf578446c776
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660312"
---
# <a name="import-new-update"></a>Importer une nouvelle mise à jour
Découvrez comment importer une nouvelle mise à jour dans Device Update pour IoT Hub.

## <a name="prerequisites"></a>Prérequis

* [Accès à un hub IoT avec Device Update pour IoT Hub activé](create-device-update-account.md). Il est recommandé d’utiliser un niveau S1 (Standard) ou supérieur pour votre hub IoT. 
* Appareil IoT (ou simulateur) provisionné pour Device Update dans IoT Hub.
   * Si vous utilisez un appareil réel, vous aurez besoin d’un fichier image de mise à jour pour une mise à jour d’image ou d’un [fichier manifeste APT](device-update-apt-manifest.md) pour une mise à jour de package.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) ou ultérieur.
* Navigateurs pris en charge :
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Certaines données envoyées à ce service peuvent être traitées dans une région située en dehors de la région dans laquelle cette instance a été créée.

## <a name="create-device-update-import-manifest"></a>Créer un manifeste d’importation Device Update

1. Vérifiez que votre fichier image de mise à jour ou fichier manifeste APT se trouve dans un répertoire accessible à partir de PowerShell.

2. Clonez le [dépôt Device Update for IoT Hub](https://github.com/azure/iot-hub-device-update), ou téléchargez-le sous forme de fichier .zip dans un emplacement accessible à partir de PowerShell (une fois le fichier zip téléchargé, cliquez avec le bouton droit sur l’onglet `Properties` > `General`, puis cochez `Unblock` dans la section `Security` pour éviter les avertissements de sécurité PowerShell).

3. Dans PowerShell, accédez au répertoire `tools/AduCmdlets` et exécutez :

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Exécutez les commandes suivantes en remplaçant les exemples de valeurs de paramètre pour générer un manifeste d’importation, un fichier JSON qui décrit la mise à jour :
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    À titre d’aide-mémoire, voici quelques exemples de valeurs pour les paramètres ci-dessus. Pour obtenir la documentation complète, consultez le schéma de manifeste d’importation complet ci-dessous.

    | Paramètre | Description |
    | --------- | ----------- |
    | deviceManufacturer | Fabricant de l’appareil avec lequel la mise à jour est compatible, par exemple, Contoso
    | deviceModel | Modèle de l’appareil avec lequel la mise à jour est compatible, par exemple, Toaster
    | updateProvider | Partie fournisseur de l’identité de la mise à jour, par exemple, Fabrikam
    | updateName | Partie nom de l’identité de la mise à jour, par exemple, ImageUpdate
    | updateVersion | Version de la mise à jour, par exemple, 2.0
    | updateType | <ul><li>Spécifier `microsoft/swupdate:1` pour une mise à jour d’image</li><li>Spécifier `microsoft/apt:1` pour une mise à jour de package</li></ul>
    | installedCriteria | <ul><li>Spécifier la valeur de SWVersion pour le type de mise à jour `microsoft/swupdate:1`</li><li>Spécifier la valeur recommandée pour le type de mise à jour `microsoft/apt:1`
    | updateFilePath(s) | Chemin du ou des fichiers de mise à jour sur votre ordinateur

    Schéma de manifeste d’importation complet

    | Nom | Type | Description | Restrictions |
    | --------- | --------- | --------- | --------- |
    | UpdateId | l'objet `UpdateId` | Identité de la mise à jour. |
    | UpdateType | string | Type de mise à jour : <ul><li>Spécifiez `microsoft/apt:1` quand vous effectuez une mise à jour basée sur un package à l’aide de l’agent de référence.</li><li>Spécifiez `microsoft/swupdate:1` quand vous effectuez une mise à jour basée sur une image à l’aide de l’agent de référence.</li><li>Spécifiez `microsoft/simulator:1` quand vous utilisez l’exemple de simulateur d’agent.</li><li>Spécifiez un type personnalisé si vous développez un agent personnalisé.</li></ul> | <ul><li>Format : `{provider}/{type}:{typeVersion}`</li><li>Longueur maximale totale de 32 caractères</li></ul> |
    | InstalledCriteria | string | Chaîne interprétée par l’agent pour déterminer si la mise à jour a été appliquée avec succès :  <ul><li>Spécifiez la **valeur** de SWVersion pour le type de mise à jour `microsoft/swupdate:1`.</li><li>Spécifiez `{name}-{version}` pour le type de mise à jour `microsoft/apt:1`, dont le nom et la version sont obtenus à partir du fichier APT.</li><li>Spécifiez le code de hachage du fichier de mise à jour pour le type de mise à jour `microsoft/simulator:1`.</li><li>Spécifiez une chaîne personnalisée si vous développez un agent personnalisé.</li></ul> | 64 caractères maximum |
    | Compatibilité | Tableau d’objets `CompatibilityInfo` | Informations de compatibilité de l’appareil compatible avec cette mise à jour. | Maximum de 10 éléments |
    | CreatedDateTime | date/heure | Date et heure de création de la mise à jour. | Format de date et heure ISO 8601 délimité, en UTC |
    | ManifestVersion | string | Version du schéma du manifeste d’importation. Spécifiez `2.0`, qui sera compatible avec l’interface `urn:azureiot:AzureDeviceUpdateCore:1` et l’interface `urn:azureiot:AzureDeviceUpdateCore:4`.</li></ul> | Doit être `2.0` |
    | Fichiers | Tableau d’objets `File` | Mettre à jour les fichiers de charge utile | Maximum de 5 fichiers |

Remarque : Tous les champs sont obligatoires.

## <a name="review-generated-import-manifest"></a>Examiner le manifeste d’importation généré

Exemple :
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Importer la mise à jour

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre hub IoT avec Device Update.

2. Sur le côté gauche de la page, sélectionnez « Mises à jour de l’appareil » sous « Gestion automatique des appareils ».

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importer des mises à jour" lightbox="media/import-update/import-updates-3.png":::

3. Plusieurs onglets s’affichent dans la partie supérieure de l’écran. Sélectionnez l’onglet Mises à jour.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Mises à jour" lightbox="media/import-update/updates-tab.png":::

4. Sélectionnez « + Importer une nouvelle mise à jour » sous l’en-tête « Prêt pour le déploiement ».

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importer une nouvelle mise à jour" lightbox="media/import-update/import-new-update-2.png":::

5. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un fichier manifeste d’importation ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez le manifeste d’importation que vous avez créé précédemment à l’aide de l’applet de commande PowerShell. Ensuite, sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un ou plusieurs fichiers de mise à jour ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez vos fichiers de mise à jour.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Sélectionner les fichiers de mise à jour" lightbox="media/import-update/select-update-files.png":::

6. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un conteneur de stockage ». Sélectionnez ensuite le compte de stockage approprié. Le conteneur de stockage est utilisé pour indexer temporairement les fichiers de mise à jour.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Compte de stockage" lightbox="media/import-update/storage-account.png":::

7. Si vous avez déjà créé un conteneur, vous pouvez le réutiliser. (Dans le cas contraire, sélectionnez « + Conteneur » pour créer un conteneur de stockage pour les mises à jour.)  Sélectionnez le conteneur à utiliser et cliquez sur « Sélectionner ».

   :::image type="content" source="media/import-update/container.png" alt-text="Sélectionner Conteneur" lightbox="media/import-update/container.png":::

8. Sélectionnez « Soumettre » pour démarrer le processus d’importation.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publier la mise à jour" lightbox="media/import-update/publish-update.png":::

9. Le processus d’importation commence et l’écran passe à la section « Historique d’importation ». Sélectionnez « Actualiser » pour voir la progression jusqu’à la fin du processus d’importation (selon la taille de la mise à jour, cette opération peut s’effectuer en quelques minutes, mais peut aussi prendre plus de temps).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Mettre à jour le séquencement d’importation" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quand la colonne État indique que l’importation a réussi, sélectionnez l’en-tête « Prêt pour le déploiement ». Vous devez maintenant voir votre mise à jour importée dans la liste.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="État du travail" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Étapes suivantes

[Créer des groupes](create-update-group.md)

[En savoir plus sur les concepts d’importation](import-concepts.md)
