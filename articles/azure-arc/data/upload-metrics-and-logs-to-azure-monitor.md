---
title: Charger l’inventaire des ressources, les données d’utilisation, les métriques et les journaux sur Azure Monitor
description: Charger l’inventaire des ressources, les données d’utilisation, les métriques et les journaux sur Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930180"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Charger l’inventaire des ressources, les données d’utilisation, les métriques et les journaux sur Azure Monitor

Avec Azure Arc Data Services, vous pouvez *éventuellement* télécharger vos métriques et vos journaux sur Azure Monitor afin de pouvoir agréger et analyser les métriques et les journaux, déclencher des alertes, envoyer des notifications ou déclencher des actions automatisées. L’envoi de vos données à Azure Monitor vous permet également de stocker des données de surveillance et de journalisation hors site et à grande échelle, ce qui permet un stockage à long terme des données pour l’analytique avancée.  Si vous disposez de plusieurs sites avec Azure Arc Data Services, vous pouvez utiliser Azure Monitor comme emplacement central pour collecter tous les journaux et toutes les métriques de vos sites.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Avant de commencer

Quelques étapes de configuration ponctuelles sont nécessaires pour activer les scénarios de chargement des journaux et des métriques :

1) Créez une application de principal de service/Azure Active Directory, avec la création d’un secret d’accès client et attribuez le principal du service au rôle « Analyseur de métriques de surveillance » sur les abonnements où se trouvent les ressources de votre instance de base de données.
2) Créez un espace de travail Log Analytics, récupérez les clés et définissez les informations dans les variables d’environnement.

Le premier élément est nécessaire pour télécharger les métriques et le deuxième pour charger les journaux.

Suivez ces commandes pour créer votre principal de service de chargement des métriques et l’affecter aux rôles « Éditeur de métriques de surveillance » et « Collaborateur » afin que le principal de service puisse charger les métriques et effectuer des opérations de création et de chargement.

## <a name="create-service-principal-and-assign-roles"></a>Créer un principal du service et attribuer des rôles

Suivez ces commandes pour créer votre principal de service de chargement de métriques et l’affecter au rôle « Serveur de publication de métriques de surveillance » :

Pour créer un principal de service, exécutez cette commande :

> [!NOTE]
> La création d’un principal de service nécessite [certaines autorisations dans Azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Exemple de sortie :

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Enregistrez les valeurs appId et tenant dans une variable d’environnement pour une utilisation ultérieure :

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Exécutez cette commande pour affecter le principal de service au rôle « Analyseur de métriques de surveillance » sur l’abonnement où se trouvent les ressources de votre instance de base de données :

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Exemple de sortie :

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

Ensuite, exécutez ces commandes pour créer un espace de travail Log Analytics et définir les informations d’accès dans des variables d’environnement.

> [!NOTE]
> Si vous avez déjà un espace de travail, ignorez cette étape.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Exemple de sortie :

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Affecter un ID et une clé partagée à des variables d’environnement

Enregistrez le customerId (ID de l’espace de travail) en tant que variable d’environnement à utiliser ultérieurement :

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Cette commande permet d’imprimer les clés d’accès requises pour se connecter à votre espace de travail Log Analytics :

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Exemple de sortie :

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Enregistrez la clé primaire dans une variable d’environnement à utiliser ultérieurement :

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Définissez les variables d’environnement finales et confirmez-les

Définissez l’URL de l’autorité du SPN dans une variable d’environnement :

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Assurez-vous que toutes les variables d’environnement nécessaires sont définies si vous le souhaitez :

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Charger les métriques sur le Azure Monitor

Pour charger les métriques de vos instances managées Azure SQL et les exécutions de groupes de serveurs Azure Database pour PostgreSQL Hyperscale, exécutez les commandes CLI suivantes :

Cette opération exporte toutes les métriques vers le fichier spécifié :

```console
azdata arc dc export -t metrics --path metrics.json
```

Cette opération charge les métriques sur Azure Monitor :

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Afficher les métriques dans le portail

Une fois vos métriques chargées, vous devez être en mesure de les visualiser à partir du portail Azure.

Pour voir vos métriques dans le portail, utilisez ce lien spécial pour ouvrir le portail : <https://portal.azure.com> Ensuite, recherchez le nom de votre instance de base de données dans la barre de recherche :

Vous pouvez voir l’utilisation du processeur sur la page de vue d’ensemble ou, si vous souhaitez des métriques plus détaillées, vous pouvez cliquer sur les métriques dans le panneau de navigation de gauche.

Choisissez SQL Server comme espace de noms de métrique :

Sélectionnez la métrique que vous souhaitez voir (vous pouvez également en sélectionner plusieurs) :

Modifiez la fréquence pour la définir sur les 30 dernières minutes :

> [!NOTE]
> Vous ne pouvez charger les métriques que pour les 30 dernières minutes. Azure Monitor rejette les métriques datant de plus de 30 minutes.

## <a name="upload-logs-to-azure-monitor"></a>Charger les journaux sur Azure Monitor

 Pour charger les journaux de vos instances managées Azure SQL et les exécutions de groupes de serveurs Azure Database pour PostgreSQL Hyperscale, exécutez les commandes CLI suivantes :

Cette opération exporte tous les journaux vers le fichier spécifié :

```console
azdata arc dc export -t logs --path logs.json
```

Cette opération charge les journaux sur un espace de travail Azure Monitor Log Analytics :

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Afficher vos journaux dans le portail Azure

Une fois vos journaux chargés, vous devez être en mesure de les interroger à l’aide de l’explorateur de requêtes de journal comme suit :

1. Ouvrez le portail Azure, recherchez le nom de votre espace de travail dans la barre de recherche en haut, puis sélectionnez-le.
2. Dans le panneau gauche, cliquez sur Journaux
3. Cliquez sur Prise en main (ou cliquez sur les liens de la page Prise en main pour en savoir plus sur Log Analytics si vous débutez avec cette fonctionnalité)
4. Suivez le tutoriel pour en savoir plus sur Log Analytics si vous utilisez cette technologie pour la première fois
5. Développez Journaux personnalisés au bas de la liste des tables et vous verrez une table appelée « sql_instance_logs_CL ».
6. Cliquez sur l’icône représentant un œil en regard du nom de la table
7. Cliquez sur le bouton « Afficher dans l’éditeur de requête »
8. Vous disposez maintenant d’une requête dans l’éditeur de requête, qui affiche les 10 événements les plus récents dans le journal
9. À partir de là, vous pouvez expérimenter l’interrogation des journaux à l’aide de l’éditeur de requête, définir des alertes, etc.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Automatisation des chargements de métriques et des journaux (facultatif)

Si vous souhaitez charger constamment les métriques et les journaux, vous pouvez créer un script et l’exécuter sur un minuteur toutes les minutes.  Voici un exemple d’automatisation des chargements à l’aide d’un script de shell Linux.

Dans votre éditeur de texte ou de code, ajoutez ce qui suit au contenu du script dans le fichier, puis enregistrez-le en tant que fichier exécutable de script, tel que. sh (Linux/Mac) ou. cmd, .bat, .ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Rendre le fichier de script exécutable

```console
chmod +x myuploadscript.sh
```

Exécutez le script toutes les 2 minutes :

```console
watch -n 120 ./myuploadscript.sh
```

Vous pouvez également utiliser un planificateur de travaux comme cron ou le Planificateur de tâches Windows ou encore un orchestrateur comme Ansible, Puppet ou Chef.
