---
title: Configurer et utiliser des métriques et des journaux de diagnostic avec un hub IoT Azure
description: Découvrez comment configurer et utiliser des métriques et des journaux de diagnostic à l’aide d’un hub Azure IoT. Cela vous permet de disposer de données à analyser pour faciliter le diagnostic des problèmes de votre hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: e6a2057eee6cf294e401f60c889bcf58ac6fbe32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429101"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Tutoriel : Configurer et utiliser des métriques et des journaux de diagnostic avec un hub IoT

Si vous avez une solution IoT Hub en cours de production, il est conseillé de configurer certaines métriques et d’activer les journaux de diagnostic. Si un problème survient, vous disposez alors de données qui vous aident à diagnostiquer le problème et à le résoudre plus rapidement. Dans cet article, vous allez voir comment activer les journaux de diagnostic et rechercher les éventuelles erreurs qu’ils contiennent. Vous allez également configurer certaines métriques à surveiller et des alertes qui se déclenchent quand ces métriques atteignent une certaine limite. Par exemple, vous pouvez recevoir un e-mail quand le nombre de messages de télémétrie envoyés dépasse une limite spécifique ou quand le nombre de messages utilisés se rapproche du quota de messages autorisés par jour pour le hub IoT. 

Pour illustrer cela, citons l’exemple d’une station-service où les pompes sont des appareils IoT qui envoient des messages à un hub IoT. Les cartes de crédit sont validées, et la transaction finale est écrite dans un magasin de données. Si les appareils IoT cessent de se connecter au hub et d’envoyer des messages, il est beaucoup plus difficile de remédier au problème car vous n’avez aucune visibilité sur ce qui se passe.

Ce tutoriel utilise l’exemple Azure du [routage IoT Hub](tutorial-routing.md) pour envoyer des messages au hub IoT.

Dans ce didacticiel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un hub IoT, un appareil simulé et un compte de stockage avec Azure CLI.  
> * Activer les journaux de diagnostics.
> * Activer les métriques.
> * Configurer des alertes pour ces métriques. 
> * Télécharger et exécuter une application qui simule l’envoi de messages par un appareil IoT au hub. 
> * Exécuter l’application jusqu’à ce que des alertes se déclenchent. 
> * Afficher les résultats des métriques et examiner les journaux de diagnostic. 

## <a name="prerequisites"></a>Conditions préalables requises

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Installer [Visual Studio](https://www.visualstudio.com/). 

- Un compte e-mail capable de recevoir des e-mails.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurer des ressources

Pour ce tutoriel, vous avez besoin d’un hub IoT, d’un compte de stockage et d’un appareil IoT simulé. Ces ressources peuvent être créées avec Azure CLI ou Azure PowerShell. Utilisez le même groupe de ressources et le même emplacement pour toutes les ressources. Puis à la fin, vous pouvez supprimer tous les éléments en une seule fois en supprimant le groupe de ressources.

Voici les étapes à suivre.

1. Créez un [groupe de ressources](../azure-resource-manager/management/overview.md). 

2. Créez un hub IoT.

3. Créez un compte de stockage V1 standard avec la réplication Standard_LRS.

4. Créez une identité d’appareil pour l’appareil simulé qui envoie des messages à votre Hub. Enregistrez la clé pour la phase de test.

### <a name="set-up-resources-using-azure-cli"></a>Configurer des ressources à l’aide d’Azure CLI

Copiez et collez ce script dans Cloud Shell. En supposant que vous êtes déjà connecté, il exécute le script, ligne par ligne. Les nouvelles ressources sont créées dans le groupe de ressources ContosoResources.

Les variables qui doivent être globalement uniques sont concaténées avec la chaîne `$RANDOM`. Quand le script est exécuté et que les variables sont définies, une chaîne numérique aléatoire est générée et ajoutée à la fin de la chaîne fixe, la rendant ainsi unique.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Quand vous créez l’identité d’appareil, vous pouvez recevoir l’erreur suivante : *Aucune clé trouvée pour la stratégie iothubowner du hub IoT ContosoTestHub*. Pour corriger cette erreur, mettez à jour l’extension Azure CLI IoT et réexécutez les deux dernières commandes dans le script. 
>
>Voici la commande pour mettre à jour l’extension. Exécutez-la dans votre instance Cloud Shell.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Activer les journaux de diagnostic 

Les [journaux de diagnostic](../azure-monitor/platform/resource-logs-overview.md) sont désactivés par défaut quand vous créez un hub IoT. Dans cette section, activez les journaux de diagnostic pour votre hub.

1. Pour commencer, si vous n’êtes pas déjà sur votre hub dans le portail, cliquez sur **Groupe de ressources**, puis sur le groupe de ressources Contoso-Resources. Sélectionnez le hub dans la liste des ressources affichées. 

2. Recherchez la section **Supervision** dans le panneau IoT Hub. Cliquez sur **Paramètres de diagnostic**. 

   ![Capture d’écran montrant la partie des paramètres de diagnostic du panneau IoT Hub.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Vérifiez que l’abonnement et le groupe de ressources sont corrects. Sous **Type de ressource**, décochez **Tout sélectionner**, puis recherchez et cochez **IoT Hub**. (Ignorez la coche qui réapparaît à côté de *Tout sélectionner*.) Sous **Ressource**, sélectionnez le nom du hub. Votre écran doit ressembler à cette image : 

   ![Capture d’écran montrant la partie des paramètres de diagnostic du panneau IoT Hub.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Cliquez à présent sur **Activer les diagnostics**. Le volet Paramètres de diagnostic s’affiche. Spécifiez « diags-hub » comme nom des paramètres de vos journaux de diagnostic.

5. Cochez **Archive vers un compte de stockage**. 

   ![Capture d’écran montrant comment définir l’archivage des diagnostics dans un compte de stockage.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Cliquez sur **Configurer** pour accéder à l’écran **Sélectionner un compte de stockage**, sélectionnez le compte adapté (*contosostoragemon*), puis cliquez sur **OK** pour revenir au volet Paramètres de diagnostic. 

   ![Capture d’écran montrant comment définir l’archivage des journaux de diagnostics dans un compte de stockage.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Sous **JOURNAL**, cochez **Connexions** et **Télémétrie d’appareil**, puis indiquez une période de **Rétention (jours)** de 7 jours pour les deux. L’écran Paramètres de diagnostic doit maintenant ressembler à cette image :

   ![Capture d’écran montrant la configuration finale des journaux de diagnostic.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Cliquez sur **Enregistrer** pour enregistrer les paramètres. Fermez le volet Paramètres de diagnostic.

Plus tard, vous pourrez voir dans les journaux de diagnostic la journalisation des opérations de connexion et de déconnexion de l’appareil. 

## <a name="set-up-metrics"></a>Configurer des métriques 

Nous allons à présent configurer certaines métriques à surveiller quand des messages sont envoyés au hub. 

1. Dans le volet des paramètres du hub IoT, cliquez sur l’option **Métriques** dans la section **Supervision**.

2. En haut de l’écran, cliquez sur **Dernières 24 heures (automatique)** . Dans la liste déroulante qui s’affiche, sélectionnez **4 dernières heures** sous **Intervalle de temps**, **1 minute** sous **Granularité temporelle**, et l’heure locale. Cliquez sur **Appliquer** pour enregistrer ces paramètres. 

   ![Capture d’écran montrant les paramètres horaires des métriques.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Une entrée de métrique est présente par défaut. Conservez le groupe de ressources par défaut et l’espace de noms de la métrique. Dans la liste déroulante **Métrique**, sélectionnez **Messages de télémétrie envoyés**. Sous **Agrégation**, sélectionnez **Somme**.

   ![Capture d’écran montrant l’ajout d’une métrique pour les messages de télémétrie envoyés.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Cliquez maintenant sur **Ajouter une métrique** pour ajouter une autre métrique au graphique. Sélectionnez votre groupe de ressources (**ContosoTestHub**). Sous **Métrique**, sélectionnez **Nombre total de messages utilisés**. Sous **Agrégation**, sélectionnez **Moy**. 

   Votre écran montre maintenant la métrique réduite pour *Messages de télémétrie envoyés* ainsi que la nouvelle métrique pour *Nombre total de messages utilisés*.

   ![Capture d’écran montrant l’ajout d’une métrique pour les messages de télémétrie envoyés.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Cliquez sur **Épingler au tableau de bord**. Elle est alors épinglée au tableau de bord de votre portail Azure pour que vous puissiez y accéder à nouveau. Si vous ne l’épinglez pas au tableau de bord, vos paramètres ne sont pas conservés.

## <a name="set-up-alerts"></a>Configurer des alertes

Accédez au hub dans le portail. Cliquez sur **Groupes de ressources**, puis sélectionnez *ContosoResources* et le hub IoT *ContosoTestHub*. 

IoT Hub ne prenant pas encore en charge les [métriques dans Azure Monitor](/azure/azure-monitor/platform/data-collection#metrics), vous devez utiliser des [alertes classiques](/azure/azure-monitor/platform/alerts-classic.overview).

1. Sous **Supervision**, cliquez sur **Alertes** pour afficher l’écran principal des alertes. 

   ![Capture d’écran montrant comment trouver les alertes classiques.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Pour accéder aux alertes classiques à partir de là, cliquez sur **Afficher les alertes classiques**. 

    ![Capture d’écran montrant l’écran des alertes classiques.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Renseignez les champs : 

    **Abonnement**: laissez ce champ configuré avec votre abonnement actuel.

    **Source** : affectez *Métriques* à ce champ.

    **Groupe de ressources** : affectez votre groupe de ressources actuel (*ContosoResources*) à ce champ. 

    **Type de ressource** : affectez IoT Hub à ce champ. 

    **Ressource** : sélectionnez votre hub IoT (*ContosoTestHub*).

3. Cliquez sur **Ajouter une alerte métrique (classique)** pour configurer une nouvelle alerte.

    Renseignez les champs :

    **Name** : entrez un nom pour votre règle d’alerte, par exemple *telemetry-messages*.

    **Description** : entrez la description de votre alerte, par exemple *alert when there are 1000 telemetry messages sent* (alerte quand 1 000 messages de télémétrie sont envoyés). 

    **Source** : Affectez la valeur *Métriques*.

    Définissez **Abonnement**, **Groupe de ressources**, et **Ressource** avec les valeurs que vous avez sélectionnées dans l’écran **Afficher les alertes classiques**. 

    Affectez **Messages de télémétrie envoyés** à *Métrique*.

    ![Capture d’écran montrant la configuration d’une alerte classique pour les messages de télémétrie envoyés.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Après le graphique, définissez les champs suivants :

   **Condition** : choisissez *Supérieur à*.

   **Seuil** : indiquez la valeur 1000.

   **Période** : choisissez *Au cours des 5 dernières minutes*.

   **Destinataires des e-mails de notification** : entrez votre adresse e-mail. 

   ![Capture d’écran montrant la partie inférieure de l’écran d’alertes.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Cliquez sur **OK** pour enregistrer l’alerte. 

5. Maintenant, configurez une autre alerte pour le *Nombre total de messages utilisés*. Cette métrique est utile si vous souhaitez envoyer une alerte quand le nombre de messages utilisés approche le quota du hub IoT. Vous savez ainsi que le hub va bientôt commencer à rejeter les messages.

   Dans l’écran **Afficher les alertes classiques**, cliquez sur **Ajouter une alerte métrique (classique)** , puis renseignez ces champs dans le volet **Ajouter une règle**.

   **Name** : entrez un nom pour votre règle d’alerte, par exemple *number-of-messages-used*.

   **Description** : entrez la description de votre alerte, par exemple *alert when getting close to quota* (alerte à l’approche du quota).

   **Source** : affectez *Métriques* à ce champ.

    Définissez **Abonnement**, **Groupe de ressources**, et **Ressource** avec les valeurs que vous avez sélectionnées dans l’écran **Afficher les alertes classiques**. 

    Affectez **Nombre total de messages utilisés** à *Métrique*.

6. Sous le graphique, renseignez les champs suivants :

   **Condition** : choisissez *Supérieur à*.

   **Seuil** : indiquez la valeur 1000.

   **Période** : affectez *Au cours des 5 dernières minutes* à ce champ. 

   **Destinataires des e-mails de notification** : entrez votre adresse e-mail. 

   Cliquez sur **OK** pour enregistrer la règle. 

5. Vous devriez maintenant voir deux alertes dans le volet d’alertes classiques : 

   ![Capture d’écran montrant l’écran des alertes classiques avec les nouvelles règles d’alerte.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Fermez le volet des alertes. 
    
    Avec ces paramètres, vous recevez une alerte quand le nombre de messages envoyés est supérieur à 400 et quand le nombre total de messages utilisés dépasse NOMBRE.

## <a name="run-simulated-device-app"></a>Exécuter l’application d’appareil simulé

Plus haut dans la section sur la configuration du script, vous avez configuré un appareil pour simuler l’utilisation d’un appareil IoT. Dans cette section, vous téléchargez une application console .NET qui simule un appareil envoyant des messages appareil-à-cloud à un IoT Hub.  

Téléchargez la solution pour la [simulation d’appareil IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Ce lien permet de télécharger un dépôt contenant plusieurs applications. La solution que vous recherchez se trouve à l’emplacement iot-hub/Tutorials/Routing/.

Double-cliquez sur le fichier de solution (SimulatedDevice.sln) pour ouvrir le code dans Visual Studio, puis ouvrez Program.cs. Remplacez `{iot hub hostname}` par le nom d’hôte IoT Hub. Le format du nom d’hôte IoT Hub est **{iot-hub-name}.azure-devices.net**. Pour ce didacticiel, le nom d’hôte IoT Hub est **ContosoTestHub.azure-devices.net**. Ensuite, remplacez `{device key}` par la clé de l’appareil enregistrée précédemment lorsque vous avez configuré l’appareil simulé. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Exécuter et tester 

Dans Program.cs, remplacez la valeur 1000 de `Task.Delay` par 10, ce qui réduit l’intervalle de temps entre l’envoi de messages de 1 à 0,01 seconde. Le fait d’écourter ce délai augmente le nombre de messages envoyés.

```csharp
await Task.Delay(10);
```

Exécutez l’application console. Patientez 10 à 15 minutes. Vous pouvez voir les messages transmis de l’appareil simulé au hub sur l’écran de la console de l’application.

### <a name="see-the-metrics-in-the-portal"></a>Afficher les métriques dans le portail

Ouvrez vos métriques à partir du tableau de bord. Remplacez les valeurs de temps par *30 dernières minutes* avec une granularité temporelle de *1 minute*. Le graphique montre alors les messages de télémétrie envoyés et le nombre total de messages utilisés, avec les nombres les plus récents en bas du graphique.

   ![Capture d’écran montrant les métriques.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Afficher les alertes

Revenez aux alertes. Cliquez sur **Groupes de ressources**, puis sélectionnez *ContosoResources* et le hub *ContosoTestHub*. Dans la page de propriétés affichée pour le hub, sélectionnez **Alertes**, puis **Afficher les alertes classiques**. 

Quand le nombre de messages envoyés dépasse la limite, vous commencez à recevoir des alertes par e-mail. Pour voir si des alertes sont actives, accédez à votre hub et sélectionnez **Alertes**. Les alertes actives sont affichées ainsi que les éventuels avertissements. 

   ![Capture d’écran montrant les alertes déclenchées.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Cliquez sur l’alerte des messages de télémétrie. Vous obtenez le résultat de la métrique et un graphique avec les résultats. Par ailleurs, l’e-mail envoyé pour vous avertir du déclenchement de l’alerte ressemble à celui contenu dans cette image :

   ![Capture d’écran de l’e-mail montrant les alertes déclenchées.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Afficher les journaux de diagnostic

Vous avez configuré vos journaux de diagnostic pour qu’ils soient exportés vers le stockage d’objets blob. Accédez à votre groupe de ressources et sélectionnez votre compte de stockage *contosostoragemon*. Sélectionnez Objets blob, puis ouvrez le conteneur *insights-logs-connections*. Descendez dans la hiérarchie jusqu’à ce que vous arriviez à la date actuelle et sélectionnez le fichier le plus récent. 

   ![Capture d’écran montrant la descente dans la hiérarchie jusqu’au conteneur de stockage pour afficher les journaux de diagnostic.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Cliquez sur **Télécharger** pour le télécharger et l’ouvrir. Vous voyez les journaux d’activité de l’appareil qui se connecte et se déconnecte à mesure qu’il envoie des messages au hub. Voici un exemple :

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Nettoyer les ressources 

Pour supprimer toutes les ressources que vous avez créées dans ce tutoriel, supprimez le groupe de ressources. Cette opération supprime toutes les ressources contenues dans le groupe. Dans le cas présent, le hub IoT, le compte de stockage et le groupe de ressources sont supprimés. Si vous avez épinglé des métriques au tableau de bord, vous devez les supprimer manuellement. Pour cela, cliquez sur les trois points situés dans le coin supérieur droit de chaque métrique et sélectionnez **Supprimer**.

Pour supprimer le groupe de ressources, utilisez la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à utiliser des métriques et des journaux de diagnostic en effectuant les tâches suivantes :

> [!div class="checklist"]
> * Créer un hub IoT, un appareil simulé et un compte de stockage avec Azure CLI.  
> * Activer les journaux de diagnostics. 
> * Activer les métriques.
> * Configurer des alertes pour ces métriques. 
> * Télécharger et exécuter une application qui simule l’envoi de messages par un appareil IoT au hub. 
> * Exécuter l’application jusqu’à ce que des alertes se déclenchent. 
> * Afficher les résultats des métriques et examiner les journaux de diagnostic. 

Passez au didacticiel suivant pour découvrir comment gérer l’état d’un appareil IoT. 

> [!div class="nextstepaction"]
> [Configurer vos appareils à partir d’un service back-end](tutorial-device-twins.md)