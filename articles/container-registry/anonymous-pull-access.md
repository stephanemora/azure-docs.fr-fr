---
title: Activer l’accès par extraction anonyme
description: Vous pouvez activer l’accès par extraction anonyme pour que le contenu de votre registre de conteneurs Azure soit accessible publiquement.
ms.topic: how-to
ms.date: 09/17/2021
ms.custom: ''
ms.openlocfilehash: e82bb43ee8865642ae7939a94291e0c9f5432198
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161978"
---
# <a name="make-your-container-registry-content-publicly-available"></a>Rendre le contenu de votre registre de conteneurs accessible publiquement

La configuration d’un registre de conteneurs Azure pour l’accès par extraction anonyme (sans authentification) est une fonctionnalité facultative qui permet à tout utilisateur disposant d’un accès à Internet d’extraire du contenu du registre.

L’accès par extraction anonyme est une fonctionnalité d’évaluation, disponible dans les [niveaux de service](container-registry-skus.md) Standard et Premium. Pour configurer l’accès par extraction anonyme, mettez à jour un registre à l’aide d’Azure CLI (version 2.21.0 ou ultérieure). Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="about-anonymous-pull-access"></a>À propos de l’accès par extraction anonyme

Par défaut, l’accès au contenu d’extraction ou de transmission à partir d’un registre de conteneurs Azure est uniquement disponible pour les utilisateurs [authentifiés](container-registry-authentication.md). L’activation de l’accès par extraction anonyme (sans authentification) rend tout le contenu du registre disponible publiquement pour des actions de lecture (pull). L’accès par extraction anonyme peut être utilisé dans des scénarios qui ne nécessitent pas d’authentification de l’utilisateur, comme la distribution d’images conteneurs publiques.

- Activez l’accès par extraction anonyme en mettant à jour les propriétés d’un registre existant.
- Après avoir activé l’accès par extraction anonyme, vous pouvez désactiver cet accès à tout moment.
- Seules les opérations de plan de données sont disponibles pour les clients non authentifiés.
- Le registre peut limiter un taux élevé de requêtes non authentifiées.
- Si vous vous êtes précédemment authentifié auprès du registre, veillez à effacer les informations d’identification avant de tenter une opération d’extraction anonyme.

> [!WARNING]
> L’accès en extraction anonyme s’applique actuellement à tous les référentiels du registre. Si vous gérez l’accès au référentiel à l’aide de [jetons délimités par le référentiel](container-registry-repository-scoped-permissions.md), tous les utilisateurs peuvent extraire du contenu de ces référentiels dans un registre activé pour l’extraction anonyme. Nous vous recommandons de supprimer des jetons lorsque l’accès par extraction anonyme est activé.

## <a name="configure-anonymous-pull-access"></a>Configurer l’accès par extraction anonyme 

### <a name="enable-anonymous-pull-access"></a>Activer l’accès par extraction anonyme
Mettez à jour un registre à l’aide de la commande [az acr update](/cli/azure/acr#az_acr_update) et transmettez le paramètre `--anonymous-pull-enabled`. Par défaut, l’extraction anonyme est désactivée dans le registre.
          
```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

> [!IMPORTANT]
> Si vous vous êtes précédemment authentifié au registre avec des informations d’identification Docker, exécutez `docker logout` pour vous assurer que vous avez effacé les informations d’identification existantes avant de tenter des opérations d’extraction anonyme. Sinon, vous risquez de voir apparaître un message d’erreur semblable à « accès par extraction refusé ».

### <a name="disable-anonymous-pull-access"></a>Désactiver l’accès par extraction anonyme
Désactivez l’accès par extraction anonyme en définissant `--anonymous-pull-enabled` sur `false`.

```azurecli
az acr update --name myregistry --anonymous-pull-enabled false
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’utilisation [des jetons délimités par le référentiel](container-registry-repository-scoped-permissions.md).
* En savoir plus sur les options d’[authentification](container-registry-authentication.md) auprès d’un registre de conteneurs Azure.
