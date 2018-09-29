---
title: Fonctionnalités d’Azure Cloud Shell | Microsoft Docs
description: Vue d’ensemble des fonctionnalités de Bash dans Azure Cloud Shell
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: juluk
ms.openlocfilehash: 2184d006bb99d57f50e71c0096d68b7a0b69a219
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162285"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Fonctionnalités et outils pour Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell s’exécute sur `Ubuntu 16.04 LTS`.

## <a name="features"></a>Caractéristiques

### <a name="secure-automatic-authentication"></a>Authentification automatique sécurisée

Cloud Shell authentifie automatiquement et de manière sécurisée l’accès aux comptes pour Azure CLI et Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>Persistance de $Home entre les sessions

Pour conserver les fichiers entre les sessions, Cloud Shell vous guide à travers le processus d’association d’un partage de fichiers Azure au premier lancement.
Par la suite, Cloud Shell associera automatiquement votre espace de stockage (monté sous forme de `$Home\clouddrive`) pour toutes les sessions à venir.
De plus, votre répertoire `$Home` est conservé en tant que fichier .img dans votre partage de fichiers Azure.
Les fichiers en dehors de `$Home` et de l’état de la machine ne sont pas conservés entre les sessions. Utilisez les bonnes pratiques lors du stockage de secrets comme des clés SSH. [Des tutoriels existent pour la configuration de services comme Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Découvrez plus d’informations sur les fichiers persistants dans Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Lecteur Azure (Azure :)

PowerShell dans Cloud Shell vous démarre dans le lecteur Azure (`Azure:`).
Le lecteur Azure facilite la détection et la navigation dans des ressources Azure, comme Calcul, Réseau, ou Stockage, de façon similaire à la navigation dans un système de fichiers.
Vous pouvez continuer à utiliser les [applets de commande Azure PowerShell](https://docs.microsoft.com/powershell/azure) que vous connaissez pour gérer ces ressources quel que soit le lecteur où vous vous trouvez.
Toutes les modifications apportées aux ressources Azure, effectuées directement dans le portail Azure ou via des applets de commande Azure PowerShell, sont répercutées dans le lecteur Azure.  Vous pouvez exécuter `dir -Force` pour actualiser vos ressources.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>Étroite intégration à des outils open source

Cloud Shell inclut une authentification préconfigurée pour des outils open source comme Terraform, Ansible et Chef InSpec. Faites un essai à partir des exemples de procédures pas à pas.

## <a name="tools"></a>Outils

|Catégorie   |NOM   |
|---|---|
|Outils Linux            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Outils Azure            |[Azure CLI](https://github.com/Azure/azure-cli) et [Azure Classic CLI](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Interface de ligne de commande de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) |
|Éditeurs de texte           |vim<br> nano<br> emacs       |
|Contrôle de code source         |git                    |
|Outils de génération            |make<br> maven<br> npm<br> pip         |
|Containers             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI DC/OS](https://github.com/dcos/dcos-cli)         |
|Bases de données              |Client MySQL<br> Client PostgreSQL<br> [Utilitaire sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Autres                  |Client iPython<br> [CLI Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>Support multilingue

|Langage   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.1.0-preview.4](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 et 3.5 (par défaut)|

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Bash dans Cloud Shell](quickstart.md) <br>
[Démarrage rapide de PowerShell dans Cloud Shell](quickstart-powershell.md) <br>
[En savoir plus sur Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[En savoir plus sur Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
