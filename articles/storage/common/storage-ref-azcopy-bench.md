---
title: azcopy bench | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518125"
---
# <a name="azcopy-bench"></a>azcopy bench

Exécute un test d’évaluation des performances en chargeant les données de test vers une destination spécifiée. Les données de test sont générées automatiquement.

La commande benchmark exécute le même processus de chargement que « copy », sauf que :

  - Il n’y a aucun paramètre source.  La commande nécessite uniquement une URL de destination. Dans la version actuelle, cette URL de destination doit faire référence à un conteneur d’objets blob.
  
  - La charge utile est décrite par les paramètres de ligne de commande, qui contrôlent le nombre de fichiers générés automatiquement et leur taille. Le processus de génération s’effectue entièrement en mémoire. Le disque n’est pas utilisé.
  
  - Seuls quelques-uns des paramètres facultatifs qui sont disponibles pour la commande copy sont pris en charge.
  
  - Des diagnostics supplémentaires sont mesurés et signalés.
  
  - Par défaut, les données transférées sont supprimées à la fin de la série de tests.

Le mode « test d’évaluation » se règle automatiquement en fonction du nombre de connexions TCP parallèles qui fournit le débit maximal. Il affiche ce nombre à la fin. Pour empêcher le réglage automatique, affectez un nombre spécifique de connexions à la variable d’environnement AZCOPY_CONCURRENCY_VALUE.

Tous les types d’authentification habituels sont pris en charge. Toutefois, l’approche la plus pratique pour le test d’évaluation consiste généralement à créer un conteneur vide avec un jeton SAS et à utiliser l’authentification SAS.

## <a name="examples"></a>Exemples

```azcopy
azcopy bench [destination] [flags]
```

Exécuter un test d’évaluation avec les paramètres par défaut (adapté aux réseaux d’évaluation jusqu’à 1 Gbit/s) :’

- azcopy bench "https://[compte].blob.core.windows.net/[conteneur]?<SAS>"

Exécuter un test d’évaluation qui charge 100 fichiers, chacun d’une taille de 2 Gio : (adapté à l’évaluation sur un réseau rapide, par exemple 10 Gbits/s) :’

- azcopy bench "https://[compte].blob.core.windows.net/[conteneur]?<SAS>" --file-count 100 --size-per-file 2G

Comme ci-dessus, mais utiliser 50 000 fichiers, chacun d’une taille de 8 MiB, et calculer leurs hachages MD5 (de la même façon qu’effectué par l’indicateur --put-md5 dans la commande copy). L’objectif de --put-md5 lors des tests d’évaluation est de tester si le calcul MD5 affecte le débit pour le nombre de fichiers et la taille sélectionnés :

- azcopy bench "https://[compte].blob.core.windows.net/[conteneur]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>Options

**--blob-type** string  Définit le type d’objet blob au niveau de la destination. Permet d’effectuer un test d’évaluation de différents types d’objets blob. Identique au paramètre du même nom dans la commande copy (par défaut : « Detect »).

**--block-size-mb** float  Utiliser cette taille de bloc (spécifiée en Mio). La valeur par défaut est calculée automatiquement en fonction de la taille du fichier. Les fractions décimales sont autorisées (par exemple 0,25). Identique au paramètre du même nom dans la commande copy.

**--delete-test-data**  Si la valeur est true, les données du test d’évaluation sont supprimées à la fin de l’exécution du test.  Affectez-lui la valeur false si vous souhaitez conserver les données dans la destination (par exemple afin de les utiliser pour des tests manuels en dehors du mode « test d’évaluation ») (par défaut : true).

**--file-count** uint  Nombre de fichiers de données générés automatiquement (par défaut : 100).

**-h, --help**  Aide pour la commande bench

**--log-level** string Définit le niveau de détail pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes/réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie) (par défaut : « INFO »)

**--put-md5**  Crée un hachage MD5 de chaque fichier, puis enregistre le hachage en tant que propriété Content-MD5 de l’objet blob ou du fichier de destination. (par défaut, le hachage n’est pas créé.) Identique au paramètre du même nom dans la commande copy.

**--size-per-file** string   Taille de chaque fichier de données généré automatiquement. Doit être un nombre immédiatement suivi de K, M ou G, par exemple 12K ou 200G (par défaut : « 250M »).

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

**--cap-mbps uint32**      Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.

**--output-type** string   Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ». (par défaut : « text »).

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
