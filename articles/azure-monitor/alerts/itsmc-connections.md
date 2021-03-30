---
title: Connecteur de gestion des services informatiques dans Azure Monitor
description: Cet article fournit des informations vous indiquant comment connecter vos produits/services ITSM à IT Service Management Connector (ITSMC) dans Azure Monitor pour surveiller et gérer les éléments de travail ITSM de manière centralisée.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009315"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Connecter des produits/services ITSM à IT Service Management Connector
Cet article fournit des informations vous indiquant comment configurer la connexion entre votre produit/service ITSM au connecteur de gestion des services informatiques (ITSMC) dans Log Analytics pour gérer de manière centralisée vos éléments de travail. Pour plus d’informations sur le connecteur ITSM, consultez [Présentation](./itsmc-overview.md).

Les produits/services ITSM suivants sont pris en charge. Sélectionnez le produit pour afficher des informations détaillées sur la connexion du produit à ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Nous proposons à nos clients Cherwell et Provance d’utiliser une [action webhook](./action-groups.md#webhook) pour point de terminaison Cherwell et Provance en guise d’autre solution pour l’intégration.

## <a name="ip-ranges-for-itsm-partners-connections"></a>Plages d’adresses IP pour les connexions des partenaires ITSM
Pour répertorier les adresses IP ITSM afin d’autoriser les connexions ITSM d’outils ITSM de partenaires, nous vous recommandons de répertorier l’ensemble de la plage d’IP publiques de la région Azure à laquelle appartient l’espace de travail LogAnalytics. [détails ici](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Pour les régions EUS/WEU/EUS2/WUS2/USA Centre Sud, le client peut répertorier la balise réseau ActionGroup uniquement.

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes dans le connecteur ITSM](./itsmc-resync-servicenow.md)
