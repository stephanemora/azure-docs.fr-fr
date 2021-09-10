---
title: Charger des données d’utilisation sur Azure
description: Charger l’utilisation des données des services de données avec Azure Arc sur Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 74df592db61e4c9c50f9b199d7803fb8e1481878
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531799"
---
# <a name="upload-usage-data-to-azure"></a>Charger des données d’utilisation sur Azure

Vous pouvez exporter les informations d’utilisation périodiquement. L’exportation et le chargement de ces informations crée et met à jour le contrôleur de données, l’instance gérée SQL et les ressources de groupe de serveurs PostgreSQL Hyperscale dans Azure.

> [!NOTE] 
> Pendant la préversion, l’utilisation des services de données avec Azure Arc est gratuite.



> [!NOTE]
> Attendez au moins 24 heures après avoir créé le contrôleur de données Azure Arc avant de charger des données d’utilisation.

## <a name="create-service-principal-and-assign-roles"></a>Créer un principal du service et attribuer des rôles

Avant de continuer, assurez-vous que vous avez créé le principal du service requis et que vous l’avez affecté à un rôle approprié. Pour plus d'informations, consultez :
* [Créez un principal du service](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Affecter des rôles au principal du service](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Charger les données d’utilisation

Les informations d’utilisation telles que l’inventaire et l’utilisation des ressources peuvent être téléchargées sur Azure de la manière suivante :

1. Exportez les données d’utilisation à l’aide de la commande `az arcdata dc export`, comme suit :

> [!NOTE]
> L’exportation des informations d’utilisation/facturation, des métriques et des journaux à l’aide de la commande `az arcdata dc export` nécessite de contourner la vérification SSL pour l’instant.  Vous êtes invité à contourner la vérification SSL ou vous pouvez définir la variable d’environnement `AZDATA_VERIFY_SSL=no` pour éviter les invites.  Il n’existe actuellement aucun moyen de configurer un certificat SSL pour l’API d’exportation du contrôleur de données.

   ```azurecli
   az arcdata dc export --type usage --path usage.json --k8s-namespace <namespace> --use-k8s
   ```
 
   Cette commande crée un fichier `usage.json` avec toutes les ressources de données avec Azure Arc, comme les instances gérées SQL et les instances PostgreSQL Hyperscale, etc., qui sont créées sur le contrôleur de données.

2. Chargez les données d’utilisation à l’aide de la commande `upload`.

   ```azurecli
   az arcdata dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Automatisation des chargements (facultatif)

Si vous souhaitez charger les métriques et les journaux sur une base planifiée, vous pouvez créer un script et l’exécuter sur la base d’un minuteur toutes les quelques minutes. Voici un exemple d’automatisation des chargements à l’aide d’un script de shell Linux.

Dans votre éditeur de texte ou de code, ajoutez ce qui suit au script dans le fichier, puis enregistrez-le en tant que fichier exécutable de script, tel que `.sh` (Linux/Mac) ou `.cmd`, `.bat` ou `.ps1`.

```azurecli
az arcdata dc export --type usage --path usage.json --force --k8s-namespace <namespace> --use-k8s
az arcdata dc upload --path usage.json
```

Rendre le fichier de script exécutable

```console
chmod +x myuploadscript.sh
```

Exécutez le script tous les jours pour l’utilisation :

```console
watch -n 1200 ./myuploadscript.sh
```

Vous pouvez également utiliser un planificateur de travaux comme cron ou le Planificateur de tâches Windows ou encore un orchestrateur comme Ansible, Puppet ou Chef.

## <a name="next-steps"></a>Étapes suivantes

[Charger les métriques sur Azure Monitor](upload-metrics.md)

[Charger les journaux sur Azure Monitor](upload-logs.md)

[Charger des données de facturation dans Azure et les afficher dans le portail Azure](view-billing-data-in-azure.md)

[Afficher la ressource de contrôleur de données Azure Arc dans le portail Azure](view-data-controller-in-azure-portal.md)
