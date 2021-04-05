---
title: Résidence des données
description: Décrit la résidence des données lors de l’utilisation d’Azure Remote Rendering.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99577452"
---
# <a name="azure-remote-rendering-data-residency"></a>Résidence des données d’Azure Remote Rendering 
Cet article explique le concept de résidence des données et la façon dont il s’applique à Azure Remote Rendering. 

## <a name="data-residency"></a>Résidence des données 
Azure Remote Rendering utilise des conteneurs Blob Azure fournis par l’utilisateur pour le stockage des modèles. Lorsque le modèle n’est pas utilisé, il reste dans la région Stockage Blob Azure fournie par l’utilisateur. Lorsque le modèle est utilisé pour le rendu, les données sont copiées dans la région choisie par l’utilisateur au début de la session de rendu.

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez apprendre à configurer un conteneur Stockage Blob Azure pour Azure Remote Rendering, consultez [Convertir un modèle pour le rendu](../quickstarts/convert-model.md).
