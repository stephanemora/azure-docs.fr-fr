---
title: Utiliser GitHub Actions avec une synchronisation Azure App Configuration
description: Utilisez GitHub Actions pour déclencher une mise à jour de votre instance App Configuration lorsque des actions définies sont effectuées sur un dépôt GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523711"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synchroniser votre instance App Configuration à l’aide de GitHub Actions
Azure App Configuration utilise GitHub Actions pour déclencher les mises à jour vers une instance App Configuration en fonction des actions effectuées dans un dépôt GitHub. Les workflows GitHub déclenchent des mises à jour de configuration, ce qui permet d’intégrer ces mises à jour au workflow qui est utilisé pour mettre à jour le code de l’application.

Un [workflow](https://help.github.com/articles/about-github-actions#workflow) GitHub Actions définit un processus automatisé dans un dépôt GitHub. Ce processus indique à GitHub comment générer et déployer votre projet GitHub. Azure App configuration fournit l’action de *synchronisation Azure App Configuration* pour activer les mises à jour d’une instance App Configuration lorsque des modifications sont apportées au dépôt source. 

Votre workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres du workflow.

Des événements GitHub, tels qu’un push dans un dépôt, peuvent déclencher un flux de travail d’action GitHub.  L’action *Azure App Configuration Sync* vous permet de déclencher la mise à jour d’une instance App Configuration quand une action GitHub spécifiée se produit. Vous pouvez déclencher des mises à jour de configuration lorsque vous envoyez (push), révisez ou branchez des fichiers de configuration d’application, comme vous le feriez avec du code d’application.

La [documentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) GitHub fournit une vue détaillée des flux de travail et actions GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Activer GitHub Actions dans votre dépôt
Pour commencer à utiliser cette action GitHub, accédez à votre dépôt et sélectionnez l’onglet **Actions**. Cliquez sur **Nouveau workflow**, puis sur **Configurer un workflow vous-même**. Enfin, recherchez « Azure App Configuration Sync » sur la Place de marché.
> [!div class="mx-imgBorder"]
> ![Sélectionner l’onglet Action](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Sélectionner l’action Azure App Configuration Sync](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchroniser les fichiers de configuration après un push
Cette action synchronise les fichiers Azure App Configuration lors du push d’une modification vers `appsettings.json`. Quand un développeur envoie (push) une modification vers `appsettings.json`, l’action App Configuration Sync met à jour l’instance App Configuration avec les nouvelles valeurs.

La première section de ce flux de travail spécifie que l’action se déclenche *sur* un *push* contenant `appsettings.json` vers la branche *maître*. La deuxième section répertorie les travaux exécutés une fois que l’action déclenchée. L’action extrait les fichiers appropriés et met à jour l’instance App Configuration à l’aide de la chaîne de connexion stockée en tant que secret dans le dépôt.  Pour plus d’informations sur l’utilisation des secrets dans GitHub, consultez [cet article GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sur la création et l’utilisation des secrets chiffrés.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Utiliser une étiquette dynamique lors d’une synchronisation
L’action précédente met à jour l’instance App Configuration à chaque mise à jour de `appsettings.json`. Cette action insère une étiquette dynamique à chaque synchronisation. De cette façon, chaque synchronisation peut être identifiée et les modifications du code peuvent être mappées aux modifications de configuration.

La première section de ce flux de travail spécifie que l’action se déclenche *sur* un *push* contenant `appsettings.json` vers la branche *maître*. La deuxième section exécute un travail qui crée une étiquette pour la mise à jour de configuration en fonction du hachage de validation. Le travail met ensuite à jour l’instance App Configuration avec les nouvelles valeurs et l’étiquette unique pour cette mise à jour.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Utiliser une synchronisation stricte
Lorsque le mode strict est activé, la synchronisation garantit que l’instance App Configuration correspond exactement au fichier de configuration pour le préfixe et l’étiquette donnés. Si les paires clé-valeur qui ont le même préfixe et le même nom ne figurent pas dans le fichier de configuration, elles sont supprimées. 
 
Si le mode strict n’est pas activé, la synchronisation ne définit que les paires clé-valeur du fichier de configuration. Aucune paire clé-valeur n’est supprimée. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Utiliser une profondeur maximale pour limiter une action GitHub
Le comportement par défaut pour les attributs JSON imbriqués consiste à aplatir l’objet entier.  Le JSON ci-dessous définit cette paire clé-valeur :

| Clé | Valeur |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Si l’objet imbriqué est destiné à être la valeur envoyée par push à l’instance Configuration, vous pouvez utiliser la valeur de profondeur (*depth*) pour arrêter l’aplatissement à la profondeur appropriée. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Pour une profondeur de 2, l’exemple ci-dessus retourne maintenant la paire clé-valeur suivante :

| Clé | Valeur |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Comprendre les entrées d’action
Les paramètres d’entrée spécifient les données que l’action utilise pendant le runtime.  Le tableau suivant contient les paramètres d’entrée acceptés par la synchronisation d’App Configuration et les valeurs attendues pour chacun d’eux.  Pour plus d’informations sur les entrées d’action pour GitHub Actions, voir la [documentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs) de GitHub.

> [!Note]
> Les ID d’entrée ne respectent pas la casse.


| Nom d’entrée | Requis ? | Valeur |
|----|----|----|
| configurationFile | Oui | Chemin relatif du fichier de configuration dans le dépôt.  Les modèles Glob sont pris en charge et peuvent inclure plusieurs fichiers. |
| format | Oui | Format du fichier de configuration.  Les formats valides sont les suivants : JSON, YAML, propriétés. |
| connectionString | Oui | Chaîne de connexion pour l’instance App Configuration. La chaîne de connexion doit être stockée en tant que secret dans le dépôt GitHub, et seul le nom du secret doit être utilisé dans le flux de travail. |
| separator | Oui | Séparateur utilisé lors de l’aplatissement du fichier de configuration en paires clé-valeur.  Les valeurs valides sont les suivantes : . , ; : - _ __ / |
| prefix | Non | Préfixe à ajouter au début des clés. |
| label | Non | Étiquette utilisée lors de la définition de paires clé-valeur. Si elle n’est pas spécifiée, une étiquette null est utilisée. |
| strict | Non | Valeur booléenne déterminant si le mode strict est activé. La valeur par défaut est false. |
| depth | Non | Profondeur maximale pour l’aplatissement du fichier de configuration.  La profondeur doit être un nombre positif.  La valeur par défaut n’a pas de profondeur maximale. |
| tags | Non | Spécifie la balise définie sur les paires clé-valeur.  Le format attendu est une forme convertie en chaîne d’un objet JSON similaire à la suivante : { [propertyName : string]: string; } Chaque nom-valeur de propriété devient une balise. |

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris l’action GitHub de synchronisation d’App Configuration et la façon de l’utiliser pour automatiser les mises à jour de votre instance App Configuration. Pour savoir comment Azure App Configuration réagit aux modifications des paires clé-valeur, passez à l’[article](./concept-app-configuration-event.md) suivant.
