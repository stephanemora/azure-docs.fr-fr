---
title: Accéder à l’API Azure Media Services - CLI 2.0 | Microsoft Docs
description: Suivez les étapes de cette procédure pour accéder à l’API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a4a7c59e93b860245d67695de90fbae2becac3e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="access-azure-media-services-api-with-cli-20"></a>Accéder à l’API Azure Media Services avec l’interface CLI 2.0
 
Vous devez utiliser l’authentification de principal de service Azure AD pour vous connecter à l’API Azure Media Services. Votre application doit demander un jeton Azure AD avec les paramètres suivants :

* Point de terminaison de locataire Azure AD
* URI de ressource Media Services
* URI de ressource pour REST Media Services
* Valeurs de l’application Azure AD : ID client et clé secrète client

Cet article vous montre comment utiliser l’interface CLI 2.0 pour créer une application Azure AD et un principal de service pour obtenir les valeurs nécessaires pour accéder aux ressources Azure Media Services.

## <a name="prerequisites"></a>Prérequis
 

Créez un nouveau compte Azure Media Services, comme décrit dans [ce démarrage rapide](create-account-cli-quickstart.md).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](http://portal.azure.com) et lancez **CloudShell** pour exécuter les commandes CLI, comme indiqué dans les étapes suivantes.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0 ou une version ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Diffuser un fichier](stream-files-dotnet-quickstart.md)
