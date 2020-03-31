---
title: Que sont les partenaires de sécurité de confiance Azure Firewall Manager (préversion)
description: Découvrez les partenaires de sécurité de confiance Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436770"
---
# <a name="what-are-trusted-security-partners-preview"></a>Que sont les partenaires de sécurité de confiance (préversion) ?

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les *partenaires de sécurité de confiance (préversion)* dans Azure Firewall Manager vous permettent d’utiliser les meilleures offres SECaaS (sécurité en tant que service) tierces et familières afin de protéger l’accès à Internet pour vos utilisateurs.

Au moyen d’une configuration rapide, vous pouvez sécuriser un hub avec un partenaire de sécurité pris en charge, et router et filtrer le trafic Internet à partir de vos réseaux virtuels ou positions de filiale au sein d’une région. Cette opération s’effectue à l’aide de la gestion automatisée des routes, sans configuration ni gestion des routes définies par l’utilisateur.

Vous pouvez déployer des hubs sécurisés configurés avec le partenaire de sécurité de votre choix dans plusieurs régions Azure pour que vos utilisateurs bénéficient de la connectivité et de la sécurité n’importe où dans le monde entier dans ces régions. Avec la possibilité d’utiliser l’offre du partenaire de sécurité pour le trafic des applications SaaS/Internet et Pare-feu Azure pour le trafic privé dans les hubs sécurisés, vous pouvez maintenant commencer à créer sur Azure un périmètre de sécurité proche de vos utilisateurs et applications répartis dans le monde entier.

Pour cette préversion, les partenaires de sécurité pris en charge sont **ZScaler** et **iboss**. Les régions prises en charge sont USA Centre-Ouest, USA Centre Nord, USA Ouest, USA Ouest 2 et USA Est.

![Partenaires de sécurité de confiance](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Principaux scénarios

Vous pouvez utiliser les partenaires de sécurité pour filtrer le trafic Internet dans les scénarios suivants :

- Réseau virtuel vers Internet

   Tirez parti de la protection Internet avancée prenant en charge l’utilisateur pour vos charges de travail cloud s’exécutant sur Azure.

- Filiale vers Internet

   Tirez parti de votre connectivité Azure et de votre distribution mondiale pour ajouter facilement un filtrage NSaaS tiers dans le cadre des scénarios de type « filiale vers Internet ». Vous pouvez créer votre réseau de transit mondial et votre périmètre de sécurité à l’aide d’Azure Virtual WAN.

Les scénarios suivants sont pris en charge :
-   Réseau virtuel vers Internet via une offre partenaire tierce.
-   Filiale vers Internet via une offre partenaire tierce.
-   Filiale vers Internet via une offre partenaire tierce, le reste du trafic privé (spoke vers spoke, spoke vers filiale, filiale vers spoke) via Pare-feu Azure.

Le scénario suivant n’est pas pris en charge :

- La configuration « réseau virtuel vers Internet » via une offre partenaire ne peut pas être combinée avec Pare-feu Azure pour le trafic privé. Consultez les limites ci-après.

## <a name="current-limitations"></a>Limites actuelles

- Pour la configuration « réseau virtuel vers Internet », vous ne pouvez pas ajouter ensemble Pare-feu Azure pour le trafic privé et une offre partenaire pour le trafic Internet. Vous pouvez envoyer le trafic Internet vers Pare-feu Azure ou une offre de partenaire de sécurité tierce dans le hub virtuel sécurisé, mais pas vers les deux. 
- Vous pouvez déployer au plus un partenaire de sécurité par hub virtuel. Si vous avez besoin de changer de fournisseur, vous devez supprimer le partenaire existant et en ajouter un nouveau.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Bonnes pratiques pour le filtrage du trafic Internet dans les hubs virtuels sécurisés

Le trafic Internet comprend généralement le trafic web. Toutefois, il comprend également le trafic destiné aux applications SaaS comme Office 365 (O365) et aux services PaaS publics Azure tels que Stockage Azure et Azure SQL. Voici les bonnes pratiques recommandées pour gérer le trafic vers ces services :

### <a name="handling-azure-paas-traffic"></a>Gestion du trafic PaaS Azure
 
- Utilisez Pare-feu Azure pour la protection si votre trafic se compose principalement de données Azure PaaS et que l’accès aux ressources pour vos applications peut être filtré à l’aide d’adresses IP, de noms de domaine complets, d’étiquettes de service ou d’étiquettes de nom de domaine complet.

- Utilisez une solution de partenaire tierce dans vos hubs si votre trafic est constitué d’un accès aux applications SaaS, si vous avez besoin d’un filtrage prenant en charge l’utilisateur (par exemple, pour vos charges de travail d’infrastructure de bureau virtuel) ou si vous avez besoin de fonctionnalités de filtrage Internet avancées.

![Tous les scénarios pour Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Gestion du trafic Office 365 (O365)

Dans les scénarios de positions de filiales à l’échelle mondiale, vous devez rediriger le trafic Office 365 directement au niveau de la filiale avant d’envoyer le reste du trafic Internet à votre hub sécurisé Azure.

Pour Office 365, la latence et les performances du réseau sont essentielles pour une expérience utilisateur réussie. Pour que les performances et l’expérience utilisateur soient optimales, les clients doivent implémenter un dispositif de déchargement direct et local pour Office 365 avant d’envisager de router le reste du trafic Internet via Azure.

Selon les [principes de connectivité réseau Office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles), les principales connexions réseau Office 365 doivent être routées localement à partir de l’appareil mobile ou de la filiale où se trouvent les utilisateurs et directement via Internet vers le point de présence réseau Microsoft le plus proche.

De plus, les connexions Office 365 sont fortement chiffrées pour protéger la confidentialité et utilisent des protocoles propriétaires efficaces pour des raisons de performances. C’est pourquoi il est peu pratique et trop risqué de soumettre ces connexions à des solutions de sécurité de niveau réseau traditionnelles. Pour ces raisons, nous recommandons vivement aux clients d’envoyer le trafic Office 365 directement à partir des filiales, avant d’envoyer le reste du trafic via Azure. Microsoft s’est associé à plusieurs fournisseurs de solutions SD-WAN, qui s’intègrent à Azure et à Office 365 et permettent aux clients d’activer le déchargement direct et local du trafic Internet pour Office 365. Pour plus d’informations, consultez [Comment définir mes stratégies O365 via Virtual WAN ?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview).


## <a name="next-steps"></a>Étapes suivantes

[Déployez une offre de sécurité de confiance dans un hub sécurisé à l’aide d’Azure Firewall Manager](deploy-trusted-security-partner.md).
