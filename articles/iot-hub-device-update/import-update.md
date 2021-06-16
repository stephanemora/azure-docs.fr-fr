---
title: Guide pratique pour ajouter une nouvelle mise à jour | Microsoft Docs
description: Guide pratique pour ajouter une nouvelle mise à jour dans Device Update pour IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 025ab1ddd9a7b14ac75df762c54fe48e4f665e29
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970144"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Ajouter une mise à jour dans Device Update pour IoT Hub
Découvrez comment ajouter une nouvelle mise à jour dans Device Update pour IoT Hub.

## <a name="prerequisites"></a>Prérequis

* [Accès à un hub IoT avec Device Update pour IoT Hub activé](create-device-update-account.md). 
* Un appareil (ou un simulateur) IoT [provisionné pour Device Update](device-update-agent-provisioning.md) dans IoT Hub.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) ou version ultérieure (y compris les installations Linux, macOS et Windows)
* Navigateurs pris en charge :
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Certaines données envoyées à ce service peuvent être traitées dans une région située en dehors de la région dans laquelle cette instance a été créée.

## <a name="obtain-an-update-for-your-devices"></a>Obtenir une mise à jour pour vos appareils

Maintenant que vous avez configuré Device Update et provisionné vos appareils, vous avez besoin du ou des fichiers de mise à jour que vous allez déployer sur ces appareils.

Si vous avez acheté des appareils auprès d’un OEM ou d’un intégrateur de solutions, cette organisation fournira probablement des fichiers de mise à jour à votre place, sans que vous ayez à créer les mises à jour. Contactez l’OEM ou l’intégrateur de solutions pour savoir comment il rend les mises à jour disponibles.

Si votre organisation crée déjà des logiciels pour les appareils que vous utilisez, c’est ce même groupe qui créera les mises à jour de ces logiciels. Quand vous créez une mise à jour à déployer à l’aide de Device Update pour IoT Hub, commencez par l’[approche basée sur une image ou par l’approche basée sur un package](understand-device-update.md#support-for-a-wide-range-of-update-artifacts) en fonction de votre scénario. Remarque : Si vous voulez créer vos propres mises à jour, mais que vous débutez, GitHub est une excellente option pour gérer votre développement. Vous pouvez stocker et gérer votre code source, et effectuer des opérations d’intégration continue (CI) et de déploiement continu (CD) avec [GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration).

## <a name="create-a-device-update-import-manifest"></a>Créer un manifeste d’importation Device Update

Si vous ne l’avez pas déjà fait, veillez à vous familiariser avec les [concepts d’importation](import-concepts.md) de base.

1. Vérifiez que vos fichiers de mise à jour se trouvent dans un répertoire accessible à partir de PowerShell.

2. Créez un fichier texte nommé **AduUpdate.psm1** dans le répertoire où se trouve votre fichier image de mise à jour ou fichier manifeste APT. Ouvrez ensuite le cmdlet PowerShell [AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets), copiez le contenu dans votre fichier texte, puis enregistrez le fichier texte.

3. Dans PowerShell, accédez au répertoire dans lequel vous avez créé votre cmdlet PowerShell à l’étape 2. Utilisez l’option Copy ci-dessous, puis collez dans PowerShell pour exécuter les commandes :

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

    À titre d’aide-mémoire, voici quelques exemples de valeurs pour les paramètres ci-dessus. Vous pouvez également afficher le [schéma complet du manifeste d’importation](import-schema.md) pour plus de détails.

    | Paramètre | Description |
    | --------- | ----------- |
    | deviceManufacturer | Fabricant de l’appareil avec lequel la mise à jour est compatible, par exemple Contoso. Doit correspondre à la _propriété de l’appareil_ [manufacturer](./device-update-plug-and-play.md#device-properties).
    | deviceModel | Modèle de l’appareil avec lequel la mise à jour est compatible, par exemple Toaster. Doit correspondre à la _propriété de l’appareil_ [model](./device-update-plug-and-play.md#device-properties).
    | updateProvider | Entité qui crée ou qui est directement responsable de la mise à jour. Souvent, il s’agit d’un nom d’entreprise.
    | updateName | Identificateur d’une classe de mises à jour. La classe peut être une chose quelconque que vous choisissez. Elle correspond souvent à un nom d’appareil ou de modèle.
    | updateVersion | Numéro de version qui distingue cette mise à jour d’autres mises à jour ayant le même fournisseur et le même nom. Ne correspond pas à une version d’un composant logiciel individuel sur l’appareil (mais possibilité le faire si vous le souhaitez).
    | updateType | <ul><li>Spécifier `microsoft/swupdate:1` pour une mise à jour d’image</li><li>Spécifier `microsoft/apt:1` pour une mise à jour de package</li></ul>
    | installedCriteria | <ul><li>Spécifier la valeur de SWVersion pour le type de mise à jour `microsoft/swupdate:1`</li><li>Spécifiez **name-version**, où _name_ est le nom du manifeste APT et _version_ est la version du manifeste APT. Par exemple : contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | Chemin du ou des fichiers de mise à jour sur votre ordinateur


## <a name="review-the-generated-import-manifest"></a>Examiner le manifeste d’importation généré

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

## <a name="import-an-update"></a>Importer une mise à jour

> [!NOTE]
> Les instructions ci-dessous montrent comment importer une mise à jour par le biais de l’interface utilisateur du Portail Azure. Vous pouvez également utiliser la [mise à jour de l’appareil pour les API IoT Hub](/rest/api/deviceupdate/updates) pour importer une mise à jour. 

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