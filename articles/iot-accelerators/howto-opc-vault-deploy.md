---
title: Guide pratique pour déployer le service de gestion de certificats OPC Vault - Azure | Microsoft Docs
description: Guide pratique pour déployer le service de gestion de certificats OPC Vault à partir de zéro.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012984"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Générer et déployer le service de gestion de certificats OPC Vault

Cet article explique comment déployer le service de gestion de certificats OPC Vault dans Azure.

> [!NOTE]
> Pour obtenir des informations sur les détails et instructions du déploiement, consultez le [dépôt OPC Vault](https://github.com/Azure/azure-iiot-opc-vault-service) GitHub.

## <a name="prerequisites"></a>Prérequis

### <a name="install-required-software"></a>Installer les logiciels nécessaires

Actuellement, l’opération de génération et de déploiement est limitée à Windows.
Les exemples sont tous écrits pour C# .NET Standard, ce qui est nécessaire pour générer le service et des exemples pour le déploiement.
Tous les outils dont vous avez besoin pour .NET Standard sont fournis avec les outils .Net Core. Vous trouverez [ici](https://docs.microsoft.com/dotnet/articles/core/getting-started) ce dont vous avez besoin.

1. [Installez .NET Core 2.1+][dotnet-install].
2. [Installez Docker][docker-url] (facultatif, uniquement si la build Docker locale est nécessaire).
4. Installez les [outils en ligne de commande Azure pour PowerShell][powershell-install].
5. Inscrivez-vous à un [abonnement Azure][azure-free].

### <a name="clone-the-repository"></a>Cloner le référentiel

Si vous ne l’avez pas déjà fait, clonez ce dépôt GitHub.  Ouvrez une invite de commandes ou un terminal, et exécutez :

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

ou clonez le dépôt directement dans Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Générer et déployer le service Azure sur Windows

Un script PowerShell permet de déployer le microservice OPC et l’application en toute simplicité.<br>

1. Ouvrez une fenêtre PowerShell à la racine du dépôt. 
3. Accédez au dossier deploy : `cd deploy`
3. Choisissez un nom pour `myResourceGroup` qui ne risque pas de provoquer un conflit avec d’autres pages web déployées. Consultez les informations [ci-dessous](#website-name-already-in-use) pour savoir comment les noms de pages web sont choisis en fonction du nom du groupe de ressources.
5. Démarrez le déploiement avec `.\deploy.ps1` pour une installation interactive<br>
ou entrez une ligne de commande complète :  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Si vous envisagez d’effectuer un développement avec ce déploiement, ajoutez `-development 1` pour activer l’interface utilisateur Swagger et déployer des builds de débogage.
6. Suivez les instructions du script pour vous connecter à votre abonnement et fournir des informations supplémentaires.
9. Après une opération de génération et de déploiement réussie, le message suivant doit s’afficher :
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

Si vous rencontrez des problèmes, effectuez les étapes mentionnées [ci-dessous](#troubleshooting-deployment-failures).

8. Ouvrez le navigateur de votre choix, puis ouvrez la page de l’application : `https://myResourceGroup.azurewebsites.net`
8. Accordez à l’application web et au microservice OPC Vault quelques minutes de préparation après le déploiement. La page d’accueil du web peut se bloquer pendant jusqu’à une minute lors de la première utilisation, jusqu’à ce que vous obteniez les premières réponses.
11. Pour consulter l’API Swagger, ouvrez : `https://myResourceGroup-service.azurewebsites.net`
13. Pour démarrer un serveur GDS local avec DotNet, démarrez `.\myResourceGroup-gds.cmd` ; ou avec Docker, démarrez `.\myResourceGroup-dockergds.cmd`.

Notez qu’il est possible de redéployer une build avec exactement les mêmes paramètres. N’oubliez pas qu’une telle opération renouvelle tous les secrets d’application et peut réinitialiser certains paramètres dans les inscriptions d’applications Azure Active Directory (Azure AD).

Il est également possible de redéployer uniquement les fichiers binaires de l’application web. Avec le paramètre `-onlyBuild 1`, les nouveaux packages zip du service et l’application sont déployés sur les applications web.

Une fois le déploiement réussi, vous pouvez commencer à utiliser les services : [Guide pratique pour gérer le service de gestion de certificats OPC Vault](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Supprimer les services de l’abonnement

1. Connectez-vous au portail Azure : `https://portal.azure.com`.
2. Accédez au groupe de ressources dans lequel le service a été déployé.
3. Sélectionnez `Delete resource group`, puis confirmez.
4. Après quelques instants, tous les composants de service déployés sont supprimés.
5. Accédez maintenant à `Azure Active Directory/App registrations`.
6. Trois inscriptions doivent être listées pour chaque groupe de ressources déployé avec les noms suivants : `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`.
Chaque inscription doit être supprimée séparément.
7. Maintenant, tous les composants déployés sont supprimés.

## <a name="troubleshooting-deployment-failures"></a>Résoudre les problèmes de déploiement

### <a name="resource-group-name"></a>Nom de groupe ressources

Veillez à utiliser un nom de groupe de ressources court et simple.  Le nom est également utilisé pour nommer les ressources et le préfixe d’URL du service. Il doit donc être conforme aux spécifications de nommage des ressources.  

### <a name="website-name-already-in-use"></a>Nom du site web déjà utilisé

Le nom du site web est peut-être déjà utilisé ailleurs.  Si vous rencontrez cette erreur, vous devez utiliser un nom de groupe de ressources différent. Les noms d’hôtes utilisés par le script de déploiement sont https://resourcegroupname.azurewebsites.net et https://resourgroupname-service.azurewebsites.net.
Les autres noms de services sont générés en combinant des hachages de noms courts et ne risquent pas d’entrer en conflit avec d’autres services.

### <a name="azure-active-directory-azure-ad-registration"></a>Inscription d’Azure Active Directory (Azure AD) 

Le script de déploiement essaie d’inscrire trois applications Azure AD dans Azure Active Directory.  
En fonction de vos autorisations dans le locataire Azure AD sélectionné, cette opération peut échouer.   Nous avons deux options :

1. Si vous avez choisi un locataire Azure AD dans une liste de locataires, redémarrez le script et choisissez un autre locataire dans la liste.
2. Vous pouvez aussi déployer un locataire Azure AD privé dans un autre abonnement, redémarrer le script, puis choisir de l’utiliser.

## <a name="deployment-script-options"></a>Options du script de déploiement

Le script utilise les paramètres suivants :


```
-resourceGroupName
```

Peut être le nom d’un groupe de ressources existant ou nouveau.

```
-subscriptionId
```


(Facultatif) L’ID de l’abonnement où déployer les ressources.

```
-subscriptionName
```


Ou encore le nom de l’abonnement.

```
-resourceGroupLocation
```


(Facultatif) L’emplacement du groupe de ressources. S’il est spécifié, le script essaiera de créer un groupe de ressources à cet emplacement.


```
-tenantId
```


Locataire Azure AD à utiliser. 

```
-development 0|1
```

(Facultatif) À déployer pour le développement. Utilisez la build Debug et définissez l’environnement ASP.Net sur Développement. Créez « .publishsettings » pour l’importation dans Visual Studio 2017 afin de lui permettre de déployer l’application et le service directement.

```
-onlyBuild 0|1
```

(Facultatif) Pour régénérer et redéployer uniquement les applications web et regénérer les conteneurs Docker.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment déployer OPC Vault à partir de zéro, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Gérer OPC Vault](howto-opc-vault-manage.md)
