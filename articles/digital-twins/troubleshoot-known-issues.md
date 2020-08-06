---
title: Problèmes connus - Azure Digital Twins
description: Recevez de l’aide pour la reconnaissance et l’atténuation des problèmes connus avec Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044138"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problèmes connus dans Azure Digital Twins

Cet article fournit des informations sur les problèmes connus associés à Azure Digital Twins.

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