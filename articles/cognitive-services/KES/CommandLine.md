---
title: Interface de ligne de commande du Service d’exploration des connaissances (KES) | Microsoft Docs
description: Utilisez l’interface de ligne de commande KES pour créer un index et des fichiers de grammaire depuis des données structurées, et les déployer en tant que services web dans Microsoft Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 71a6f5ac93e5605182a55de1bae9a99c5c3eddf4
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136353"
---
# <a name="command-line-interface"></a>Interface de ligne de commande
L’interface de ligne de commande KES fournit la capacité de créer un index et des fichiers de grammaire depuis des données structurées et de les déployer en tant que services web.  Voici la syntaxe générale qu’elle utilise : `kes.exe <command> <required_args> [<optional_args>]`.  Vous pouvez exécuter `kes.exe` sans argument pour afficher une liste de commandes, ou `kes.exe <command>` pour afficher une liste d’arguments disponibles pour la commande spécifiée.  Voici une liste des commandes disponibles :
* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index

La commande **buil_index** crée un fichier d’index à partir d’un fichier de définition de schéma et d’un fichier de données d’objets à indexer.  Le fichier d’index ainsi créé peut être utilisé pour évaluer des expressions de requête structurées, ou pour générer des interprétations de requêtes dans un langage naturel conjointement à un fichier de grammaire compilé.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Paramètre      | Description               |
|----------------|---------------------------|
| `<schemaFile>` | Chemin du schéma d’entrée |
| `<dataFile>`   | Chemin des données d’entrée   |
| `<indexFile>`  | Chemin de l’index de sortie |
| `--description <description>` | Chaîne de description |
| `--remote <vmSize>`           | Taille de la machine virtuelle pour build distante |

Ces fichiers peuvent être spécifiés par des chemins de fichiers locaux ou des chemins URL vers des objets blob Azure.  Le schéma de fichier décrit la structure des objets indexés ainsi que les opérations à prendre en charge (consultez [Format de schéma](SchemaFormat.md)).  Le fichier de données énumère les objets et les valeurs d’attribut à indexer (voir [Format de données](DataFormat.md)).  Lorsque la build se termine, le fichier d’index de sortie contient une représentation compressée des données d’entrée qui prend en charge les opérations désirées.  

Une chaîne de description peut être spécifiée (facultatif) pour identifier par la suite un index binaire à l’aide de la commande **describe_index**.  

Par défaut, l’index est créé sur l’ordinateur local.  En dehors de l’environnement Azure, les builds locales se limitent à des fichiers de données pouvant contenir jusqu'à 10 000 objets.  Lorsque la balise --remote est spécifiée, l’index sera créé sur une machine virtuelle Azure temporaire de la taille spécifiée.  Cela permet à de plus larges indices d’être créés plus efficacement avec des machines virtuelles Azure disposant de plus de mémoire.  Pour éviter la pagination, qui ralentit le processus de création, nous vous recommandons d’utiliser une machine virtuelle disposant de 3 fois plus de RAM que la taille du fichier de données d’entrée.  Pour obtenir la liste des tailles de machine virtuelle disponibles, consultez [Tailles des machines virtuelles Windows dans Azure](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Pour créer plus rapidement, triez au préalable les objets du fichier de données par ordre de probabilité décroissant.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar

La commande **build_grammar** compile un fichier de grammaire spécifié dans un fichier XML en fichier de grammaire binaire.  Le fichier de grammaire obtenu peut être utilisé avec un fichier d’index pour générer des interprétations de requêtes de langage naturel.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Paramètre       | Description               |
|-----------------|---------------------------|
| `<xmlFile>`     | Chemin d’une spécification de grammaire XML d’entrée |
| `<grammarFile>` | Chemin d’un fichier de grammaire compilé de sortie         |

Ces fichiers peuvent être spécifiés par des chemins de fichiers locaux ou des chemins URL vers des objets blob Azure.  La spécification de grammaire décrit l’ensemble d’expressions de langage naturel traitées et leurs interprétations sémantiques (voir [Format de grammaire](GrammarFormat.md)).  Une fois la création terminée, le fichier de grammaire de sortie contient une représentation binaire de la spécification de grammaire pour permettre un décodage rapide.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service

La commande **host_service** héberge une instance du service KES sur l’ordinateur local.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Paramètre       | Description                |
|-----------------|----------------------------|
| `<grammarFile>` | Chemin du fichier de grammaire binaire d’entrée         |
| `<indexFile>`   | Chemin du fichier d’index binaire d’entrée           |
| `--port <port>` | Numéro de port local.  Par défaut : 8000 |

Ces fichiers peuvent être spécifiés par des chemins de fichiers locaux ou des chemins URL vers des objets blob Azure.  Un service web est hébergé dans http://localhost:&lt;port&gt;/.  Consultez [API Web](WebAPI.md) pour une liste des opérations prises en charge.

En dehors de l’environnement Azure, les services hébergés localement se limitent à des fichiers d’index pouvant aller jusqu’à 1 Mo, 10 requêtes par seconde et 1 000 appels au total.  Pour contourner ces limitations, exécutez la commande **host_service** dans une machine virtuelle Azure, ou déployez dans un service cloud Azure avec la commande **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service

La commande **deploy_service** déploie une instance du service KES dans un service cloud Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Paramètre       | Description                  |
|-----------------|------------------------------|
| `<grammarFile>` | Chemin du fichier de grammaire binaire d’entrée           |
| `<indexFile>`   | Chemin du fichier d’index binaire d’entrée             |
| `<serviceName>` | Nom du service cloud cible |
| `<vmSize>`      | Taille de la machine virtuelle du service cloud     |
| `--slot <slot>` | Emplacement du service cloud : « staging » (par défaut), « production » |

Ces fichiers peuvent être spécifiés par des chemins de fichiers locaux ou des chemins URL vers des objets blob Azure.  Le nom du service spécifie un service cloud Azure préconfiguré (voir [Création et déploiement d’un service cloud](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  La commande déploie automatiquement le service KES dans le service cloud Azure spécifié, avec les machines virtuelles de la taille spécifiée.  Pour éviter la pagination, qui diminue fortement les performances, nous vous recommandons d’utiliser une machine virtuelle avec 1 Go de RAM de plus que le fichier d’index d’entrée.  Pour obtenir la liste des tailles de machine virtuelle disponibles, consultez [Tailles des services cloud](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Par défaut, le service est déployé dans un environnement intermédiaire, ce qui peut être contourné via le paramètre --slot.  Consultez [API Web](WebAPI.md) pour une liste des opérations prises en charge.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>describe_index

La commande **describe_index** sort les informations d’un fichier d’index, dont le schéma et la description.

`kes.exe describe_index <indexFile>`

| Paramètre     | Description      |
|---------------|------------------|
| `<indexFile>` | Chemin de l’index d’entrée |

Ce fichier peut être spécifié par un chemin de fichier local ou URL vers un objet blob Azure.  La chaîne de description de la sortie peut être spécifiée à l’aide du paramètre --description de la commande **build_index**.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>describe_grammar

La commande **describe_grammar** sort la spécification de grammaire d’origine utilisée pour créer le fichier de grammaire binaire.

`kes.exe describe_grammar <grammarFile>`

| Paramètre       | Description      |
|-----------------|------------------|
| `<grammarFile>` | Chemin du fichier de grammaire d’entrée |

Ce fichier peut être spécifié par un chemin de fichier local ou URL vers un objet blob Azure.

