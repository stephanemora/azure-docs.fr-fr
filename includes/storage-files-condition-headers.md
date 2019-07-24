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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177131"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Erreur ConditionHeadersNotSupported sur une application web utilisant Azure Files à partir du navigateur

Lorsque vous accédez à du contenu hébergé dans Azure Files via une application utilisant des en-têtes conditionnels (par exemple, un navigateur web), l’accès échoue et le message d’erreur ConditionHeadersNotSupported s’affiche.

![Erreur ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Cause :

Les en-têtes conditionnels ne sont pas encore pris en charge. Les applications qui utilisent ces en-têtes doivent demander le fichier complet pour chaque accès au fichier.

### <a name="workaround"></a>Solution de contournement

Quand un nouveau fichier est chargé, la propriété cache-control est définie sur « no-cache » par défaut. Pour forcer l’application à demander le fichier à chaque fois, la valeur « no-cache » de la propriété cache-control du fichier doit être remplacée par « no-cache, no-store, must-revalidate ». Vous pouvez le faire en utilisant [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

![Modification du cache de contenu de l’explorateur de stockage](media/storage-files-condition-headers/storage-explorer-cache.png)