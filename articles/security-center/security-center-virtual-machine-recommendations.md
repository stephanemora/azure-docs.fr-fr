---
title: Recommandations concernant les machines virtuelles dans Azure Security Center | Microsoft Docs
description: Ce document traite des recommandations d’Azure Security Center qui peuvent vous aider à protéger vos machines virtuelles, vos ordinateurs, vos applications web et vos environnements App Service.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: rkarlin
ms.openlocfilehash: dd7858b9723d8d3ee9eb9299b2cee11596563a33
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340701"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Comprendre les recommandations sur les ressources d’Azure Security Center


## <a name="recommendations"></a>Recommandations
Utilisez le tableau ci-dessous pour mieux comprendre les recommandations Compute et App Services disponibles, et leurs effets.

### <a name="computers"></a>Ordinateurs
| Recommandation | Description |
| --- | --- |
| [Activer la collecte des données pour des abonnements](security-center-enable-data-collection.md) |Recommande l’activation de la collecte des données dans la stratégie de sécurité pour chacun de vos abonnements et toutes les machines virtuelles de vos abonnements. |
| [Activer le chiffrement pour le compte de stockage Azure](security-center-enable-encryption-for-storage-account.md) | Recommande d’activer Azure Storage Service Encryption pour les données au repos. SSE (Storage Service Encryption) chiffre les données lorsqu’elles sont écrites dans le stockage Azure et les déchiffre avant récupération. SSE est actuellement disponible uniquement pour le service BLOB Azure et peut être utilisé pour les objets blob de blocs, les objets blob de pages les objets blob Append. Pour en savoir plus, consultez [Azure Storage Service Encryption pour les données au repos](../storage/common/storage-service-encryption.md).</br>SSE est uniquement pris en charge sur les comptes de stockage Resource Manager. Les comptes de stockage Classic ne sont actuellement pas pris en charge. Pour comprendre les modèles de déploiement de type classique et Resource Manager, consultez [Modèles de déploiement Azure](../azure-classic-rm.md). |
| [Corriger les configurations de sécurité](security-center-remediate-os-vulnerabilities.md) |Recommande d’aligner les configurations de votre système d’exploitation sur les règles de configuration de sécurité recommandées, comme le fait de ne pas permettre l’enregistrement des mots de passe. |
| [Appliquer des mises à jour système](security-center-apply-system-updates.md) |Recommande le déploiement des mises à jour de sécurité du système et des mises à jour critiques manquantes sur les machines virtuelles. |
| [Appliquer un contrôle d’accès réseau Juste à temps](security-center-just-in-time.md) | Recommande d’appliquer un accès Juste à temps à la machine virtuelle. La fonctionnalité Juste à temps est disponible en préversion pour le niveau Standard de Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center. |
| [Redémarrage après des mises à jour système](security-center-apply-system-updates.md#reboot-after-system-updates) |Recommande de redémarrer une machine virtuelle pour terminer le processus de mise à jour du système. |
| [Installer Endpoint Protection](security-center-install-endpoint-protection.md) |Recommande l’approvisionnement de logiciels anti-programme malveillant sur les machines virtuelles (Windows uniquement). |
| [Activer l’agent de machine virtuelle](security-center-enable-vm-agent.md) |Vous permet de connaître les machines virtuelles qui nécessitent l’agent de machine virtuelle. L’agent de machine virtuelle doit être installé sur les machines virtuelles pour approvisionner l’analyse des correctifs, l’analyse des lignes de base et les logiciels anti-programme malveillant. L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. L’article [Installer l’agent de machine virtuelle – Deuxième partie](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’agent de machine virtuelle. |
| [Apply disk encryption (Appliquer le chiffrement de disque Azure Disk Encryption)](security-center-apply-disk-encryption.md) |Recommande le chiffrement des disques des machines virtuelles à l’aide d’Azure Disk Encryption (Windows et Linux). Le chiffrement est recommandé pour les systèmes d’exploitation et les volumes de données de votre machine virtuelle. |
| [Mettre à jour la version du système d’exploitation](security-center-update-os-version.md) |Recommande de mettre à jour la version du système d’exploitation de votre service Cloud vers la version la plus récente disponible pour votre famille de systèmes d’exploitation.  Pour en savoir plus sur Cloud Services, consultez [Vue d’ensemble de Cloud Services](../cloud-services/cloud-services-choose-me.md). |
| [Évaluation des vulnérabilités non installée](security-center-vulnerability-assessment-recommendations.md) |Recommande d’installer une solution d’évaluation des vulnérabilités sur votre machine virtuelle. |
| [Corriger des vulnérabilités](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Vous permet de voir les vulnérabilités du système et des applications détectées par la solution d’évaluation des vulnérabilités installée sur votre machine virtuelle. |

### <a name="app-services"></a>App Services
| Recommandation | Description |
| --- | --- |
| App Service ne doit être accessible que via HTTPS | Recommande de limiter l’accès d’App Service via HTTPS uniquement. |
| Web Sockets doit être désactivé pour l’application web| Recommande d’examiner soigneusement l’utilisation de Web Sockets à l’intérieur d’applications web.  Le protocole Web Sockets est vulnérable à différents types de menaces de sécurité. |
| Utiliser des domaines personnalisés pour votre application web | Recommande l’utilisation de domaines personnalisés pour protéger une application web contre des attaques courantes telles que le hameçonnage et d’autres attaques liées au DNS. |
| Configurer des restrictions d’adresse IP pour l’application web | Recommande de définir une liste d’adresses IP autorisées à accéder à votre application.  L’utilisation de restrictions d’adresse IP protège une application web contre des attaques courantes. |
| Ne pas autoriser toutes (’*’) les ressources à accéder à votre application | Recommande de ne pas définir le paramètre WEBSITE_LOAD_CERTIFICATES sur «  *». La définition du paramètre sur «*  » signifie que tous les certificats seront chargés dans votre magasin de certificats personnels d’applications web.  Cela peut conduire à un abus du principe des privilèges minimum, car il est peu probable que le site ait besoin d’accéder à tous les certificats lors de l’exécution. |
| CORS ne devrait pas autoriser toutes les ressources à accéder à votre application | Recommande d’autoriser uniquement les domaines requis à interagir avec votre application web. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application web. |
| Utiliser la dernière version de .NET Framework prise en charge pour l’application web | Recommande d’utiliser la dernière version de .NET Framework pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable. |
| Utiliser la dernière version de Java prise en charge pour l’application web | Recommande d’utiliser la dernière version de Java pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable. |
| Utiliser la dernière version de PHP prise en charge pour l’application web | Recommande d’utiliser la dernière version de PHP pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable. |
| [Add a web application firewall](security-center-add-web-application-firewall.md) |Recommande le déploiement d’un pare-feu d’applications web (WAF) pour les points de terminaison web. Une recommandation WAF est indiquée pour n’importe quelle IP publique (adresse IP de niveau d’instance ou adresse IP à équilibrage de charge) ayant un groupe de sécurité réseau associé avec des ports web entrants ouverts (80, 443).</br></br>Security Center recommande d’approvisionner un WAF pour vous défendre contre les attaques ciblant vos applications web sur les machines virtuelles et sur l’environnement App Service (ASE). Un environnement App Service (ASE) est une option de plan de service [Premium](https://azure.microsoft.com/pricing/details/app-service/) d'Azure App Service qui fournit un environnement totalement isolé et dédié pour l'exécution sécurisée de vos applications Azure App Service. Pour en savoir plus sur ASE, voir [Documentation sur l’environnement App Service](../app-service/environment/intro.md).</br></br>Vous pouvez protéger plusieurs applications web dans le centre de sécurité en les ajoutant à vos déploiements WAF existants. |
| [Finaliser la protection des applications](security-center-add-web-application-firewall.md#finalize-application-protection) |Pour terminer la configuration d’un pare-feu d’applications web, le trafic doit être redirigé vers l’appliance de pare-feu d’applications web. L’application de cette recommandation permet d’apporter les modifications nécessaires à la configuration. |
| Utiliser la dernière version de Node.js prise en charge pour l’application web | Recommande d’utiliser la dernière version de Node.js pour les classes de sécurité les plus récentes. L’utilisation de classes et types plus anciens peut rendre votre application vulnérable. |
| CORS ne devrait pas autoriser toutes les ressources à accéder à votre Function App | Recommande d’autoriser uniquement les domaines requis à interagir avec votre application web. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application de fonction. |
| Utiliser des domaines personnalisés pour Function App | Recommande l’utilisation de domaines personnalisés pour protéger une application de fonction contre des attaques courantes telles que le hameçonnage et d’autres attaques liées au DNS. |
| Configurer les restrictions IP pour Function App | Recommande de définir une liste d’adresses IP autorisées à accéder à votre application. L’utilisation de restrictions d’adresse IP protège une application de fonction contre des attaques courantes. |
| Function App ne doit pas être accessible via HTTPS | Recommande de limiter l’accès d’applications de fonction via HTTPS uniquement. |
| Le débogage à distance devrait être désactivé pour Function App | Recommande de désactiver le débogage pour Function App si vous n’avez plus besoin de l’utiliser. Le débogage distant requiert que des ports d’entrée soient ouverts sur une Function App. |
| Web Sockets devrait être désactivé pour Function App | Recommande d’examiner soigneusement l’utilisation de Web Sockets à l’intérieur d’applications de fonction. Le protocole Web Sockets est vulnérable à différents types de menaces de sécurité. |


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
