---
title: Installer les outils clients
description: Installez azdata, kubectl, Azure CLI, psql, Azure Data Studio (Insiders) et l’extension Arc pour Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e328c65eb7453a8e80faa315c0b038e8fa6f13e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563243"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Installer les outils clients pour le déploiement et la gestion des services de données avec Azure Arc

Cet article vous indique les ressources permettant d’installer les outils de gestion des services de données avec Arc.

> [!IMPORTANT]
> Si vous effectuez une mise à jour vers une nouvelle version, mettez à jour vers la dernière version d’Azure Data Studio, l’extension Azure Arc pour Azure Data Studio, l’interface de ligne de commande (CLI) Azure (`az`) et [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)].
>
> [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)] 

L’extension [`arcdata` pour l’interface Azure CLI (`az`)](reference/reference-az-arcdata-dc.md) remplace `azdata` pour les services de données avec Arc.

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Outils de création et de gestion des services de données avec Azure Arc

Le tableau suivant répertorie les outils courants requis pour créer et gérer les services de données avec Azure Arc, et indique comment installer ces outils :

| Outil | Obligatoire | Description | Installation |
|---|---|---|---|
| Azure CLI (`az`)<sup>1</sup> | Oui | Interface de ligne de commande moderne pour la gestion des services Azure. Utilisée pour gérer les services Azure en général, ainsi que spécifiquement les services de données avec Arc à l’aide de l’interface CLI ou dans des scripts pour le mode de connexion indirecte (disponible maintenant) et le mode de connexion directe (bientôt disponible). ([Informations supplémentaires](/cli/azure/)). | [Installer](/cli/azure/install-azure-cli) |
| Extension `arcdata` pour l’interface Azure (`az`) CLI | Yes | Outil en ligne de commande pour la gestion des services de données avec Arc en tant qu’extension de l’interface Azure CLI (`az`) | [Installer](install-arcdata-extension.md) |
| Azure Data Studio | Oui | Outil offrant une expérience riche pour la connexion à une série de bases de données, dont Azure SQL, SQL Server, PostrgreSQL et MySQL, ainsi que leur interrogation. Les extensions d’Azure Data Studio fournissent une expérience d’administration pour les services de données avec Azure Arc. | [Installer](/sql/azure-data-studio/download-azure-data-studio) |
| Extension Azure Arc pour Azure Data Studio | Oui | Extension pour Azure Data Studio, qui fournit une expérience de gestion pour les services de données avec Azure Arc.| Installez à partir de la galerie d’extensions dans Azure Data Studio.|
| Extension PostgreSQL dans Azure Data Studio | Non | Extension PostgreSQL pour Azure Data Studio, qui fournit des fonctionnalités de gestion pour PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Installez à partir de la galerie d’extensions dans Azure Data Studio.|
| Interface de ligne de commande Kubernetes (kubectl)<sup>2</sup> | Oui | Outil en ligne de commande pour gérer le cluster Kubernetes ([informations supplémentaires](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | Requis pour certains exemples de scripts. | Outil en ligne de commande pour transférer des données avec des URL. | [Windows](https://curl.haxx.se/windows/) \| Linux : installer le package d’installation |
| oc | Requis pour les déploiements Red Hat OpenShift et Azure RedHat OpenShift. |`oc` est l’interface de ligne de commande (CLI) Shift ouvert. | [Installation de l’interface CLI](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Vous devez utiliser Azure CLI version 2.26.0 ou ultérieure. Exécutez `az --version` pour trouver la version, si nécessaire.

<sup>2</sup> Vous devez utiliser `kubectl` version 1.19 ou ultérieure. En outre, la version de `kubectl` doit être une version mineure plus une ou moins une de votre cluster Kubernetes. Si vous souhaitez installer une version spécifique sur le client `kubectl`, consultez [Installer le binaire `kubectl` via curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (sur Windows 10, utilisez cmd.exe et non Windows PowerShell pour exécuter curl).

<sup>3</sup> si vous utilisez PowerShell, curl est un alias pour la cmdlet Invoke-WebRequest.

## <a name="next-steps"></a>Étapes suivantes

[Créer le contrôleur de données Azure Arc](create-data-controller.md)
