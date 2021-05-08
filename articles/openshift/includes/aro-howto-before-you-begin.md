---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: d888d4df3f578bf88f8f38cb979ed70f681c64e8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108157620"
---
### <a name="create-the-cluster"></a>Créer le cluster

Suivez le tutoriel pour [Créer un cluster Azure Red Hat OpenShift](../tutorial-create-cluster.md). Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce tutoriel vous demande d’exécuter Azure CLI version 2.6.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Se connecter au cluster

Pour gérer un cluster Azure Red Hat OpenShift, vous utilisez [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html), le client de ligne de commande OpenShift.

> [!NOTE]
> Nous vous recommandons d’[installer la ligne de commande OpenShift](../tutorial-connect-cluster.md) sur [Azure Cloud Shell](https://shell.azure.com/) et de l’utiliser pour toutes les opérations de ligne de commande ci-dessous. Lancez votre interpréteur de commandes à partir de shell.azure.com ou en cliquant sur le lien suivant :
>
> [![Lancement de l’incorporation](/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Lancement d’Azure Cloud Shell")](https://shell.azure.com/bash)

Suivez le didacticiel pour installer l’interface CLI, récupérer les informations d’identification du cluster et [vous connecter au cluster](../tutorial-connect-cluster.md) à l’aide de la console web et de l’interface CLI OpenShift.

Une fois que vous êtes connecté, vous devriez voir un message indiquant que vous utilisez le projet `default`.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```