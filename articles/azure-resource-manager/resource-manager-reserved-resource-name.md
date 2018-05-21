---
title: Erreurs de nom de ressource réservé Azure | Microsoft Docs
description: Explique comment résoudre les erreurs quand vous fournissez un nom de ressource qui inclut un mot réservé.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: b91a53d17d64afb0a56f745505f10e8cabbc22cc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-reserved-resource-name-errors"></a>Résoudre les erreurs de nom de ressource réservé

Cet article décrit l’erreur que vous rencontrez quand vous déployez une ressource dont le nom inclut un mot réservé.

## <a name="symptom"></a>Symptôme

Au cours du déploiement d’une ressource qui est disponible par le biais d’un point de terminaison public, vous pouvez rencontrer l’erreur suivante :

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Cause :

Le nom d’une ressource qui a un point de terminaison public ne peut pas contenir de mots réservés ou de marques.

Les mots suivants sont réservés :

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Les mots suivants ne peuvent pas être utilisés en tant que mot entier ou sous-chaîne dans le nom :

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solution

Fournissez un nom qui n’utilise aucun des mots réservés.