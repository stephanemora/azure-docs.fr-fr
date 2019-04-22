---
title: Comment déployer un module jumeau de OPC à un projet Azure existant | Microsoft Docs
description: Comment déployer des représentations d’OPC à un projet existant.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6bdfeefc366734aa10dbaccec69bac8e0b41103f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493244"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Déployer la représentation d’OPC à un projet existant

Le module jumeau de OPC s’exécute sur IoT Edge et fournit plusieurs services edge à la représentation d’OPC et les services de Registre. 

Le service de micro OPC Twin facilite la communication entre les opérateurs de fabrique et des appareils de serveur OPC UA en usine via un module OPC représentations IoT Edge. Le service micro expose des services OPC UA (Parcourir, lecture, écriture et exécuter) via l’API REST. 

Le microservice de Registre d’appareil OPC UA fournit l’accès aux applications OPC UA inscrites et leurs points de terminaison. Opérateurs et administrateurs peuvent inscrire et annuler l’inscription de nouvelles applications OPC UA et parcourir les existantes, y compris leurs points de terminaison. Outre application et la gestion de point de terminaison, le service de Registre répertorie également les modules OPC représentations IoT Edge inscrits. L’API de service vous donne contrôle des fonctionnalités de module edge, par exemple, le démarrage ou l’arrêt de la découverte de serveurs (services d’analyse) ou activation nouvelle jumeaux de point de terminaison qui sont accessibles via le service de micro OPC jumeau.

Le cœur du module est l’identité du superviseur. Le superviseur gère les représentations de point de terminaison, qui correspond aux points de terminaison OPC UA server qui sont activés à l’aide de l’API du Registre OPC UA correspondant. Représentations de ce point de terminaison traduisent OPC UA JSON reçu du service de micro OPC représentations en cours d’exécution dans le cloud en messages binaire OPC UA, qui sont envoyées via un canal sécurisé avec état au point de terminaison géré. Le superviseur fournit également des services de découverte qui envoient des événements de découverte de périphérique pour le service de l’intégration d’appareil OPC UA pour le traitement, dans lequel ces événements sont le résultat dans les mises à jour dans le Registre OPC UA.  Cet article vous montre comment déployer le module jumeau de OPC à un projet existant. 

> [!NOTE]
> Pour plus d’informations sur les détails de déploiement et des instructions, consultez le GitHub [référentiel](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Conditions préalables

Vérifiez que vous disposez de PowerShell et [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) extensions installées.   Si vous n’avez pas encore fait, clonez ce référentiel GitHub.  Ouvrez une invite de commandes ou un terminal et exécutez :

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Déployer les services IoT industrielles dans Azure

1. Sur l’invite de commandes ouverte ou un terminal, exécutez :

   ```bash
   deploy
   ```

2. Suivez les invites pour attribuer un nom au groupe de ressources du déploiement et un nom pour le site Web.   Le script déploie les microservices et leurs dépendances de plateforme Azure dans le groupe de ressources dans votre abonnement Azure.  Le script enregistre également une Application dans votre locataire Azure Active Directory (AAD) pour prendre en charge l’authentification OAUTH.  Déploiement nécessitera plusieurs minutes.  Un exemple de ce que vous devriez voir une fois que la solution est déployée avec succès :

   ![Représentation de OPC Industrial IoT déployer sur un projet existant](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   La sortie inclut l’URL du point de terminaison public. 

3. Une fois le script terminé, sélectionnez si vous souhaitez enregistrer le fichier .env.  Vous avez besoin du fichier d’environnement .env si vous souhaitez vous connecter au point de terminaison cloud à l’aide des outils tels que la Console ou déployer des modules pour le développement et le débogage.

## <a name="troubleshooting-deployment-failures"></a>Résolution des problèmes de déploiement

### <a name="resource-group-name"></a>Nom de groupe ressources

Vérifiez que vous utilisez un nom de groupe de ressources à court et simple.  Le nom est également utilisé pour les ressources de nom en tant que tel qu'il doit être conforme aux exigences de noms de ressource.  

### <a name="website-name-already-in-use"></a>Nom du site Web déjà en cours d’utilisation

Il est possible que le nom du site Web est déjà en cours d’utilisation.  Si vous rencontrez cette erreur, vous devez utiliser un nom d’application différent.

### <a name="azure-active-directory-aad-registration"></a>Inscription Azure Active Directory (AAD)

Le script de déploiement tente d’inscrire deux applications AAD dans Azure Active Directory.  En fonction de vos droits au locataire AAD sélectionné, le déploiement peut échouer. Nous avons deux options :

1. Si vous avez choisi un locataire AAD à partir d’une liste de locataires, redémarrez le script et choisissez une option différente dans la liste.
2. Vous pouvez également déployer un locataire AAD privé dans un autre abonnement, redémarrer le script et sélectionnez cette option pour l’utiliser.

> [!WARNING]
> JAMAIS continuer sans authentification.  Si vous choisissez pour ce faire, tout le monde peut accéder à vos points de terminaison OPC Twin à partir d’Internet non authentifié.   Vous pouvez toujours choisir la [option de déploiement « local »](howto-opc-twin-deploy-dependencies.md) pour vos premiers pas.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Déployer une démonstration de services IoT industrielle tout-en-un

Au lieu des services et des dépendances, vous pouvez également déployer une démo tout-en-un.  Le tout dans une démonstration contient trois serveurs OPC UA, le module jumeau de OPC, tous les microservices et un exemple de l’Application Web.  Il est destiné à des fins de démonstration.

1. Assurez-vous que vous disposez d’un clone du référentiel (voir ci-dessus). Ouvrez une invite de commandes ou un terminal à la racine du référentiel et exécutez :

   ```bash
   deploy -type demo
   ```

2. Suivez les invites pour affecter un nouveau nom pour le groupe de ressources et un nom pour le site Web.  Une fois déployé, le script affiche l’URL du point de terminaison d’application web.

## <a name="deployment-script-options"></a>Options de script de déploiement

Le script accepte les paramètres suivants :

```bash
-type
```

Le type de déploiement (démonstration local, de la machine virtuelle)

```bash
-resourceGroupName
```

Peut être le nom d’un existant ou un groupe de ressources.

```bash
-subscriptionId
```

Facultatif, l’ID d’abonnement dans lequel déployer les ressources.

```bash
-subscriptionName
```

Ou le nom de l’abonnement.

```bash
-resourceGroupLocation
```

Facultatif, il s’agit d’un emplacement du groupe de ressources. Si spécifié, tente de créer un nouveau groupe de ressources à cet emplacement.

```bash
-aadApplicationName
```

Un nom pour l’application AAD Enregistrer sous. 

```bash
-tenantId
```

Client AAD à utiliser.

```bash
-credentials
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à déployer des représentations d’OPC à un projet existant, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Sécuriser les communications de Client d’OPC et OPC PLC](howto-opc-vault-deploy-existing-client-plc-communication.md)
