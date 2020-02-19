---
title: Utiliser GitHub Actions avec une synchronisation Azure App Configuration
description: Utilisez GitHub Actions pour déclencher une mise à jour de votre instance App Configuration lorsque des actions définies sont effectuées sur un dépôt GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ce8d42ec7c37b19378b6f4ae0c81548f2eff5c9c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190378"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synchroniser votre instance App Configuration à l’aide de GitHub Actions
Azure App configuration utilise GitHub Actions pour mettre à jour une instance App Configuration déclenchée par une action effectuée sur un dépôt GitHub. Vous pouvez tirer parti des flux de travail GitHub pour mettre à jour une configuration d’application, ce qui permet l’intégration de mises à jour de configuration d’application dans le même flux de travail que celui utilisé pour mettre à jour le code de l’application.

Dans GitHub Actions, un [flux de travail](https://help.github.com/articles/about-github-actions#workflow) est un processus automatisé défini dans votre dépôt GitHub. Ce processus indique à GitHub comment générer et déployer votre projet GitHub. Azure App configuration fournit l’action de *synchronisation Azure App Configuration* pour activer les mises à jour d’une instance App Configuration lorsque des modifications sont apportées au dépôt source. 

Un flux de travail est défini par un fichier YAML (.yml) situé dans le chemin d’accès `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres définissant le flux de travail.

Des événements GitHub, tels qu’un push dans un dépôt, peuvent déclencher un flux de travail d’action GitHub.  Azure fournit l’action de *synchronisation Azure App Configuration* pour vous permettre de déclencher une mise à jour d’une instance App Configuration quand une action GitHub spécifiée se produit. Cela permet aux équipes de tirer parti des fonctionnalités principales de GitHub lors d’un push, d’un examen ou de la création de branche de fichiers de configuration d’application, comme elles le font avec un code d’application.

La [documentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) GitHub fournit une vue détaillée des flux de travail et actions GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Activer GitHub Actions dans votre dépôt
Pour commencer à utiliser cette action GitHub, accédez à votre dépôt et sélectionnez l’onglet **Actions**. Cliquez sur « Nouveau workflow », puis sur « Configurer un workflow vous-même ». À partir de là, recherchez « Azure App Configuration Sync » sur la Place de marché.
> [!div class="mx-imgBorder"]
> ![Sélectionner l’onglet Action](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Sélectionner l’action de synchronisation de configuration d’application](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchroniser les fichiers de configuration après un push
Cette action synchronise les fichiers Azure App Configuration lors du push d’une modification vers `appsettings.json`. Quand un développeur apporte et effectue un push de modification vers `appsettings.json`, l’action de synchronisation d’App Configuration met à jour l’instance App Configuration avec les nouvelles valeurs.

La première section de ce flux de travail spécifie que l’action se déclenche *sur* un *push* contenant `appsettings.json` vers la branche *maître*. La deuxième section répertorie les travaux exécutés une fois que l’action déclenchée. L’action extrait les fichiers appropriés et met à jour l’instance App Configuration à l’aide de la chaîne de connexion stockée en tant que secret dans le dépôt.  Pour plus d’informations sur l’utilisation des secrets dans GitHub, voir [cet article](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sur la création et l’utilisation de secrets chiffrés.

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
L’action précédente a simplement mis à jour l’instance App Configuration à chaque mise à jour de `appsettings.json`. Cette action insère une étiquette dynamique à chaque synchronisation, garantissant ainsi que chaque synchronisation peut être identifiée de manière unique.  Cela permet un mappage rapide des changements de code aux changements de configuration.

La première section de ce flux de travail spécifie que l’action se déclenche *sur* un *push* contenant `appsettings.json` vers la branche *maître*. La deuxième section exécute un travail qui crée une étiquette unique pour la mise à jour de configuration en fonction du hachage de validation. Le travail met ensuite à jour l’instance App Configuration avec les nouvelles valeurs et l’étiquette unique pour cette mise à jour.

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
Lorsque le mode strict est activé, la synchronisation garantit que l’instance App Configuration correspond exactement au fichier de configuration pour le préfixe et l’étiquette donnés. Les paires clé-valeur ayant les mêmes préfixe et nom qui ne figurent pas dans le fichier de configuration sont supprimées. 
 
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

Pour une profondeur de 2, l’exemple ci-dessus retourne maintenant la paire clé-valeur suivante :

| Clé | Valeur |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Comprendre les entrées d’action
Les paramètres d’entrée spécifient les données que l’action utilise pendant le runtime.  Le tableau suivant contient les paramètres d’entrée acceptés par la synchronisation d’App Configuration et les valeurs attendues pour chacun d’eux.  Pour plus d’informations sur les entrées d’action pour GitHub Actions, voir la [documentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs) de GitHub.

> [!Note]
> Les ID d’entrée ne respectent pas la casse.


| Nom d’entrée | Requis ? | Valeur |
|----|----|----|
| configurationFile | Oui | Chemin d’accès du fichier de configuration dans le dépôt, par rapport à la racine de celui-ci.  Les modèles Glob sont pris en charge et peuvent inclure plusieurs fichiers. |
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
