---
title: Fonctions Bicep - fichiers
description: Décrit les fonctions à utiliser dans un fichier Bicep pour charger le contenu à partir d’un fichier.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 28b28086986eb4e871cc6ed8c315c3802e721840
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359841"
---
# <a name="file-functions-for-bicep"></a>Fonctions de fichier pour Bicep

Cet article décrit les fonctions Bicep pour le chargement de contenu à partir de fichiers externes.

## <a name="loadfileasbase64"></a>loadFileAsBase64

`loadFileAsBase64(filePath)`

Télécharge le fichier comme une chaîne base64.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| filePath | Oui | string | Chemin d'accès vers le fichier à charger. Le chemin d’accès est relatif au fichier Bicep déployé. |

### <a name="remarks"></a>Remarques

Utilisez cette fonction lorsque vous souhaitez inclure du contenu binaire au déploiement. Au lieu d’encoder manuellement le fichier dans une chaîne Base64 et de l’ajouter à votre fichier Bicep, chargez le fichier avec cette fonction. Le fichier est chargé lorsque le fichier Bicep est compilé dans un modèle JSON. Pendant le déploiement, le modèle JSON contient le contenu du fichier sous la forme d’une chaîne codée en dur.

Cette fonction requiert **Bicep version 0.4.412 ou ultérieure**. 

La taille maximale autorisée du fichier est de **96 Ko**.

### <a name="return-value"></a>Valeur de retour

Le fichier est sous la forme d’une chaîne base64.

## <a name="loadtextcontent"></a>loadTextContent

`loadTextContent(filePath, [encoding])`

Charge le contenu du fichier spécifié sous forme de chaîne. 

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| filePath | Oui | string | Chemin d'accès vers le fichier à charger. Le chemin d’accès est relatif au fichier Bicep déployé. |
| encodage | Non | string | Encodage du fichier. La valeur par défaut est `utf-8`. Les options disponibles sont : `iso-8859-1` , `us-ascii` , `utf-16` , `utf-16BE` ou `utf-8`.  |

### <a name="remarks"></a>Remarques

Utilisez cette fonction lorsque vous avez du contenu davantage stocké dans un fichier séparé. Au lieu de dupliquer le contenu de votre fichier Bicep, chargez le contenu avec cette fonction. Par exemple, vous pouvez charger un script de déploiement à partir d’un fichier. Le fichier est chargé lorsque le fichier Bicep est compilé dans un modèle JSON. Pendant le déploiement, le modèle JSON contient le contenu du fichier sous la forme d’une chaîne codée en dur.

Lors du chargement d’un fichier JSON, vous pouvez utiliser la fonction [json](bicep-functions-object.md#json) avec la fonction loadTextContent pour créer un objet JSON. Dans VS Code, les propriétés de l’objet chargé sont disponibles dans intellisense. Par exemple, vous pouvez créer un fichier avec des valeurs à partager entre plusieurs fichiers Bicep. Un exemple est présenté dans cet article.

Cette fonction requiert **Bicep version 0.4.412 ou ultérieure**.

La taille maximale autorisée du fichier est de **131 072 caractères**, y compris les fins de ligne.

### <a name="return-value"></a>Valeur de retour

Contenu du fichier sous la forme d'une chaîne.

### <a name="examples"></a>Exemples

L’exemple suivant charge un script à partir d’un fichier et l’utilise pour un script de déploiement.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loaddeploymentscript.bicep" highlight="13" :::

Dans l’exemple suivant, vous créez un fichier JSON qui contient des valeurs que vous souhaitez utiliser pour un groupe de sécurité réseau.

::: code language="json" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/nsg-security-rules.json" :::

Vous chargez ce fichier et vous le convertissez en un objet JSON. Vous utilisez l’objet pour assigner des valeurs à la ressource.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loadsharedrules.bicep" highlight="3,13-21" :::

Vous pouvez réutiliser le fichier de valeurs dans d’autres fichiers Bicep qui déploient un groupe de sécurité réseau.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [Comprendre la structure et la syntaxe des fichiers Bicep](./file.md).
