---
title: Installer les outils clients
description: Installez azdata, kubectl, Azure CLI, psql, Azure Data Studio (Insiders) et l’extension Arc pour Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3eb41b868aba7e7350f54594fa43f01eeeabebc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90930756"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Installez les outils clients pour le déploiement et la gestion des services de données activés par Azure Arc

> [!IMPORTANT]
> Si vous effectuez une mise à jour vers une nouvelle mise en production mensuelle, veillez également à opérer une mise à jour vers la dernière version d’Azure Data Studio, l’outil d’interface de ligne de commande d’Azure Data CLI (azdata), l’interface de ligne de commande Azure Data CLI et les extensions Azure Arc pour Azure Data Studio.

Ce document vous guide au fil des étapes d’installation de l’interface de ligne de commande Azure Data (azdata), d’Azure Data Studio, d’Azure CLI (AZ) et de l’outil d’interface de ligne de commande Kubernetes (kubectl) sur votre ordinateur client.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Outils de création et de gestion des services de données activés par Azure Arc 

Le tableau suivant répertorie les outils courants requis pour créer et gérer les services de données activés par Azure Arc, et indique comment installer ces outils :

| Outil | Obligatoire | Description | Installation |
|---|---|---|---|
| Interface de ligne de commande Azure Data (azdata) | Oui | Outil en ligne de commande pour l’installation et la gestion d’un cluster Big Data. L’interface de ligne de commande Azure Data comprend également un utilitaire de ligne de commande permettant de se connecter à Azure SQL, aux instances SQL Server et aux serveurs Postgres, ainsi que de les interroger à l’aide des commandes `azdata sql query` (exécution d’une requête unique à partir de la ligne de commande), `azdata sql shell` (interpréteur de commandes interactif), `azdata postgres query` et `azdata postgres shell`. | [Installer](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Oui | Outil offrant une expérience riche pour la connexion à une série de bases de données, dont Azure SQL, SQL Server, PostrgreSQL et MySQL, ainsi que leur interrogation. Les extensions d’Azure Data Studio fournissent une expérience d’administration pour les services de données activés par Azure Arc. | [Installer](https://aka.ms/getazuredatastudio) |
| Extension d’interface de ligne de commande Azure Data pour Azure Data Studio | Oui | Extension pour Azure Data Studio qui installe l’interface de ligne de commande Azure Data si vous n’en disposez pas encore.| Installez à partir de la galerie d’extensions dans Azure Data Studio.|
| Extension Azure Arc pour Azure Data Studio | Oui | Extension pour Azure Data Studio, qui fournit une expérience de gestion pour les services de données activés par Azure Arc. Il existe une dépendance de l’extension d’interface de ligne de commande Azure Data pour Azure Data Studio. | Installez à partir de la galerie d’extensions dans Azure Data Studio.|
| Extension PostgreSQL dans Azure Data Studio | Non | Extension PostgreSQL pour Azure Data Studio, qui fournit des fonctionnalités de gestion pour PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Installez à partir de la galerie d’extensions dans Azure Data Studio.|
| Azure CLI (az)<sup>1</sup> | Oui | Interface de ligne de commande moderne pour la gestion des services Azure. Utilisée avec des déploiements AKS et pour charger dans Azure les données d’inventaire et de facturation des services de données activés par Azure Arc. ([Informations supplémentaires](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Installer](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Interface de ligne de commande Kubernetes (kubectl)<sup>2</sup> | Oui | Outil en ligne de commande pour gérer le cluster Kubernetes ([informations supplémentaires](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | Requis pour certains exemples de scripts. | Outil en ligne de commande pour transférer des données avec des URL. | [Windows](https://curl.haxx.se/windows/) \| Linux : installer le package d’installation |
| oc | Requis pour les déploiements Red Hat OpenShift et Azure RedHat OpenShift. |`oc` est l’interface de ligne de commande (CLI) Shift ouvert. | [Installation de l’interface CLI](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Vous devez utiliser Azure CLI versions 2.0.4 ou ultérieures. Exécutez `az --version` pour trouver la version, si nécessaire.

<sup>2</sup> Vous devez utiliser `kubectl` versions 1.13 ou ultérieures. En outre, la version de `kubectl` doit être une version mineure plus une ou moins une de votre cluster Kubernetes. Si vous souhaitez installer une version spécifique sur le client `kubectl`, consultez [Installer le binaire `kubectl` via curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (sur Windows 10, utilisez cmd.exe et non Windows PowerShell pour exécuter curl).

<sup>3</sup> si vous utilisez PowerShell, curl est un alias pour la cmdlet Invoke-WebRequest.

## <a name="next-steps"></a>Étapes suivantes

[Créer le contrôleur de données Azure Arc](create-data-controller.md)
