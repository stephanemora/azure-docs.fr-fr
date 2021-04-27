---
author: baanders
description: fichier include décrivant une solution à code pour la limitation interlocataire avec Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589332"
---
L’exemple suivant montre comment définir une valeur d’ID de locataire pour `InteractiveBrowserTenantId` dans les options `DefaultAzureCredential` :

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="Capture d’écran du code montrant la méthode DefaultAzureCredentialOptions. La valeur de InteractiveBrowserTenantId est définie sur un exemple de valeur d’ID de locataire.":::

Des options similaires sont disponibles pour définir un locataire pour l’authentification avec Visual Studio et Visual Studio Code. Pour plus d’informations sur les options disponibles, consultez la [documentation de DefaultAzureCredentialOptions](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true).