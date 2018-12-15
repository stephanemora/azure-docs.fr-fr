---
title: Écrire du code pour effectuer le suivi des requêtes avec Azure Application Insights | Microsoft Docs
description: Écrivez du code pour effectuer le suivi des requêtes avec Application Insights afin d’obtenir des profils pour vos requêtes.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722120"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Écrire du code pour effectuer le suivi des requêtes avec Application Insights

Pour afficher les profils pour votre application dans la page Performances, Application Insights doit effectuer le suivi des requêtes pour votre application. Application Insights peut effectuer automatiquement le suivi des requêtes pour les applications qui reposent sur des frameworks déjà instrumentés, comme ASP.net et ASP.Net Core. En revanche, pour d’autres applications telles que les rôles de travail Azure Cloud Service et les API sans état Service Fabric, vous devez écrire du code pour indiquer à Application Insights où vos requêtes commencent et se terminent. Une fois que vous aurez écrit ce code, les données de télémétrie relatives aux requêtes seront envoyées à Application Insights ; vous verrez les données de télémétrie dans la page Performances et les profils seront collectés pour ces requêtes. 

Voici les étapes que vous devez effectuer pour suivre manuellement les requêtes :


  1. Ajoutez le code suivant au début de la durée de vie de l’application :  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Pour plus d’informations sur cette configuration de clé d’instrumentation générale, consultez [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Utiliser Service Fabric avec Application Insights).  

  1. Pour tout bloc de code que vous souhaitez instrumenter, encadrez-le d’une instruction **USING** `StartOperation<RequestTelemetry>`, comme indiqué dans l’exemple suivant :

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        L’appel de `StartOperation<RequestTelemetry>` dans une autre étendue `StartOperation<RequestTelemetry>` n’est pas pris en charge. Vous pouvez utiliser `StartOperation<DependencyTelemetry>` dans l’étendue imbriquée à la place. Par exemple :   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
