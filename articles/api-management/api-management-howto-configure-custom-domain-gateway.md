---
title: Configurer un nom de domaine personnalisé pour votre passerelle de gestion des API Azure auto-hébergée | Microsoft Docs
description: Cette rubrique décrit les étapes de configuration d’un nom de domaine personnalisé pour une passerelle de gestion des API Azure auto-hébergée.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99490994"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Configurer un nom de domaine personnalisé pour une passerelle auto-hébergée

Lorsque vous configurez une [passerelle Gestion des API Azure auto-hébergée](self-hosted-gateway-overview.md), aucun nom d’hôte ne lui est pas attribué et vous devez y faire référence par adresse IP. Cet article montre comment mapper un nom DNS personnalisé existant (également appelé nom d’hôte) à une passerelle auto-hébergée.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

-   Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
- Une passerelle auto-hébergée. Pour plus d’informations, consultez [Comment configurer une passerelle auto-hébergée](api-management-howto-provision-self-hosted-gateway.md)
-   Un nom de domaine personnalisé qui vous appartient ou à votre organisation. Cette rubrique ne fournit aucune instruction sur l’approvisionnement d’un nom de domaine personnalisé.
-   Un enregistrement DNS hébergé sur un serveur DNS qui mappe le nom de domaine personnalisé à l’adresse IP de la passerelle auto-hébergée. Cette rubrique ne fournit aucune instruction sur l’hébergement d’un enregistrement DNS.
-   Vous devez disposer d’un certificat valide et d’une clé publique et privée (.PFX). Le sujet ou l’autre nom du sujet (SAN) doit correspondre au nom de domaine. Cela permet à l’instance de Gestion des API d’exposer les URL de manière sécurisée sur TLS.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Ajouter un certificat de domaine personnalisé à votre service Gestion des API

Ajoutez un fichier de certificat de domaine personnalisé (.PFX) à votre instance de Gestion des API, ou faites référence à un certificat stocké dans Azure Key Vault. Suivez les étapes décrites dans [Sécuriser les services principaux à l’aide d’une authentification par certificat client dans Gestion des API Azure](api-management-howto-mutual-certificates.md).

> [!NOTE]
> Nous vous recommandons d’utiliser un certificat de coffre de clés pour le domaine de passerelle auto-hébergé.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Utiliser le portail Azure pour définir un nom de domaine personnalisé pour votre passerelle auto-hébergée

1. Sélectionnez **Passerelles** sous **Déploiement et infrastructure**.
2. Sélectionnez la passerelle auto-hébergée pour laquelle vous souhaitez configurer le nom de domaine.
3. Sélectionnez **Noms d’hôtes** sous **Paramètres**.
4. Sélectionnez **+ Ajouter**
5. Entrez le nom de la ressource pour le nom d’hôte dans le champ **Nom**.
6. Entrez le nom de domaine dans le champ **Nom d’hôte**.
7. Sélectionnez un certificat dans la liste déroulante **Certificat**.
8. Activez la case à cocher **Négocier le certificat client** si l’une des API exposées via cette passerelle utilise l’authentification par certificat client.
    > [!WARNING]
    > Ce paramètre est partagé par tous les noms de domaine configurés pour la passerelle.
9. Sélectionnez **Ajouter** pour attribuer le nom de domaine personnalisé à la passerelle auto-hébergée sélectionnée.

## <a name="next-steps"></a>Étapes suivantes

[Mettre à niveau votre service et le mettre à l’échelle](upgrade-and-scale.md)
