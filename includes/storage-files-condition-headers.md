---
title: Fichier Include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159867"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>ConditionHeadersNotSupported d’erreur à partir d’une Application Web à l’aide d’Azure Files à partir du navigateur

Lorsque l’accès au contenu hébergé dans Azure Files via une application qui permet d’utiliser des en-têtes conditionnels, comme un navigateur web, l’accès échoue, affiche une erreur ConditionHeadersNotSupported.

![Erreur de ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Cause :

En-têtes conditionnels ne sont pas encore pris en charge. Vous devez les mettre en œuvre des applications demander le fichier complet chaque fois que le fichier est accessible.

### <a name="workaround"></a>Solution de contournement

Quand un nouveau fichier est chargé, la propriété de contrôle de cache par défaut est « no-cache ». Pour forcer l’application pour demander le fichier chaque fois, la propriété du fichier cache-control doit être mis à jour à partir de « no-cache » pour « no-cache, no-store, must-revalidate ». Vous pouvez le faire à l’aide de [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Modification de cache de contenu de l’Explorateur de stockage](media/storage-files-condition-headers/storage-explorer-cache.png)