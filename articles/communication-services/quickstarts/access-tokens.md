---
title: 'Démarrage rapide : Créer et gérer des jetons d’accès'
titleSuffix: An Azure Communication Services quickstart
description: Découvrez comment gérer des identités et des jetons d’accès à l’aide de la bibliothèque de client Azure Communication Services Administration.
author: tomaschladek
manager: jken
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e323f1f50fe6c67a841c300fcbec1eed3afc4497
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074122"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Démarrage rapide : Créer et gérer des jetons d’accès

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Commencez avec Azure Communication Services en utilisant la bibliothèque de client Communication Services Administration pour provisionner et gérer des jetons d’accès. Les jetons d’accès permettent aux bibliothèques de client Chat et Calling de s’authentifier directement auprès d’Azure Communication Services. Ces jetons sont générés sur un service de provisionnement de jetons côté serveur que vous implémentez. Ils sont ensuite utilisés pour initialiser les bibliothèques de client Communication Services sur les appareils clients.

Notez que les tarifs indiqués sur les images tout au long de ce tutoriel sont fournis à titre d’exemple uniquement.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

La sortie de l’application décrit chaque action terminée :
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

> [!div class="checklist"]
> * Gérer des identités
> * Émettre des jetons d’accès
> * Utiliser la bibliothèque de client Communication Services Administration


> [!div class="nextstepaction"]
> [Ajouter Voice calling à votre application](./voice-video-calling/getting-started-with-calling.md)

Vous voudrez peut-être aussi :

 - [Découvrir l’authentification](../concepts/authentication.md)
 - [Ajouter un système de conversation à votre application](./chat/get-started.md)
 - [Découvrir l’architecture client et serveur](../concepts/client-and-server-architecture.md)
