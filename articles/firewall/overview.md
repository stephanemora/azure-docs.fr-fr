---
title: Qu’est-ce qu’un pare-feu Azure ?
description: Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 07/01/2021
ms.author: victorh
ms.openlocfilehash: e87953217fe44fedc8d693a40a1f3ae942a06a39
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113216366"
---
# <a name="what-is-azure-firewall"></a>Qu’est-ce qu’un pare-feu Azure ?

![Certification ICSA](media/overview/icsa-cert-firewall-small.png)

Pare-feu Azure est un service de sécurité réseau informatique géré qui protège vos ressources Réseau virtuel Azure. Il s’agit d’un service de pare-feu avec état intégral, doté d’une haute disponibilité intégrée et d’une scalabilité illimitée dans le cloud.

![Présentation du pare-feu](media/overview/firewall-threat.png)

Vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels. Pare-feu Azure utilise une adresse IP publique statique pour vos ressources de réseau virtuel, ce qui permet aux pare-feu situés à l’extérieur d’identifier le trafic provenant de votre réseau virtuel.  Le service est totalement intégré à Azure Monitor pour la journalisation et les analyses.

Pour découvrir les fonctionnalités du Pare-feu Azure, consultez [Fonctionnalités du Pare-feu Azure](features.md).

## <a name="azure-firewall-premium-preview"></a>Pare-feu Azure Premium - Préversion

Pare-feu Azure Premium - Préversion est un pare-feu de nouvelle génération avec des fonctionnalités qui sont nécessaires pour les environnements hautement sensibles et réglementés. Ces fonctionnalités incluent l’inspection TLS, IDPS, le filtrage d’URL et les catégories web.

Pour plus d’informations sur les fonctionnalités du Pare-feu Azure Premium - Préversion, consultez [Fonctionnalités du Pare-feu Azure Premium - Préversion](premium-features.md).


Pour voir le Pare-feu Premium - Préversion est configuré dans le portail Azure, consultez [Pare-feu Azure Premium - Préversion dans le portail Azure](premium-portal.md).


## <a name="pricing-and-sla"></a>Tarifs et contrat SLA

Pour plus d’informations sur les tarifs du Pare-feu Azure, consultez [Tarifs du Pare-feu Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

Pour plus d’informations sur le contrat de niveau de service du Pare-feu Azure, consultez [Contrat SLA du Pare-feu Azure](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Nouveautés

Pour découvrir les nouveautés du Pare-feu Azure, consultez [Mises à jour Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Problèmes connus

Les problèmes connus du service Pare-feu Azure sont les suivants :

|Problème  |Description  |Limitation des risques  |
|---------|---------|---------|
|Les règles de filtrage réseau pour les protocoles autres que TCP/UDP (par exemple ICMP) ne fonctionnent pas pour le trafic lié à Internet.|Les règles de filtrage réseau des protocoles autres que TCP/UDP ne fonctionnent pas avec SNAT pour votre adresse IP publique. Les protocoles autres que TCP/UDP sont pris en charge entre les sous-réseaux du rayon et les réseaux virtuels.|Le service Pare-feu Azure utilise Standard Load Balancer, [qui ne prend pas en charge SNAT pour les protocoles IP pour le moment](../load-balancer/load-balancer-overview.md). Nous étudions les possibilités de prendre en charge ce scénario dans une prochaine version.|
|Protocole ICMP non pris en charge par PowerShell et l’interface de ligne de commande|Azure PowerShell et l’interface CLI ne prennent pas en charge le protocole ICMP en tant que protocole valide dans les règles de réseau.|Il est toujours possible d’utiliser le protocole ICMP par le biais du portail et de l’API REST. Nous travaillons actuellement à ajouter à PowerShell et à l’interface de ligne de commande la prise en charge du protocole ICMP.|
|Les balises FQDN requièrent une définition protocole : port|Les règles d’application avec des balises FQDN requièrent une définition port : protocole.|Vous pouvez utiliser **https** en tant que valeur port : protocole. Nous travaillons actuellement à rendre ce champ facultatif lorsque des balises FQDN sont utilisées.|
|Le déplacement d’un pare-feu vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge|Le déplacement d’un pare-feu vers un autre groupe de ressources ou un autre abonnement n’est pas pris en charge.|La prise en charge de cette fonctionnalité figure sur notre feuille de route. Pour déplacer un pare-feu vers un autre groupe de ressources ou un autre abonnement, vous devez supprimer l’instance actuelle et la recréer dans le nouveau groupe de ressources ou le nouvel abonnement.|
|Les alertes Threat intelligence peuvent être masquées|Les règles de réseau avec la destination 80/443 pour le filtrage sortant masque les alertes intelligentes de menaces lorsqu’elles sont configurées pour en mode alerte uniquement.|Créez un filtrage sortant pour 80/443 à l’aide de règles d’application. Ou modifiez le mode d’intelligence contre les menaces pour **alerter et rejeter**.|
|La fonction DNAT du Pare-feu Azure ne fonctionne pas pour les destinations IP privées|La prise en charge de la fonction DNAT du Pare-feu Azure est limitée aux sorties/entrées Internet. Actuellement, DNAT ne fonctionne pas pour les destinations IP privées. Par exemple, spoke-à-spoke.|Il s’agit d’une limitation actuelle.|
|Impossible de supprimer la première configuration IP publique|Chaque adresse IP publique du Pare-feu Azure est affectée à une *configuration IP*.  La première configuration IP est affectée pendant le déploiement du pare-feu. En général, elle contient une référence au sous-réseau du pare-feu (sauf si elle est configurée autrement de manière explicite via un déploiement de modèle). Vous ne pouvez pas supprimer cette configuration IP, car cela aurait pour effet de désallouer le pare-feu. Vous pouvez toujours changer ou supprimer l’adresse IP publique qui est associée à cette configuration IP si le pare-feu comprend au moins une autre adresse IP publique disponible.|C'est la procédure normale.|
|Les zones de disponibilité ne peuvent être configurées que pendant le déploiement.|Les zones de disponibilité ne peuvent être configurées que pendant le déploiement. Vous ne pouvez pas configurer les Zones de disponibilité après le déploiement d’un pare-feu.|C'est la procédure normale.|
|SNAT sur les connexions entrantes|En plus de DNAT, les connexions via l’adresse IP publique du pare-feu (entrante) sont traduites vers l’une des adresses IP privées du pare-feu. Cette exigence actuelle (également pour les appliances virtuelles réseau Active/Active) permet d’assurer la symétrie du routage.|Pour préserver la source originale pour HTTP/S, pensez à utiliser les en-têtes [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For). Par exemple, utilisez un service tel que [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) ou [Azure Application Gateway](../application-gateway/rewrite-http-headers-url.md) devant le pare-feu. Vous pouvez également ajouter le pare-feu d’applications web en tant qu’Azure Front Door et l’associer au pare-feu.
|Prise en charge du filtrage FQDN SQL uniquement en mode proxy (port 1433)|Pour Azure SQL Database, Azure Synapse Analytics et Azure SQL Managed Instance :<br><br>Le filtrage FQDN SQL est pris en charge uniquement en mode proxy (port 1433).<br><br>Pour Azure SQL IaaS :<br><br>Si vous utilisez des ports non standard, vous pouvez spécifier ces ports dans les règles de l’application.|Pour SQL en mode de redirection (l’option par défaut si vous vous connectez à partir d’Azure), vous pouvez à la place filtrer l’accès par le biais de l’étiquette du service SQL, dans le cadre des règles de réseau du Pare-feu Azure.
|Le trafic sortant sur le port TCP 25 n’est pas autorisé| Les connexions SMTP sortantes qui utilisent le port TCP 25 sont bloquées. Le port 25 sert essentiellement à la remise d’e-mails non authentifiés. Il s’agit du comportement par défaut de la plateforme pour les machines virtuelles. Pour plus d’informations consultez [Résoudre les problèmes de connectivité SMTP sortante dans Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Toutefois, contrairement aux machines virtuelles, il n’est actuellement pas possible d’activer cette fonctionnalité sur le Pare-feu Azure. Remarque : Pour autoriser un serveur SMTP authentifié (port 587) ou SMTP sur un port autre que 25, veillez à configurer une règle de réseau et non pas une règle d’application, car l’inspection SMTP n’est pas prise en charge pour l’instant.|Suivez la méthode d’envoi d’e-mails recommandée dans l’article traitant de la résolution des problèmes liés à SMTP. Vous pouvez aussi exclure la machine virtuelle qui a besoin d’un accès SMTP sortant entre votre route par défaut et le pare-feu. Configurez plutôt un accès sortant, directement vers Internet.
|Insuffisance de ports SNAT|Actuellement, le pare-feu Azure prend en charge 1024 ports par adresse IP publique par instance de groupe de machines virtuelles identiques back-end. Par défaut, il existe deux instances de groupe de machines virtuelles identiques.|Il s’agit de limites de l’équilibreur de charge standard, et nous cherchons constamment des opportunités de les repousser. En attendant, il est recommandé de configurer les déploiements de pare-feu Azure avec un minimum de cinq adresses IP publiques pour les déploiements sujets à une insuffisance de ports SNAT. Ceci multiplie par cinq le nombre de ports SNAT disponibles. Effectuez l’allocation à partir d’un préfixe d’adresse IP pour simplifier les autorisations en aval.|
|DNAT n’est pas pris en charge avec l’option Tunneling forcé activée|Les pare-feu déployés, dont l’option Tunneling forcé est activée, ne peuvent pas prendre en charge l’accès entrant depuis Internet compte tenu du routage asymétrique.|Ils ont ce comportement par défaut en raison du routage asymétrique. Le chemin de retour des connexions entrantes passe par le pare-feu local, qui n’a pas vu la connexion établie.
|Le FTP passif sortant est susceptible de ne pas fonctionner pour les pare-feu ayant plusieurs adresses IP publiques ; cela dépend de la configuration de votre serveur FTP.|Le mode FTP passif établit des connexions différentes pour les canaux de contrôle et ceux de données. Lorsqu’un pare-feu disposant de plusieurs adresses IP publiques envoie des données sortantes, il sélectionne de manière aléatoire une de ses adresses IP publiques comme adresse IP source. FTP peut échouer lorsque les canaux de données et de contrôle utilisent des adresses IP sources différentes ; cela dépend de la configuration de votre serveur FTP.|Une configuration SNAT explicite est prévue. En attendant, vous pouvez configurer votre serveur FTP pour accepter des canaux de données et de contrôle à partir d’adresses IP sources différentes (consultez [cet exemple pour IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). En guise d’alternative, utilisez une seule adresse IP dans ce cas de figure.|
|Le FTP passif entrant est susceptible de ne pas fonctionner ; cela dépend de la configuration de votre serveur FTP. |Le mode FTP passif établit des connexions différentes pour les canaux de contrôle et ceux de données. Les connexions entrantes sur le Pare-feu Azure font l’objet d’une traduction SNAT en une des adresses IP privées du pare-feu afin de garantir un routage symétrique. FTP peut échouer lorsque les canaux de données et de contrôle utilisent des adresses IP sources différentes ; cela dépend de la configuration de votre serveur FTP.|La conservation de l’adresse IP source d’origine est en cours d’examen. En attendant, vous pouvez configurer votre serveur FTP pour accepter des canaux de données et de contrôle à partir d’adresses IP sources différentes.|
|Il manque une dimension de protocole à la métrique NetworkRuleHit|La métrique ApplicationRuleHit autorise le protocole basé sur le filtrage, mais cette fonctionnalité est absente de la métrique NetworkRuleHit correspondante.|Un correctif est en cours d’étude.|
|Les règles NAT avec des ports entre 64000 et 65535 ne sont pas prises en charge|Le Pare-feu Azure autorise tous les ports de la plage 1-65535 dans les règles de réseau et d’application. Toutefois, les règles NAT prennent uniquement en charge les ports de la plage 1-63999.|Il s’agit d’une limitation actuelle.
|Les mises à jour de configuration peuvent prendre cinq minutes en moyenne|Une mise à jour de configuration du Pare-feu Azure peut prendre trois à cinq minutes en moyenne ; les mises à jour parallèles ne sont pas prises en charge.|Un correctif est en cours d’étude.|
|Le pare-feu Azure utilise des en-têtes SNI TLS pour filtrer le trafic HTTPS et MSSQL|Si le logiciel du navigateur ou du serveur ne prend pas en charge l’extension SNI, vous ne pouvez pas vous connecter via le Pare-feu Azure.|Si le logiciel du navigateur ou du serveur ne prend pas en charge SNI, il est possible que vous puissiez contrôler la connexion en utilisant une règle de réseau au lieu d’une règle d’application. Consultez [Indication du nom du serveur](https://wikipedia.org/wiki/Server_Name_Indication) (SNI) pour découvrir les logiciels qui prennent en charge SNI.|
|Le DNS personnalisé ne fonctionne pas avec le tunneling forcé|Si le tunneling forcé est activé, le DNS personnalisé ne fonctionne pas.|Un correctif est en cours d’étude.|
|Démarrer/Arrêter ne fonctionne pas avec un pare-feu configuré en mode tunnel forcé.|Démarrer/Arrêter ne fonctionne pas avec le pare-feu Azure configuré en mode tunnel forcé. Toute tentative de démarrage de Pare-feu Azure avec le tunneling forcé configuré provoque l’erreur suivante :<br><br>*Set-AzFirewall : impossible d’ajouter la configuration IP de gestion AzureFirewall FW-xx à un pare-feu existant. Redéployez avec une configuration IP de gestion si vous souhaitez utiliser la prise en charge du tunneling forcé.<br>StatusCode : 400<br>ReasonPhrase : Requête incorrecte*|En cours d’examen.<br><br>En guise de solution de contournement, vous pouvez supprimer le pare-feu existant et en créer un nouveau avec les mêmes paramètres.|
|Impossible d’ajouter des étiquettes de stratégie de pare-feu à l’aide du portail ou des modèles Azure Resource Manager (ARM)|La stratégie de Pare-feu Azure a une limite de prise en charge des correctifs qui vous empêche d’ajouter une étiquette à l’aide du portail Azure ou des modèles ARM. L’erreur suivante est générée : *Impossible d’enregistrer les étiquettes de la ressource*.|Un correctif est en cours d’étude. Vous pouvez aussi utiliser l’applet de commande Azure PowerShell `Set-AzFirewallPolicy` pour mettre à jour les balises.|
|IPv6 n’est pas pris en charge actuellement|Si vous ajoutez une adresse IPv6 à une règle, le pare-feu échoue.|Utilisez uniquement des adresses IPv4. La prise en charge d’IPv6 est en cours d’examen.|
|La mise à jour de plusieurs groupes d'adresses IP échoue en générant une erreur de conflit.|Quand vous mettez à jour au moins deux groupes IP joints au même pare-feu, l’une des ressources passe en état d’échec.|Il s'agit d'un problème/d'une limitation connu(e). <br><br>Quand vous mettez à jour un groupe IP, une mise à jour est déclenchée sur tous les pare-feux auxquels ce groupe IP est joint. Si la mise à jour d’un deuxième groupe IP est lancée alors que le pare-feu est toujours à l’état *Mise à jour*, la mise à jour du groupe IP échoue.<br><br>Pour éviter l’échec, les groupes IP joints au même pare-feu doivent être mis à jour un par un. Prévoyez suffisamment de temps entre les mises à jour pour permettre au pare-feu de sortir de l'état *Mise à jour*.|
|La suppression de RuleCollectionGroups à l’aide de modèles ARM n’est pas prise en charge.|La suppression d’un RuleCollectionGroup à l’aide de modèles ARM n’est pas prise en charge et entraîne un échec.|Cette opération n’est pas prise en charge.|
|La règle DNAT permettant de *tout* (*) autoriser renvoie le trafic SNAT.|Si une règle DNAT autorise *tout* (*) en tant qu’adresse IP source, une règle de réseau implicite met en correspondance le traffic VNet-VNet et soumet systématiquement le trafic à cette règle SNAT.|Il s’agit d’une limitation actuelle.|
|L’ajout d’une règle DNAT à un hub virtuel sécurisé à l’aide d’un fournisseur de sécurité n’est pas pris en charge.|Cela se traduit par un itinéraire asynchrone pour le trafic DNAT de retour, qui accède au fournisseur de sécurité.|Non pris en charge.|


## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Créer un pare-feu Azure et une stratégie de pare-feu - Modèle ARM](../firewall-manager/quick-firewall-policy.md)
- [Démarrage rapide : Déployer un pare-feu Azure avec Zones de disponibilité - Modèle ARM](deploy-template.md)
- [Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md)
