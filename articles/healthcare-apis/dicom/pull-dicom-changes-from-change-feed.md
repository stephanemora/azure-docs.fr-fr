---
title: Extraire les modifications DICOM à l’aide du flux de modification
description: Ce guide de procédures explique comment extraire les modifications DICOM à l’aide du flux de modification DICOM pour les API de santé Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fa0237a57f5ebb8df0a69fa715a36d963ea0748f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122433614"
---
# <a name="pull-dicom-changes-using-the-change-feed"></a>Extraire les modifications DICOM à l’aide du flux de modification

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le flux de modification DICOM offre aux clients la possibilité de parcourir l’historique du service DICOM et d’agir sur les événements de création et de suppression dans le service. Ce guide pratique décrit comment utiliser le flux de modification.

Le flux de modification est accessible à l’aide des API REST. Ces API, ainsi que l’utilisation des exemples de flux de modification, sont documentées dans la [vue d’ensemble du flux de modification DICOM](dicom-change-feed-overview.md). La version de l’API REST doit être explicitement spécifiée dans l’URL de la demande, comme indiqué dans la [documentation relative au contrôle de version de l’API pour le service DICOM](api-versioning-dicom-service.md).

## <a name="consume-change-feed"></a>Consommer le flux de modification

L’exemple de code C# suivant montre comment utiliser le flux de modification à l’aide du package client DICOM.

```csharp
const int limit = 10;
 
using HttpClient httpClient = new HttpClient { BaseAddress = new Uri("<URL>") };
using CancellationTokenSource tokenSource = new CancellationTokenSource();
 
int read;
List<ChangeFeedEntry> entries = new List<ChangeFeedEntry>();
DicomWebClient client = new DicomWebClient(httpClient);
do
{
    read = 0;
    DicomWebAsyncEnumerableResponse<ChangeFeedEntry> result = await client.GetChangeFeed(
        $"?offset={entries.Count}&limit={limit}&includeMetadata={true}",
        tokenSource.Token);
 
    await foreach (ChangeFeedEntry entry in result)
    {
        read++;
        entries.Add(entry);
    }
} while (read > 0);
```

Pour afficher et accéder à l’exemple de code **ChangeFeedRetrieveService. cs** , consultez [utilisation du flux de modification](https://github.com/microsoft/dicom-server/blob/main/converter/dicom-cast/src/Microsoft.Health.DicomCast.Core/Features/DicomWeb/Service/ChangeFeedRetrieveService.cs).

## <a name="next-steps"></a>Étapes suivantes

Ce guide pratique décrit comment utiliser le flux de modification. Le flux de modification vous permet de surveiller l’historique du service DICOM. Pour plus d’informations sur le service DICOM, consultez.

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
