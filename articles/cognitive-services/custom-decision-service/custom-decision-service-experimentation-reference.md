---
title: Expérimentation - Service Décision personnalisée
titlesuffix: Azure Cognitive Services
description: Cet article est un guide destiné à l’expérimentation du service Décision personnalisée.
services: cognitive-services
author: marco-rossi29
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: eec2c82b779fa5421bc9ac58107ef56f8c71bd1e
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366547"
---
# <a name="experimentation"></a>Expérimentation

Suivant la théorie des [bandits manchots contextuels (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), le service Décision personnalisée observe à plusieurs reprises un contexte, effectue une action et observe une récompense pour l’action choisie. Par exemple, la personnalisation du contenu : le contexte décrit un utilisateur, les actions sont des récits candidats, et la récompense mesure l’appréciation de l’utilisateur pour le récit recommandé.

Le service Décision personnalisée génère une stratégie, via une association des contextes aux actions. Avec une stratégie cible spécifique, vous souhaitez connaître la récompense attendu. Une façon d’évaluer cette récompense consiste à utiliser une stratégie en ligne et à la laisser choisir des actions (par exemple, recommander des récits aux utilisateurs). Toutefois, cette évaluation en ligne peut être coûteuse pour deux raisons :

* Elle expose les utilisateurs à une stratégie expérimentale non testée.
* Elle ne permet pas d’évaluer plusieurs stratégies cible.

Vous pouvez également opter pour une évaluation sans stratégie. Si vous avez des journaux provenant d’un système en ligne existant qui applique une stratégie de journalisation, une évaluation sans stratégie vous permet d’estimer les récompenses attendues pour les nouvelles stratégies cible.

À l’aide du fichier journal, l’expérimentation s’efforce de trouver la stratégie qui va générer le plus de récompenses. Les stratégies cibles sont paramétrées par des arguments [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki). Dans le mode par défaut, le script teste une variété d’arguments Vowpal Wabbit en les ajoutant à `--base_command`. Le script effectue les actions suivantes :

* Il détecte automatiquement les espaces de noms des fonctionnalités dans les premières lignes `--auto_lines` du fichier d’entrée.
* Il effectue un premier balayage sur les hyper-paramètres (`learning rate`, `L1 regularization` et `power_t`).
* Il teste la stratégie d’évaluation `--cb_type` (score de tendance inverse (`ips`) ou doublement robuste (`dr`)). Pour plus d’informations, consultez l’[exemple de bandit manchot contextuel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Il teste les éléments marginaux.
* Il teste les fonctionnalités d’interaction quadratique :
   * **phase bruteforce** : il teste toutes les combinaisons avec `--q_bruteforce_terms` paires ou moins.
   * **phase greedy** : ajoute la meilleure paire jusqu'à ce qu’il n’y ait aucune amélioration pendant `--q_greedy_stop` tentatives successives.
* Il effectue un deuxième balayage sur les hyper-paramètres (`learning rate`, `L1 regularization` et `power_t`).

Les paramètres qui contrôlent ces étapes incluent des arguments Vowpal Wabbit :
- Exemples d’options de manipulation :
  - espaces de noms partagés
  - espaces de noms d'action
  - espaces de noms marginaux
  - fonctionnalités quadratiques
- Options de règle de mise à jour
  - taux d'apprentissage
  - régularisation L1
  - valeur de puissance t

Pour obtenir une explication détaillée des arguments ci-dessus, consultez les [arguments de ligne de commande Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Prérequis
- Vowpal Wabbit : installé et sur votre chemin d’accès.
  - Windows : [utilisez le programme d’installation `.msi`](https://github.com/eisber/vowpal_wabbit/releases).
  - Autres plates-formes : [obtenez le code source](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3 : installé et sur votre chemin d’accès.
- NumPy : utilisez le gestionnaire de package de votre choix.
- Référentiel *Microsoft/mwt-ds* : [clonez le référentiel](https://github.com/Microsoft/mwt-ds).
- Fichier journal JSON du service Décision : par défaut, la commande de base inclut `--dsjson`, qui permet au JSON du service Décision d’analyser le fichier de données d’entrée. [Obtenez un exemple de ce format](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Usage
Accédez à `mwt-ds/DataScience` et exécutez `Experimentation.py` avec les arguments appropriés, comme indiqué dans le code suivant :

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Un journal de résultats est ajouté au fichier *mwt-ds/DataScience/experiments.csv*.

### <a name="parameters"></a>parameters
| Entrée | Description | Default |
| --- | --- | --- |
| `-h`, `--help` | Afficher le message d’aide et quitter. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Chemin d’accès du fichier de données (format `.json` ou `.json.gz`, chaque ligne est un élément `dsjson`). | Obligatoire |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Commande de base Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Nombre de processus parallèles à utiliser. | Processeurs logiques |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Espaces de noms de fonctionnalités partagés (par exemple, `abc` représente les espaces de noms `a`, `b` et `c`).  | Détection automatique dans le fichier de données |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Espaces de noms des fonctionnalités d’action. | Détection automatique dans le fichier de données |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Espaces de noms des fonctionnalités marginales. | Détection automatique dans le fichier de données |  
| `--auto_lines AUTO_LINES` | Nombre de lignes de fichier de données à analyser pour détecter automatiquement les espaces de noms de fonctionnalités. | `100` |  
| `--only_hp` | Balayer uniquement les hyper-paramètres (`learning rate`, `L1 regularization` et `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Plage de taux d’apprentissage (valeurs positives `min,max,steps`). | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Plage de régularisation L1 (valeurs positives `min,max,steps`). | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Plage de puissance_t (valeurs positives `min,max,step`). | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Nombre de paires quadratiques à tester dans la phase bruteforce. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Arrondi sans améliorations, après interruption de la phase de recherche greedy quadratique. | `3` |  

### <a name="examples"></a>Exemples
Pour utiliser les valeurs par défaut prédéfinies :
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

De façon équivalente, Vowpal Wabbit peut également ingérer des fichiers `.json.gz` :
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Pour balayer uniquement les hyper-paramètres (`learning rate`, `L1 regularization`, et `power_t`, avec un arrêt après l’étape 2) :
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
