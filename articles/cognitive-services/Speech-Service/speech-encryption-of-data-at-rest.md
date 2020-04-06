---
title: Chiffrement du service Speech pour les données au repos
titleSuffix: Azure Cognitive Services
description: Chiffrement du service Speech pour les données au repos.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372082"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Chiffrement du service Speech pour les données au repos

Le service Speech chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement du service Speech protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

## <a name="about-cognitive-services-encryption"></a>À propos du chiffrement de Cognitive Services

Les données sont chiffrées et déchiffrées à l'aide du chiffrement [AES 256 bits](https://en.wikipedia.org/wiki/FIPS_140-2) certifié [FIPS 140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). Le chiffrement et le déchiffrement sont transparents, ce qui signifie que le chiffrement et l’accès sont gérés automatiquement. Comme vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti du chiffrement.

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Lorsque vous utilisez Custom Speech et Custom Voice, le service Speech peut stocker les données suivantes dans le cloud :  

* Données de trace Speech : uniquement si vous activez la trace pour votre point de terminaison personnalisé
* Données de formation et de test chargées

Par défaut, vos données sont stockées dans un compte de stockage Microsoft, et votre abonnement utilise des clés de chiffrement gérées par Microsoft. Vous pouvez également préparer votre propre compte de stockage. L’accès au magasin est géré par la fonctionnalité Identité managée, et le service Speech ne peut pas accéder directement à vos propres données, notamment les données de trace Speech, les données de formation de personnalisation et les modèles personnalisés.

Pour plus d’informations sur Identité managée, consultez [Que sont les identités managées ?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Mode Bring your own storage (BYOS) pour la personnalisation et la journalisation

Pour demander l’accès à la fonctionnalité Mode Bring your own storage (BYOS), remplissez puis envoyez le  [formulaire de demande Service Speech - bring your own storage (BYOS)](https://aka.ms/cogsvc-cmk). Une fois votre demande approuvée, vous devrez créer votre propre compte de stockage pour y stocker les données requises pour la personnalisation et la journalisation. Lorsque vous ajoutez un compte de stockage, la ressource du service Speech active une identité managée attribuée par le système. Une fois que l’identité managée affectée par le système est activée, cette ressource est inscrite auprès d’Azure Active Directory (AAD). Une fois inscrite, l’identité managée aura accès au compte de stockage. Vous trouverez ici plus d’informations sur les identités managées. Pour plus d’informations sur Identité managée, consultez [Que sont les identités managées ?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Si vous désactivez les identités managées attribuées par le système, l’accès au compte de stockage sera supprimé. Les composants du service Speech qui requièrent l’accès au compte de stockage cesseront de fonctionner.  

## <a name="regional-availability"></a>Disponibilité régionale

BYOS est actuellement disponible dans les régions suivantes :

* USA Centre Sud
* USA Ouest 2
* USA Est

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande Service Speech - bring your own storage (BYOS)](https://aka.ms/cogsvc-cmk)
* [Que sont les identités managées ?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
