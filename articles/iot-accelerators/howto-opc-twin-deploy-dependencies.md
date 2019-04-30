---
title: Comment déployer des dépendances de cloud OPC représentation dans Azure | Microsoft Docs
description: Comment déployer des dépendances OPC Twin Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae9f2b05bfc6ea6315022d04c8d267d916cf282e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451597"
---
# <a name="deploying-dependencies-for-local-development"></a>Déploiement des dépendances pour un développement local

Cet article explique comment déployer uniquement les Services de plateforme Azure devoir faire du développement et débogage.   À la fin, avoir un groupe de ressources déployé qui contient tout ce dont vous avez besoin pour le développement local et le débogage.

## <a name="deploy-azure-platform-services"></a>Déployer des services de plateforme Azure

1. Vérifiez que vous disposez de PowerShell et [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) extensions installées.  Ouvrez une invite de commandes ou un terminal et exécutez :

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Suivez les invites pour attribuer un nom au groupe de ressources pour votre déploiement.  Le script déploie uniquement les dépendances sur ce groupe de ressources dans votre abonnement Azure, mais pas les services micro-charges.  Le script enregistre également une Application dans Azure Active Directory.  Cela est nécessaire pour prendre en charge l’authentification OAUTH.  Le déploiement peut prendre plusieurs minutes.

3. Une fois le script terminé, vous pouvez sélectionner pour enregistrer le fichier .env.  Le fichier d’environnement .env est le fichier de configuration de tous les services et outils que vous souhaitez exécuter sur votre ordinateur de développement.  

## <a name="troubleshooting-deployment-failures"></a>Résolution des problèmes de déploiement

### <a name="resource-group-name"></a>Nom de groupe ressources

Vérifiez que vous utilisez un nom de groupe de ressources à court et simple.  Le nom est également utilisé pour les ressources de nom en tant que tel qu'il doit être conforme aux exigences de noms de ressource.  

### <a name="azure-active-directory-aad-registration"></a>Inscription Azure Active Directory (AAD)

Le script de déploiement essaie d’inscrire des applications AAD dans Azure Active Directory.  En fonction de vos droits au locataire AAD sélectionné, cette opération peut échouer.   Il existe 3 options :

1. Si vous avez choisi un locataire AAD à partir d’une liste de locataires, redémarrez le script et choisissez une option différente dans la liste.
2. Vous pouvez également déployer un locataire AAD privé, redémarrer le script et sélectionnez cette option pour l’utiliser.
3. Continuer sans authentification.  Étant donné que vous exécutez vos services micro-charges localement, cela est acceptable, mais ne reproduit pas les environnements de production.  

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez correctement déployé les services du jumeau d’OPC à un projet existant, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Découvrez comment déployer des modules de représentations d’OPC](howto-opc-twin-deploy-modules.md)
