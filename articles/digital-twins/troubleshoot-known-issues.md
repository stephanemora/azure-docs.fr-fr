---
title: Problèmes connus - Azure Digital Twins
description: Recevez de l’aide pour la reconnaissance et l’atténuation des problèmes connus avec Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528017"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problèmes connus dans Azure Digital Twins

Cet article fournit des informations sur les problèmes connus associés à Azure Digital Twins.

## <a name="managing-event-routes-in-the-azure-portal"></a>Gestion des itinéraires d’événements sur le Portail Azure

Si vous êtes connecté au portail à l’aide d’un [**compte Microsoft**](https://account.microsoft.com/account/Account), tel qu’un compte *@outlook.com* , un écran indiquant *Vous avez besoin d’une autorisation pour afficher des itinéraires d’événements* s’affichera, lorsque vous tenterez de gérer des itinéraires d’événements sur le portail, peu importe votre niveau d’autorisation.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Capture d’écran du Portail Azure montrant l’erreur d’autorisation lors de la tentative de création d’itinéraires d’événements sur une instance Azure Digital Twins":::

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Les utilisateurs qui ne sont pas actuellement en mesure de gérer des itinéraires d’événements sur le portail peuvent toujours gérer les itinéraires d’événements à l’aide des API Azure Digital Twins ou de l’interface de ligne de commande. Le passage à l’un de ces outils pour la gestion des itinéraires d’événements est la stratégie recommandée pour atténuer ce problème.

Les instructions se trouvent dans [*Procédure : Gérer les points de terminaison et les routes*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Causes possibles

Vous êtes connecté au portail à l’aide d’unl [compte Microsoft](https://account.microsoft.com/account/Account) personnel, tel qu’un compte *@outlook.com* . La gestion des itinéraires d’événements sur le Portail Azure n’est actuellement disponible que pour des utilisateurs Azure sur des comptes de domaine d’entreprise.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>« Erreur 400 du client : Requête incorrecte » dans Cloud Shell

Les commandes dans Cloud Shell peuvent échouer par intermittence avec l’erreur « Erreur 400 du client : Requête incorrecte pour l’URL : http://localhost:50342/oauth2/token  » suivie par l’arborescence complète des appels de procédure.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Cela peut être résolu en réexécutant à nouveau la commande `az login` et en suivant les étapes de connexion suivantes.

Passée cette étape, vous devez pouvoir réexécuter la commande.

### <a name="possible-causes"></a>Causes possibles

Voici le résultat d’un problème connu dans Cloud Shell : [*L’obtention d’un jeton à partir de Cloud Shell échoue par intermittence avec l’Erreur 400 du client : Requête incorrecte*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la sécurité et les autorisations sur Azure Digital Twins :
* [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md)