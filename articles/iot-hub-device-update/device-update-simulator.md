---
title: Tutoriel Device Update pour Azure IoT Hub avec l’agent de référence du simulateur Ubuntu (18.04 x64) | Microsoft Docs
description: Commencer à utiliser Device Update pour Azure IoT Hub avec l’agent de référence du simulateur Ubuntu (18.04 x64).
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 77bfab51cf7ccc9ec6ffe3b0018c0a488b601a0a
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070206"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Tutoriel Device Update pour Azure IoT Hub avec l’agent de référence du simulateur Ubuntu (18.04 x64)

Device Update pour IoT Hub prend en charge deux formes de mise à jour : l’une basée sur une image et l’autre sur un package.

Les mises à jour basées sur une image offrent un niveau de confiance plus élevé dans l’état final de l’appareil. Il est généralement plus facile de répliquer les résultats d’une mise à jour basée sur une image entre un environnement de préproduction et un environnement de production, car cela ne présente pas les mêmes défis que les packages et leurs dépendances. En raison de leur nature atomique, il est également possible d’adopter aisément un modèle de basculement A/B.

Ce tutoriel vous guide tout au long des étapes de mise à jour de bout en bout basée sur une image à l’aide de Device Update pour IoT Hub. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Télécharger et installer une image
> * Ajouter une étiquette à votre appareil IoT
> * Importer une mise à jour
> * Créer un groupe d’appareils
> * Déployer une mise à jour basée sur une image
> * Superviser le déploiement de la mise à jour

## <a name="prerequisites"></a>Prérequis
* Si vous ne l’avez pas déjà fait, créez un [compte et une instance Device Update](create-device-update-account.md), ce qui inclut la configuration d’un hub IoT.

### <a name="download-and-install"></a>Télécharger et installer

* Autres applets de commande Az (Azure CLI) pour PowerShell :
  * Ouvrir PowerShell > Installer Azure CLI (« Oui » aux invites d’installation à partir d’une source « non approuvée »)

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Activer WSL sur votre appareil Windows (Sous-système Windows pour Linux)

1. Ouvrez PowerShell en tant qu’administrateur sur votre ordinateur et exécutez la commande suivante (vous pouvez être invité à redémarrer après chaque étape ; faites-le quand vous y êtes invité) :

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Vous pouvez être invité à redémarrer après cette étape.* )

2. Accédez au Microsoft Store sur le web et installez [Ubuntu 18.04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Démarrez « Ubuntu 18.04 LTS » et procédez à l’installation.

4. Une fois l’installation terminée, vous êtes invité à définir le nom de la racine (nom d’utilisateur) et le mot de passe. Veillez à utiliser un mot de passe facile à mémoriser pour le nom de la racine.

5. Dans PowerShell, exécutez la commande suivante pour définir Ubuntu comme distribution Linux par défaut :

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Listez toutes les distributions Linux, puis vérifiez qu’Ubuntu est bien celle par défaut.

```powershell
PS> wsl --list
```

7. Vous devez voir : **Ubuntu-18.04 (Default)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Télécharger l’agent de référence du simulateur Ubuntu (18.04 x64) pour Device Update

Vous pouvez télécharger l’image de mise à jour Ubuntu à partir de la section *Assets* des notes de publication [ici](https://github.com/Azure/iot-hub-device-update/releases).

L’agent se présente sous deux versions. Pour un scénario basé sur une image, utilisez AducIotAgentSim-microsoft-swupdate et pour un scénario basé sur un package, utilisez use AducIotAgentSim-microsoft-apt.

## <a name="install-device-update-agent-simulator"></a>Installer le simulateur d’agent Device Update

1. Démarrez Ubuntu WSL et entrez la commande suivante (remarquez l’espace supplémentaire et le point à la fin).

  ```shell
  explorer.exe .
  ```

2. Copiez AducIotAgentSim-microsoft-swupdate (ou AducIotAgentSim-microsoft-apt) à partir de votre dossier local dans lequel vous l’avez téléchargé sous /mnt vers votre dossier de base dans WSL.

3. Pour rendre les fichiers binaires exécutables, utilisez la commande suivante.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  or

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Le logiciel Device Update pour Azure IoT Hub est soumis aux termes de contrat de licence suivants :
   * [Licence Device Update pour IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licence cliente d’optimisation de la distribution](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Lisez les termes de contrat de licence avant d’utiliser l’agent. Le fait de procéder à l’installation et à l’utilisation revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas l’agent Device Update pour IoT Hub.

## <a name="add-device-to-azure-iot-hub"></a>Ajouter un appareil à Azure IoT Hub

Une fois que l’agent Device Update s’exécute sur un appareil IoT, l’appareil doit être ajouté à Azure IoT Hub.  Dans Azure IoT Hub, une chaîne de connexion est générée pour un appareil particulier.

1. À partir du portail Azure, lancez le hub IoT de Device Update.
2. Créez un appareil.
3. Sur le côté gauche de la page, accédez à « Appareils IoT », puis sélectionnez « Nouveau ».
4. Donnez un nom à l’appareil sous « ID de l’appareil » ; vérifiez que la case « Générer automatiquement les clés » est cochée.
5. Sélectionnez « Enregistrer ».
6. À présent, vous êtes redirigé vers la page « Appareils » et l’appareil que vous avez créé doit figurer dans la liste. Sélectionnez cet appareil.
7. Dans la vue de l’appareil, sélectionnez l’icône « Copier » en regard de « Chaîne de connexion principale ».
8. Collez les caractères copiés quelque part en vue d’une utilisation ultérieure dans les étapes ci-dessous. **Cette chaîne copiée est la chaîne de connexion de votre appareil**.

## <a name="add-connection-string-to-simulator"></a>Ajouter la chaîne de connexion au simulateur

Démarrez l’agent Device Update sur vos nouveaux appareils logiciels.

1. Démarrez Ubuntu.
2. Exécutez l’agent Device Update et spécifiez la chaîne de connexion de l’appareil provenant de la section précédente entre apostrophes :

Remplacez `<device connection string>` par votre chaîne de connexion.
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

or

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Faites défiler vers le haut et recherchez la chaîne indiquant que l’appareil est à l’état « inactif ». Un état « inactif » signifie que l’appareil est prêt à recevoir des commandes de service :

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>Ajouter une étiquette à votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez au hub IoT.

2. Sous « Appareils IoT » ou « IoT Edge » dans le volet de navigation de gauche, recherchez votre appareil IoT, puis accédez au Jumeau d’appareil ou au Jumeau de module.

3. Dans le jumeau de module du module d’agent Device Update, supprimez toutes les valeurs d’étiquette Device Update existantes en leur affectant la valeur null. Si vous utilisez l’identité d’appareil avec l’agent Device Update, effectuez ces changements sur le jumeau d’appareil.

4. Ajoutez une nouvelle valeur d’étiquette Device Update comme indiqué ci-dessous.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importer la mise à jour

1. Créez un manifeste d’importation en suivant ces [instructions](import-update.md).
2. Sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche.

3. Sélectionnez l’onglet Mises à jour.

4. Sélectionnez « + Importer une nouvelle mise à jour ».

5. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un fichier manifeste d’importation ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez le manifeste d’importation que vous avez créé plus haut.  Ensuite, sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un ou plusieurs fichiers de mise à jour ». Vous verrez une boîte de dialogue de sélection de fichiers. Sélectionnez l’image de mise à jour Ubuntu que vous avez téléchargée. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Capture d’écran illustrant la sélection du fichier de mise à jour." lightbox="media/import-update/select-update-files.png":::

6. Sélectionnez l’icône de dossier ou la zone de texte sous « Sélectionner un conteneur de stockage ». Sélectionnez ensuite le compte de stockage approprié.

7. Si vous avez déjà créé un conteneur, vous pouvez le réutiliser. (Dans le cas contraire, sélectionnez « + Conteneur » pour créer un conteneur de stockage pour les mises à jour.)  Sélectionnez le conteneur à utiliser et cliquez sur « Sélectionner ».
  
  :::image type="content" source="media/import-update/container.png" alt-text="Capture d’écran illustrant la sélection du conteneur." lightbox="media/import-update/container.png":::

8. Sélectionnez « Soumettre » pour démarrer le processus d’importation.

9. Le processus d’importation commence et l’écran passe à la section « Historique d’importation ». Sélectionnez « Actualiser » pour voir la progression jusqu’à la fin du processus d’importation. Selon la taille de la mise à jour, cette opération peut prendre quelques minutes ou durer plus longtemps.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Capture d’écran illustrant la séquence d’importation de mise à jour." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quand la colonne État indique que l’importation a réussi, sélectionnez l’en-tête « Prêt pour le déploiement ». Vous devez maintenant voir votre mise à jour importée dans la liste.

[Apprenez-en davantage](import-update.md) sur l’importation de mises à jour.

## <a name="create-update-group"></a>Créer un groupe de mises à jour

1. Accédez au hub IoT que vous avez précédemment connecté à votre instance Device Update.

2. Sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche.

3. Sélectionnez l’onglet Groupes en haut de la page. 

4. Sélectionnez le bouton Ajouter pour créer un nouveau groupe.

5. Sélectionnez l’étiquette IoT Hub que vous avez créée à l’étape précédente à partir de la liste. Sélectionnez Créer un groupe de mises à jour.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Capture d’écran illustrant la sélection d’une étiquette" lightbox="media/create-update-group/select-tag.PNG":::

[En savoir plus](create-update-group.md) sur l’ajout d’étiquettes et la création de groupes de mises à jour


## <a name="deploy-update"></a>Déployer la mise à jour

1. Une fois le groupe créé, vous devriez voir une nouvelle mise à jour disponible pour votre groupe d’appareils, avec un lien vers cette mise à jour sous Mises à jour en attente. Vous devrez peut-être actualiser une fois. 

2. Cliquez sur la mise à jour disponible.

3. Vérifiez que le groupe approprié est sélectionné en tant que groupe cible. Planifiez votre déploiement, puis sélectionnez Déployer la mise à jour.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Sélectionner la mise à jour" lightbox="media/deploy-update/select-update.png":::

4. Visualisez le graphique de conformité. Vous devez voir que la mise à jour est maintenant en cours. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Mise à jour en cours" lightbox="media/deploy-update/update-in-progress.png":::

5. Une fois votre appareil correctement mis à jour, vous devez voir votre graphique de conformité et les détails du déploiement refléter la même chose. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Mise à jour réussie" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Superviser le déploiement d’une mise à jour

1. Sélectionnez l’onglet Déploiements en haut de la page.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Onglet Déploiements" lightbox="media/deploy-update/deployments-tab.png":::

2. Sélectionnez le déploiement que vous avez créé pour en examiner les détails.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Détails du déploiement" lightbox="media/deploy-update/deployment-details.png":::

3. Sélectionnez Actualiser pour voir les détails d’état les plus récents. Poursuivez ce processus jusqu’à ce que l’état passe à Réussi.

Vous avez maintenant réussi à effectuer une mise à jour d’image de bout en bout à l’aide de Device Update pour IoT Hub avec l’agent de référence du simulateur Ubuntu (18.04 x64).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, nettoyez vos compte, instance, hub IoT et appareil IoT Device Update. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Dépannage](troubleshoot-device-update.md)

