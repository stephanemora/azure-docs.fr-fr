---
title: Déboguer des applications de Proxy d’Application - Azure Active Directory | Microsoft Docs
description: Déboguer les problèmes liés aux applications de Proxy d’Application Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172264"
---
# <a name="debug-application-proxy-application-issues"></a>Déboguer les problèmes d’application de Proxy d’Application 

Cet article vous permet de résoudre les problèmes liés aux applications de Proxy d’Application Azure Active Directory (Azure AD). Si vous utilisez le service de Proxy d’Application pour l’accès à distance à une application de web en local, mais vous ne parvenez pas à se connecter à l’application, utilisez cet organigramme pour déboguer les problèmes de l’application. 

## <a name="before-you-begin"></a>Avant de commencer

Lors de la résolution des problèmes de Proxy d’Application, nous vous recommandons de que commencer avec les connecteurs. Tout d’abord, suivez le flux de résolution des problèmes dans [déboguer Application Proxy Connector émet les](application-proxy-debug-connectors.md) s’assurer que les connecteurs de Proxy d’Application sont correctement configurés. Si vous rencontrez toujours des problèmes, revenez à cet article pour résoudre les problèmes de l’application.  

Pour plus d’informations sur le Proxy d’Application, consultez :

- [Accès à distance aux applications locales via le Proxy d’Application](application-proxy.md)
- [Connecteurs de Proxy d’application](application-proxy-connectors.md)
- [Installer et inscrire un connecteur](application-proxy-add-on-premises-application.md)
- [Résoudre les problèmes de proxy d’application et les messages d’erreur](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Organigramme pour connaître les problèmes

Cet organigramme vous guide à travers les étapes pour le débogage de certains des problèmes plus courants avec la connexion à l’application. Pour plus d’informations sur chaque étape, consultez le tableau suivant l’organigramme.

![Organigramme illustrant les étapes pour le débogage d’une application](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Action | Description  | 
|---------|---------|---------|
|1 | Ouvrez un navigateur, accéder à l’application et entrez vos informations d’identification | Essayez d’utiliser vos informations d’identification pour vous connecter à l’application, puis vérifiez les éventuelles erreurs liées aux utilisateurs, par exemple [cette application d’entreprise n’est pas accessible](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Vérifier l’affectation de l’utilisateur à l’application | Assurez-vous que votre compte d’utilisateur a l’autorisation d’accéder à l’application à partir du réseau d’entreprise et ensuite tester la connexion à l’application en suivant les étapes décrites dans [tester l’application](application-proxy-add-on-premises-application.md#test-the-application). Si les problèmes de connexion persistent, consultez [comment résoudre les erreurs de connexion](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors).  |
|3 | Ouvrez un navigateur et essayez d’accéder à l’application | Si une erreur s’affiche immédiatement, vérifiez que le Proxy d’Application est correctement configuré. Pour plus d’informations sur les messages d’erreur spécifiques, consultez [les problèmes de Proxy d’Application résoudre les problèmes et messages d’erreur](application-proxy-troubleshoot.md).  |
|4 | Vérifiez votre configuration de domaine personnalisé ou de résoudre l’erreur | Si la page ne s’affiche pas du tout, vérifiez que votre domaine personnalisé est configuré correctement en examinant [utilisation des domaines personnalisés](application-proxy-configure-custom-domain.md).<br></br>Si la page ne se charge et s’affiche un message d’erreur, corrigez l’erreur en vous reportant aux [les problèmes de Proxy d’Application résoudre les problèmes et messages d’erreur](application-proxy-troubleshoot.md). <br></br>S’il faut plu de 20 secondes pour un message d’erreur apparaisse, il existe peut-être un problème de connectivité. Accédez à la [connecteurs de Proxy d’Application déboguer](application-proxy-debug-connectors.md) article sur le dépannage.  |
|5. | Si les problèmes persistent, accédez à débogage du connecteur | Il existe peut-être un problème de connectivité entre le proxy et le connecteur ou entre le connecteur et le serveur principal. Accédez à la [connecteurs de Proxy d’Application déboguer](application-proxy-debug-connectors.md) article sur le dépannage. |
|6. | Publier toutes les ressources, vérifiez les outils de développement de navigateur et résoudre les liens | Assurez-vous que le chemin de publication inclut des images nécessaires, des scripts et des feuilles de style pour votre application. Pour plus d’informations, consultez [ajouter une application en local à Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Utiliser des outils de développement (outils F12 dans Internet Explorer ou Microsoft Edge) et la vérification du navigateur pour la publication des problèmes comme décrit dans [page de l’Application ne s’affiche pas correctement](application-proxy-page-appearance-broken-problem.md). <br></br>Passez en revue les options pour la résolution des liens rompus dans [liens sur la page ne fonctionnent pas](application-proxy-page-links-broken-problem.md). |
|7 | Recherchez la latence du réseau | Si la page se charge lentement, découvrez comment réduire la latence du réseau dans [considérations relatives à la réduction de la latence](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Consultez l’aide de dépannage supplémentaire | Si les problèmes persistent, rechercher d’autres articles de résolution des problèmes dans le [Proxy d’Application de résolution des problèmes de documentation](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>Étapes suivantes


* [Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs](application-proxy-connector-groups.md)
* [Travailler avec des serveurs proxy locaux existants](application-proxy-configure-connectors-with-proxy-servers.md)
* [Résoudre les erreurs du proxy d’application et du connecteur](application-proxy-troubleshoot.md)
* [Comment installer silencieusement le connecteur du Proxy d'application Azure AD](application-proxy-register-connector-powershell.md)
