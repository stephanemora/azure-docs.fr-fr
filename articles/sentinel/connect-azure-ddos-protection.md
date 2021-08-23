---
title: Connecter des données Azure DDoS Protection à Azure Sentinel
description: Découvrez comment ingérer des données Azure DDoS Protection dans Azure Sentinel afin de pouvoir les afficher, les analyser et les examiner.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 999555624ad4e88058a575dce3fb9cd606228b18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562603"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Connecter des données à partir d’Azure DDoS Protection

Une attaque par déni de service distribué (DDoS) tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent être ciblées sur n’importe quel point de terminaison qui est publiquement accessible via Internet. Combiné aux meilleures pratiques en matière de conception d’application, [Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md) assure une protection robuste contre les attaques DDoS. Vous pouvez connecter des journaux Azure DDoS Protection à Azure Sentinel, ce qui vous permet d’afficher les données des journaux dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer vos investigations.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez avoir configuré un [plan de protection Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Vous devez avoir configuré un [réseau virtuel sur lequel Azure DDoS Standard est activé](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Se connecter à Azure DDoS Protection
    
1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez **Azure DDoS Protection** dans la galerie de connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Activez **Journaux de diagnostic** sur toutes les adresses IP publiques dont vous souhaitez connecter les journaux :

    1. Sélectionnez le lien **Ouvrir les paramètres de diagnostic >** et choisissez une ressource **IP publique** dans la liste.

    1. Sélectionnez **+ Ajouter le paramètre de diagnostic**.

    1. Dans l’écran **Paramètres de diagnostic** :
       - Entrez un nom dans le champ **Nom du paramètre de diagnostic**.

       - Activez la case à cocher **Envoyer à Log Analytics**. Deux nouveaux champs s’affichent sous celle-ci. Choisissez l’**Abonnement** approprié et l’**Espace de travail Log Analytics** (où se trouve Azure Sentinel).

       - Activez les cases à cocher des types de règles dont vous souhaitez ingérer les journaux. Nous vous recommandons d’utiliser **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** et **DDoSMitigationReports**.

    1. Cliquez sur **Enregistrer** dans la partie supérieure de l’écran. Répétez ce processus pour tous les pare-feu supplémentaires (IP publiques) pour lesquels vous avez activé la protection DDoS.

1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes d’Azure DDoS Protection, recherchez **AzureDiagnostics**.

> [!NOTE]
>
> Avec ce connecteur de données particulier, les indicateurs d’état de connectivité (une bande de couleur dans la galerie de connecteurs de données et des icônes de connexion en regard des noms de types de données) s’affichent comme *Connectés* (en vert) uniquement si les données ont été ingérées au cours des deux dernières semaines. Après deux semaines passées sans ingestion de données, le connecteur déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter des journaux Azure DDoS Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
