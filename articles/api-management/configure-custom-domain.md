---
title: Configurer un nom de domaine personnalisé pour une instance de gestion des API Azure | Microsoft Docs
description: Cette rubrique explique comment configurer un nom de domaine personnalisé pour votre instance de gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: a8bfa7c5baa316b4019480bfc146b6cc61eff979
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141300"
---
# <a name="configure-a-custom-domain-name"></a>Configuration d’un nom de domaine personnalisé 

Lorsque vous créez une instance de gestion des API (APIM), Azure l’affecte à un sous-domaine d’azure-api.net (par exemple, `apim-service-name.azure-api.net`). Toutefois, vous pouvez également exposer vos points de terminaison APIM via votre propre nom de domaine, par exemple, **contoso.com**. Ce didacticiel explique comment mapper un nom DNS personnalisé existant à des points de terminaison exposés par une instance de gestion des API Azure.

> [!WARNING]
> Les clients qui souhaitent utiliser un épinglage de certificat pour améliorer la sécurité de leurs applications doivent utiliser un nom de domaine personnalisé > et le certificat qu’ils gèrent, pas le certificat par défaut. Les clients qui épinglent le certificat par défaut à la place > prendront une dépendance dure sur les propriétés du certificat qu’ils ne contrôlent pas, ce qui n’est pas une pratique recommandée.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
+ Un nom de domaine personnalisé qui vous appartient. Vous devez vous procurer séparément le nom de domaine personnalisé que vous souhaitez utiliser. De plus, il doit être hébergé sur un serveur DNS. Cette rubrique ne donne aucune instruction sur l’hébergement d’un nom de domaine personnalisé.
+ Vous devez disposer d’un certificat valide et d’une clé publique et privée (.PFX). Le sujet ou l’autre nom du sujet doit correspondre au nom du domaine. Cela permet à APIM d’exposer des URL de manière sécurisée, via SSL.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utiliser le portail Azure pour définir un nom de domaine personnalisé

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance APIM.
1. Sélectionnez **Domaines personnalisés et SSL**.
    
    Il existe un nombre de points de terminaison à laquelle vous pouvez affecter un nom de domaine personnalisé. Actuellement, les points de terminaison disponibles sont les suivants : 
   + **Proxy** (valeur par défaut : `<apim-service-name>.azure-api.net`) 
   + **Portail** (valeur par défaut : `<apim-service-name>.portal.azure-api.net`)     
   + **Gestion** (valeur par défaut : `<apim-service-name>.management.azure-api.net`) 
   + **SCM** (valeur par défaut : `<apim-service-name>.scm.azure-api.net`)

     >[!NOTE]
     > Vous pouvez mettre à jour tous les points de terminaison ou certains d’entre eux. En règle générale, les clients mettent à jour les points de terminaison **Proxy** (cette URL est utilisée pour appeler l’API exposée via la gestion des API) et **Portal** (URL du portail des développeurs). Les points de terminaison **Gestion** et **SCM** sont utilisés en interne par les clients APIM. Pour cette raison, ils se voient moins fréquemment assigner un nom de domaine personnalisé.

1. Sélectionnez le point de terminaison que vous souhaitez mettre à jour. 
1. Dans la fenêtre de droite, cliquez sur **Personnalisé**.

   + Dans la zone **Nom de domaine personnalisé**, spécifiez le nom que vous souhaitez utiliser. Par exemple : `api.contoso.com`. Les noms de domaine avec des caractères génériques (par exemple, *.domaine.com) sont également pris en charge.
   + Dans le **certificat**, sélectionnez un certificat dans Key Vault. Vous pouvez également télécharger un valide. PFX fichier et fournir ses **mot de passe**, si le certificat est protégé par un mot de passe.

     > [!TIP]
     > Si vous utilisez Azure Key Vault pour gérer le certificat SSL de domaine personnalisé, assurez-vous que le certificat est inséré dans le coffre de clés [comme un *certificat*](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), et non un *secret*. Si le certificat est défini sur autorotate, gestion des API reprendra automatiquement la dernière version.

1. Cliquez sur Appliquer.

    >[!NOTE]
    >Le processus d’attribution du certificat peut prendre 15 minutes ou plus, selon la taille du déploiement. La référence SKU de développeur présente des temps d’arrêt, mais pas les références SKU de base ou supérieures.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Étapes suivantes

[Mettre à niveau votre service et le mettre à l’échelle](upgrade-and-scale.md)
