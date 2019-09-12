---
title: Aide sur la limitation de requêtes Azure Key Vault
description: La limitation Key Vault permet de réduire le nombre d’appels simultanés afin d’empêcher toute surexploitation des ressources.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: f10f40551701cafd94692afc0916972b1fd73aff
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883045"
---
# <a name="azure-key-vault-throttling-guidance"></a>Aide sur la limitation de requêtes Azure Key Vault

La limitation de requêtes est le processus permettant de réduire le nombre d’appels simultanés au service Azure afin d’empêcher toute surexploitation des ressources. Azure Key Vault (AKV) est conçu pour gérer un volume élevé de requêtes. En cas d’affluence, la limitation des requêtes du client permet de maintenir des performances et une fiabilité optimales pour le service AKV.

Les limites varient selon les scénarios. Par exemple, si vous réalisez un volume important d’écritures, la possibilité de limitation est plus élevée que si vous effectuez uniquement des lectures.

## <a name="how-does-key-vault-handle-its-limits"></a>Comment Key Vault gère-t-il ses limites ?

Les limites de service de Key Vault sont là pour empêcher tout usage abusif des ressources et garantir la qualité du service pour tous les clients de Key Vault. En cas de dépassement d’un seuil de service, Key Vault limite toutes les autres requêtes de ce client sur une période donnée. Dans ce cas, Key Vault retourne le code d’état HTTP 429 (Trop de requêtes), et les requêtes échouent. Par ailleurs, les requêtes qui ont échoué et retournent une erreur 429 sont comptabilisées dans les limites suivies par Key Vault. 

Si vous avez un scénario valide justifiant une limitation supérieure, contactez-nous.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Guide pratique pour limiter une application en réponse à des limites de service

Les **bonnes pratiques** suivantes doivent être implémentées lorsque votre service est limité :
- Réduisez le nombre d’opérations par requête.
- Réduisez la fréquence des requêtes.
- Évitez les nouvelles tentatives immédiates. 
    - Toutes les requêtes sont comptabilisées dans le cadre de vos limites d’utilisation.

Lorsque vous implémentez la gestion des erreurs de votre application, utilisez le code d’erreur HTTP 429 pour détecter si une limitation côté client est nécessaire. Si la requête échoue à nouveau avec un code d’erreur HTTP 429, cela signifie que vous rencontrez toujours une limite de service Azure. Continuez à utiliser la méthode de limitation côté client recommandée, en réessayant la requête jusqu’à ce qu’elle aboutisse.

Le code qui implémente un backoff exponentiel est montré ci-dessous. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


L'utilisation de ce code dans une application cliente C\# est simple. L’exemple suivant montre comment procéder en utilisant la classe HttpClient.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

N’oubliez pas que ce code ne convient que comme preuve de concept. 

### <a name="recommended-client-side-throttling-method"></a>Méthode de limitation côté client recommandée

En cas de code d’erreur HTTP 429, commencez à limiter votre client suivant une approche d’interruption exponentielle :

1. Patientez une seconde, relancez la requête ;
2. Si la limitation persiste, patientez deux secondes, relancez la requête ;
3. Si la limitation persiste, patientez quatre secondes, relancez la requête ;
4. Si la limitation persiste, patientez huit secondes, relancez la requête ;
5. Si la limitation persiste, patientez seize secondes, relancez la requête.

À ce stade, vous ne devriez pas recevoir de code de réponse HTTP 429.

## <a name="see-also"></a>Voir aussi

Pour orienter la limitation de façon plus approfondie sur Microsoft Cloud, consultez la page [Modèle de limitation](https://docs.microsoft.com/azure/architecture/patterns/throttling).

