---
title: FAQ - Azure Monitor pour SAP Solutions | Microsoft Docs
description: Dans cet article, découvrez les réponses aux questions fréquentes sur Azure Monitor pour SAP Solutions.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/08/2020
ms.author: radeltch
ms.openlocfilehash: dfc28d9ced0ca65b4ebe0e49ade05eb822c59bab
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567629"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Questions fréquentes (FAQ) sur Azure Monitor pour SAP Solutions (préversion)
## <a name="frequently-asked-questions"></a>Forum aux questions

Cet article offre des réponses aux questions fréquentes sur Azure Monitor pour SAP Solutions.  

 - **Dois-je payer pour Azure Monitor pour solutions SAP ?**  
Il n’y a pas de frais de licence pour Azure Monitor pour SAP Solutions. Toutefois, le coût des composants du groupe de ressources managé est à votre charge.  

 - **Dans quelles régions ce service est-il disponible pour la préversion publique ?**  
Pour la préversion publique, ce service sera disponible dans les régions USA Est 2, USA Ouest 2, USA Est et Europe Ouest.  

 - **Dois-je donner des autorisations pour permettre le déploiement d’un groupe de ressources managé dans mon abonnement ?**  
Non, des autorisations explicites ne sont pas requises.  

 - **Où se trouve la machine virtuelle du collecteur ?**  
Au moment de déployer Azure Monitor pour SAP Solutions, nous vous recommandons de choisir le même réseau virtuel pour votre ressource de supervision que votre serveur SAP HANA. Nous recommandons donc que la machine virtuelle du collecteur se trouve dans le même réseau virtuel que le serveur SAP HANA. Si vous utilisez une base de données non-HANA, la machine virtuelle du collecteur se trouve dans le même réseau virtuel que la base de données non-HANA.  

 - **Quelles sont les versions de HANA prises en charge ?**  
HANA 1.0 SPS 12 (Rev. 120 ou ultérieure) et HANA 2.0 SPS03 ou ultérieur sont pris en charge. 

 - **Quelles sont les configurations de déploiement HANA prises en charge ?**  
Les configurations suivantes sont prises en charge :
   - Mononœud (scale-up) et multinœud (scale-out).  
   - Un conteneur de bases de données uniques (HANA 1.0 SPS 12) et des conteneurs de bases de données multiples (HANA 1.0 SPS 12 ou HANA 2.0).
   - Basculement d’hôte automatique (n+1) et HSR.  

 - **Quelles versions de SQL Server sont prises en charge ?**  
SQL Server 2012 SP4 ou ultérieur.  

 - **Quelles sont les configurations de SQL Server prises en charge ?**  
Les configurations suivantes sont prises en charge :
   - Les instances par défaut ou autonomes nommées dans une machine virtuelle.  
   - Les instances en cluster ou dans une configuration AlwaysOn lors de l’utilisation du nom virtuel de la ressource de cluster ou du nom d’écouteur AlwaysOn.
   - Actuellement, aucune métrique propre au cluster ou à la configuration AlwaysOn n’est collectée.    
   - Actuellement, Azure SQL Database (PaaS) n’est pas pris en charge.  

 - **Que se passe-t-il si je supprime accidentellement le groupe de ressources managé ?**  
Le groupe de ressources managé est verrouillé par défaut. Par conséquent, les risques de supprimer accidentellement le groupe de ressources managé sont minuscules. Si vous supprimez le groupe de ressources managé, Azure Monitor pour SAP Solutions cessera de fonctionner. Vous devrez déployer une nouvelle ressource Azure Monitor pour SAP Solutions et recommencer.  

 - **De quels rôles ai-je besoin dans mon abonnement Azure pour déployer des ressources Azure Monitor pour solutions SAP ?**  
Le rôle Contributeur.  

 - **Quel est le contrat SLA sur ce produit ?**  
Les préversions sont exclues des contrats de niveau de service. Lisez entièrement le contrat de licence via l’image Azure Monitor pour SAP Solutions de la Place de marché.  

 - **Puis-je surveiller l’intégralité de son paysage avec cette solution ?**  
En préversion publique, vous pouvez actuellement surveiller :
- Une base de données HANA
- L’infrastructure sous-jacente
- Le cluster haute disponibilité
- Microsoft SQL Server
- La disponibilité de SAP NetWeaver
- Les métriques de disponibilité des instances d’application SAP

 - **Ce service remplace-t-il le gestionnaire de solutions SAP ?**  
Non. Les clients peuvent toujours utiliser le gestionnaire de solutions SAP pour l’analyse des processus d’entreprise.  

 - **Quelle est la valeur de ce service par rapport aux solutions traditionnelles comme SAP HANA Cockpit/Studio ?**  
Azure Monitor pour SAP Solutions n’est pas spécifique à la base de données HANA. Azure Monitor pour SAP Solutions prend également en charge AnyDB.  

- **Quelles sont les versions de SAP NetWeaver prises en charge ?**  
SAP NetWeaver 7.0 ou ultérieur.  

- **Quelles sont les configurations SAP NetWeaver prises en charge ?**  
Les configurations de serveur d’applications SAP NetWeaver ABAP, Java et double pile sont prises en charge.

- **Pourquoi est-ce que je dois ôter la protection des méthodes pour la supervision des applications SAP NetWeaver ?**  
Dans les versions de SAP >= à 7.3, la plupart des méthodes de service web sont protégées par défaut. Pour extraire des métriques de disponibilité et de performances en appelant ces méthodes, vous devez ôter la protection des méthodes suivantes : GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic et GetSystemInstancelist.

- **La suppression de la protection des méthodes web SAPCONTROL présente-t-elle un risque ?**  
En général, vous pouvez ôter la protection des méthodes web SAPCONTROL sans risque de sécurité [en tant que tel](https://launchpad.support.sap.com/#/notes/1439348). Vous pouvez restreindre l’accès aux méthodes web non protégées via les ports serveur 5XX13 / 5XX14 de sapstartsrv. Pour ce faire, ajoutez un filtre dans la liste de contrôle d’accès SAP. La [note OSS](https://service.sap.com/sap/support/notes/1495075) décrit la configuration requise. 

- **Est-ce que je dois redémarrer mes instances SAP après avoir effectué des configurations système pour configurer le fournisseur SAP NetWeaver ?**  
Oui, une fois que vous avez ôté la protection des méthodes par le biais de modifications de configuration SAP, vous devez redémarrer les systèmes SAP concernés afin de vous assurer que les modifications de configuration sont mises à jour.  

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail Azure Monitor pour SAP Solutions.

> [!div class="nextstepaction"]
> [Azure Monitor pour SAP Solutions](azure-monitor-overview.md)
