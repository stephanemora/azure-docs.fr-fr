---
title: Guide pratique pour déployer des dépendances cloud OPC Twin dans Azure | Microsoft Docs
description: Comment déployer des dépendances Azure OPC Twin.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae9f2b05bfc6ea6315022d04c8d267d916cf282e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61451597"
---
# <a name="deploying-dependencies-for-local-development"></a>Déploiement de dépendances pour un développement local

Cet article explique comment déployer seulement les services de plateforme Azure nécessaires pour le développement et le débogage local.   À la fin, vous aurez un groupe de ressources déployé qui contient tout ce dont vous avez besoin pour le développement et le débogage local.

## <a name="deploy-azure-platform-services"></a>Déployer des services de plateforme Azure

1. Vérifiez que PowerShell et les extensions [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) sont installés.  Ouvrez une invite de commandes ou un terminal, et exécutez :

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Suivez les invites pour affecter un nom au groupe de ressources pour votre déploiement.  Le script déploie seulement les dépendances sur ce groupe de ressources dans votre abonnement Azure, mais pas les microservices.  Le script inscrit également une application dans Azure Active Directory.  Ceci est nécessaire pour la prise en charge de l’authentification OAUTH.  Le déploiement peut prendre plusieurs minutes.

3. Une fois le script terminé, vous pouvez choisir d’enregistrer le fichier .env.  Le fichier d’environnement .env est le fichier de configuration de tous les services et outils que vous voulez exécuter sur votre machine de développement.  

## <a name="troubleshooting-deployment-failures"></a>Dépannage des échecs de déploiement

### <a name="resource-group-name"></a>Nom de groupe ressources

Vérifiez que vous utilisez un nom de groupe de ressources court et simple.  Le nom est également utilisé pour nommer les ressources : il doit donc être conforme aux spécifications de nommage des ressources.  

### <a name="azure-active-directory-aad-registration"></a>Inscription Azure Active Directory

Le script de déploiement essaie d’inscrire les applications AAD dans Azure Active Directory.  En fonction de vos droits sur le locataire AAD sélectionné, cette opération peut échouer.   Il y a 3 options :

1. Si vous avez choisi un locataire AAD dans une liste de locataires, redémarrez le script et choisissez un autre locataire dans la liste.
2. Vous pouvez aussi déployer un locataire AAD privé, redémarrer le script et choisir de l’utiliser.
3. Continuer sans authentification.  Comme vous exécutez vos microservices localement, ceci est acceptable, mais ne reproduit pas les environnements de production.  

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé des services OPC Twin sur un projet existant, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Découvrir comment déployer des modules OPC Twin](howto-opc-twin-deploy-modules.md)
