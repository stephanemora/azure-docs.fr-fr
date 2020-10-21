---
title: Charger les données d’utilisation, les métriques et les journaux sur Azure Monitor
description: Charger l’inventaire des ressources, les données d’utilisation, les métriques et les journaux sur Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 69a2bc002b2ccccba90bde5b6c92d162b6e78815
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952248"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Charger les données d’utilisation, les métriques et les journaux sur Azure Monitor

Vous pouvez régulièrement exporter les informations d’utilisation à des fins de facturation et de surveillance des métriques et des journaux, puis les charger sur Azure.  L’exportation et le chargement d’un de ces trois types de données a aussi pour effet de créer et mettre à jour le contrôleur de données, l’instance managée SQL et les ressources de groupe de serveurs PostgreSQL Hyperscale dans Azure.

> [!NOTE] 
> Il n’y a aucun coût pour les services de données compatibles avec Azure Arc pendant la période de la version préliminaire.

## <a name="prerequisites"></a>Prérequis

Vous aurez besoin des interfaces Azure CLI (AZ) et Azure Data CLI (azdata) installées.  [Installez les outils](./install-client-tools.md).

Avant de télécharger des données dans Azure, vous devez vous assurer que le fournisseur de ressources Microsoft.AzureData est inscrit dans votre abonnement Azure.

Vous pouvez le vérifier en exécutant la commande suivante :

```console
az provider show -n Microsoft.AzureData -o table
```

Si le fournisseur de ressources n’est pas actuellement inscrit dans votre abonnement, vous pouvez l’inscrire en exécutant la commande suivante.  Cette commande prend une minute ou deux pour s’exécuter.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Charger les données d’utilisation

Les informations d’utilisation telles que l’inventaire et l’utilisation des ressources peuvent être téléchargées sur Azure de la manière suivante :

1. Exportez les données d’utilisation à l’aide de la commande ```azdata export```, comme suit :

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Cette commande crée un fichier `usage.json` avec toutes les ressources de données Azure Arc activées, comme les instances managées SQL et les instances PostgreSQL Hyperscale, etc., qui sont créées sur le contrôleur de données.

2. Charger les données d’utilisation à l’aide de la commande ```azdata upload```

   > [!NOTE]
   > Veuillez attendre au moins 24 heures après avoir créé le contrôleur de données Azure Arc avant d’exécuter le chargement

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Charger des métriques et des journaux

Avec Azure Arc Data Services, vous pouvez éventuellement télécharger vos métriques et vos journaux sur Azure Monitor afin de pouvoir agréger et analyser les métriques et les journaux, déclencher des alertes, envoyer des notifications ou déclencher des actions automatisées. 

L’envoi de vos données à Azure Monitor vous permet également de stocker des données de surveillance et de journalisation hors site et à grande échelle, ce qui permet un stockage à long terme des données pour l’analytique avancée.

Si vous disposez de plusieurs sites avec Azure Arc Data Services, vous pouvez utiliser Azure Monitor comme emplacement central pour collecter tous les journaux et toutes les métriques de vos sites.

### <a name="before-you-begin"></a>Avant de commencer

Quelques étapes de configuration ponctuelles sont nécessaires pour activer les scénarios de chargement des journaux et des métriques :

1. Créez une application Azure Active Directory/un principal de service, y compris un secret d’accès client, et attribuez le principal de service au rôle « Éditeur de métriques d’analyse » sur les abonnements où se trouvent les ressources de votre instance de base de données.
2. Créez un espace de travail Log Analytics, récupérez les clés et définissez les informations dans les variables d’environnement.

Le premier élément est nécessaire pour télécharger les métriques et le deuxième pour charger les journaux.

Suivez ces commandes pour créer votre principal de service de chargement des métriques et l’affecter aux rôles « Éditeur de métriques de surveillance » et « Collaborateur » afin que le principal de service puisse charger les métriques et effectuer des opérations de création et de chargement.

## <a name="create-service-principal-and-assign-roles"></a>Créer un principal du service et attribuer des rôles

Suivez ces commandes pour créer votre principal de service de chargement de métriques et l’affecter au rôle « Serveur de publication de métriques de surveillance » :

Pour créer un principal de service, exécutez cette commande :

> [!NOTE]
> La création d’un principal de service nécessite [certaines autorisations dans Azure](/azure/active-directory/develop/howto-create-service-principal-portal#permissions-required-for-registering-an-app).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Exemple de sortie :

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Enregistrez les valeurs appId et tenant dans une variable d’environnement pour une utilisation ultérieure. 

Pour enregistrer les valeurs appId et de locataire avec PowerShell, suivez cet exemple :

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

Sur Linux ou macOS, vous pouvez également enregistrer les valeurs appId et de locataire avec cet exemple :

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Exécutez cette commande pour affecter le principal de service au rôle « Analyseur de métriques de surveillance » sur l’abonnement où se trouvent les ressources de votre instance de base de données :


> [!NOTE]
> Vous devez utiliser des guillemets doubles pour les noms de rôle lors de l’exécution à partir d’un environnement Windows.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Exemple de sortie :

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

Ensuite, exécutez ces commandes pour créer un espace de travail Log Analytics et définir les informations d’accès dans des variables d’environnement.

> [!NOTE]
> Si vous avez déjà un espace de travail, ignorez cette étape.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Exemple de sortie :

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Pour charger les métriques de vos instances managées SQL compatibles Azure Arc et les groupes de serveurs PostgreSQL Hyperscale compatibles Azure Arc, exécutez les commandes CLI suivantes :

1. Exportez toutes les métriques vers le fichier spécifié :

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Charger les métriques sur le Azure Monitor :

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Attendez au moins 30 minutes après la création des instances de données Azure Arc activées pour le premier chargement
   >
   >Veillez à `upload` les mesures immédiatement après `export`, car Azure Monitor accepte uniquement les métriques des 30 dernières minutes. [En savoir plus](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Si vous voyez des erreurs indiquant « Impossible d’obtenir les métriques » pendant l’exportation, vérifiez si la collecte de données est définie sur ```true``` en exécutant la commande suivante :

```console
azdata arc dc config show
```

et regardez sous la « section relative à la sécurité »

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Vérifiez si les propriétés `allowNodeMetricsCollection` et `allowPodMetricsCollection` sont définies sur `true`.

## <a name="view-the-metrics-in-the-portal"></a>Afficher les métriques dans le portail

Une fois vos métriques chargées, vous pouvez les afficher à partir du Portail Azure.
> [!NOTE]
> Notez que le traitement des données chargées peut prendre quelques minutes avant que vous puissiez afficher les métriques dans le portail.


Pour voir vos métriques dans le portail, utilisez ce lien pour ouvrir le portail : <https://portal.azure.com> Ensuite, recherchez le nom de votre instance de base de données dans la barre de recherche :

Vous pouvez voir l’utilisation du processeur sur la page de vue d’ensemble ou, si vous souhaitez des métriques plus détaillées, vous pouvez cliquer sur les métriques dans le panneau de navigation de gauche.

Choisissez SQL Server comme espace de noms de métrique :

Sélectionnez la métrique que vous souhaitez voir (vous pouvez également en sélectionner plusieurs) :

Modifiez la fréquence pour la définir sur les 30 dernières minutes :

> [!NOTE]
> Vous ne pouvez charger les métriques que pour les 30 dernières minutes. Azure Monitor rejette les métriques datant de plus de 30 minutes.

## <a name="upload-logs-to-azure-monitor"></a>Charger les journaux sur Azure Monitor

 Pour charger des journaux pour vos instances managées SQL compatibles Azure Arc et groupes de serveurs Azure Arc PostgreSQL Hyperscale, exécutez les commandes CLI suivantes :

1. Exporter tous les journaux dans le fichier spécifié :

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Chargez les journaux sur un espace de travail Azure Monitor Log Analytics :

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Afficher vos journaux dans le portail Azure

Une fois vos journaux chargés, vous devez être en mesure de les interroger à l’aide de l’explorateur de requêtes de journal comme suit :

1. Ouvrez le portail Azure, recherchez le nom de votre espace de travail dans la barre de recherche en haut, puis sélectionnez-le.
2. Dans le panneau gauche, cliquez sur Journaux
3. Cliquez sur Prise en main (ou cliquez sur les liens de la page Prise en main pour en savoir plus sur Log Analytics si vous débutez avec cette fonctionnalité)
4. Suivez le tutoriel pour en savoir plus sur Log Analytics si vous utilisez Log Analytics pour la première fois
5. Développez Journaux personnalisés au bas de la liste des tables et vous verrez une table appelée « sql_instance_logs_CL ».
6. Cliquez sur l’icône représentant un œil en regard du nom de la table
7. Cliquez sur le bouton « Afficher dans l’éditeur de requête »
8. Vous disposez maintenant d’une requête dans l’éditeur de requête, qui affiche les 10 événements les plus récents dans le journal
9. À partir de là, vous pouvez expérimenter l’interrogation des journaux à l’aide de l’éditeur de requête, définir des alertes, etc.

## <a name="automating-uploads-optional"></a>Automatisation des chargements (facultatif)

Si vous souhaitez charger les métriques et les journaux sur une base planifiée, vous pouvez créer un script et l’exécuter sur la base d’un minuteur toutes les quelques minutes. Voici un exemple d’automatisation des chargements à l’aide d’un script de shell Linux.

Dans votre éditeur de texte ou de code, ajoutez ce qui suit au script dans le fichier, puis enregistrez-le en tant que fichier exécutable de script, tel que. sh (Linux/Mac) ou. cmd, .bat, .ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Rendre le fichier de script exécutable

```console
chmod +x myuploadscript.sh
```

Exécutez le script toutes les 20 minutes :

```console
watch -n 1200 ./myuploadscript.sh
```

Vous pouvez également utiliser un planificateur de travaux comme cron ou le Planificateur de tâches Windows ou encore un orchestrateur comme Ansible, Puppet ou Chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Conseils généraux sur l’exportation et le chargement des métriques d’utilisation

Les opérations de création, lecture, mise à jour et suppression (CRUD) sur les services de données compatibles Azure Arc sont journalisées à des fins de facturation et de surveillance. Il existe des services en arrière-plan qui surveillent ces opérations CRUD et calculent la consommation de manière appropriée. Le calcul réel de l’utilisation ou de la consommation se produit de manière planifiée et est effectué en arrière-plan. 

Pendant la préversion, ce processus se produit la nuit. La recommandation générale est de charger les données d’utilisation une seule fois par jour. Lorsque les informations d’utilisation sont exportées et chargées plusieurs fois au cours de la même période de 24 heures, seul l’inventaire des ressources est mis à jour dans le Portail Azure mais pas dans l’utilisation des ressources.

Pour le chargement des mesures, Azure Monitor accepte uniquement les 30 dernières minutes de données ([En savoir plus](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). La recommandation pour le chargement des métriques est de charger les métriques immédiatement après la création du fichier d’exportation afin de pouvoir afficher l’ensemble du jeu de données dans le Portail Azure. Par exemple, supposons que vous avez exporté les métriques à 14 h et que vous avez exécuté la commande de chargement à 14 h 50. Étant donné qu’Azure Monitor n’accepte que les données des 30 dernières minutes, vous ne verrez aucune donnée dans le portail. 

## <a name="next-steps"></a>Étapes suivantes

[Charger des données de facturation dans Azure et les afficher dans le portail Azure](view-billing-data-in-azure.md)

[Afficher la ressource de contrôleur de données Azure Arc dans le portail Azure](view-data-controller-in-azure-portal.md)
