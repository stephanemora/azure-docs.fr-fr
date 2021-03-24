---
title: Paramètres DNS de la stratégie de Pare-feu Azure
description: Vous pouvez configurer les stratégies de pare-feu Azure avec les paramètres de serveur DNS et de proxy DNS.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633646"
---
# <a name="azure-firewall-policy-dns-settings"></a>Paramètres DNS de la stratégie de Pare-feu Azure

Vous pouvez configurer un serveur DNS personnalisé et activer le proxy DNS pour les stratégies de Pare-feu Azure. Vous pouvez configurer ces paramètres lorsque vous déployez le pare-feu ou plus tard à partir de la page **Paramètres DNS**.

## <a name="dns-servers"></a>Serveurs DNS

Un serveur DNS gère et résout les noms de domaine en adresses IP. Par défaut, le Pare-feu Azure utilise Azure DNS pour la résolution de noms. Le paramètre **Serveur DNS** vous permet de configurer vos propres serveurs DNS pour la résolution de noms du Pare-feu Azure. Vous pouvez configurer un ou plusieurs serveurs.

### <a name="configure-custom-dns-servers"></a>Configurer des serveurs DNS personnalisés

1. Sélectionnez votre stratégie de pare-feu.
2. Dans **Paramètres**, sélectionnez **Paramètres DNS**.
3. Dans **Serveurs DNS**, vous pouvez saisir ou ajouter des serveurs DNS existants qui ont été précédemment spécifiés dans votre réseau virtuel.
4. Sélectionnez **Enregistrer**.
5. Le pare-feu dirige désormais le trafic DNS vers le ou les serveurs DNS spécifiés pour la résolution de noms.

## <a name="dns-proxy"></a>Proxy DNS

Vous pouvez configurer le Pare-feu Azure pour qu’il agisse comme proxy DNS. Un proxy DNS agit comme un intermédiaire pour les requêtes DNS des machines virtuelles clientes à un serveur DNS. Si vous configurez un serveur DNS personnalisé, vous devez activer le proxy DNS pour éviter les incompatibilités de résolution DNS et activer le filtrage de nom de domaine complet dans les règles de réseau.

Si vous n’activez pas le proxy DNS, les requêtes DNS du client peuvent se déplacer vers un serveur DNS à une heure différente ou retourner une réponse différente de celle du pare-feu. Le proxy DNS place le Pare-feu Azure sur le trajet des demandes du client pour éviter toute incohérence.

La configuration du proxy DNS nécessite trois étapes :

1. Activez le proxy DNS dans les paramètres DNS du Pare-feu Azure.
2. Éventuellement, configurez votre serveur DNS personnalisé ou utilisez la valeur par défaut fournie.
3. Enfin, vous devez configurer l’adresse IP privée du Pare-feu Azure en tant qu’adresse DNS personnalisée dans les paramètres du serveur DNS de votre réseau virtuel. Cela garantit que le trafic DNS est dirigé vers le Pare-feu Azure.

### <a name="configure-dns-proxy"></a>Configurer un proxy DNS

Pour configurer le proxy DNS, vous devez configurer le paramètre des serveurs DNS de votre réseau virtuel pour utiliser l’adresse IP privée du pare-feu. Ensuite, activez le proxy DNS dans les **paramètres DNS** de la stratégie de Pare-feu Azure.

#### <a name="configure-virtual-network-dns-servers"></a>Configurer les serveurs DNS du réseau virtuel

1. Sélectionnez le réseau virtuel dans lequel le trafic DNS sera routé via le Pare-feu Azure.
2. Sous **Paramètres**, sélectionnez **Serveurs DNS**.
3. Sélectionnez **Personnalisé** sous **Serveurs DNS**.
4. Saisissez l’adresse IP privée du pare-feu.
5. Sélectionnez **Enregistrer**.

#### <a name="enable-dns-proxy"></a>Activer le proxy DNS

1. Sélectionnez votre stratégie de Pare-feu Azure.
2. Dans **Paramètres**, sélectionnez **Paramètres DNS**.
3. Le **proxy DNS** est désactivé par défaut. Lorsqu’il est activé, le pare-feu écoute le port 53 et transfère les requêtes DNS aux serveurs DNS configurés.
4. Vérifiez la configuration des **serveurs DNS** pour vous assurer que les paramètres sont adaptés à votre environnement.
5. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Filtrage de nom de domaine complet dans les règles de réseau](fqdn-filtering-network-rules.md)