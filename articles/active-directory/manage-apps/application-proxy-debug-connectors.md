---
title: Déboguer des connecteurs de Proxy d’Application - Azure Active Directory | Microsoft Docs
description: Déboguer les problèmes avec les connecteurs de Proxy d’Application Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172234"
---
# <a name="debug-application-proxy-connector-issues"></a>Déboguer les problèmes de connecteur de Proxy d’Application 

Cet article vous aide à résoudre les problèmes avec les connecteurs de Proxy d’Application Azure Active Directory (Azure AD). Si vous utilisez le service de Proxy d’Application pour l’accès à distance à une application de web en local, mais vous ne parvenez pas à se connecter à l’application, utilisez cet organigramme pour déboguer les problèmes du connecteur. 

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez installé le connecteur de Proxy d’Application et que vous rencontrez un problème. Lors de la résolution des problèmes de Proxy d’Application, nous vous recommandons de que commencer avec ce flux de résolution des problèmes pour déterminer si les connecteurs de Proxy d’Application sont configurés correctement. Si vous rencontrez toujours des problèmes de connexion à l’application, suivez le flux de résolution des problèmes dans [problèmes de Proxy d’Application déboguer des applications](application-proxy-debug-apps.md).  


Pour plus d’informations sur le Proxy d’Application et à l’aide de ses connecteurs, consultez :

- [Accès à distance aux applications locales via le Proxy d’Application](application-proxy.md)
- [Connecteurs de Proxy d’application](application-proxy-connectors.md)
- [Installer et inscrire un connecteur](application-proxy-add-on-premises-application.md)
- [Résoudre les problèmes de proxy d’application et les messages d’erreur](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Diagramme de flux pour les problèmes de connecteur

Cet organigramme vous guide à travers les étapes pour le débogage des problèmes de connecteur plus courants. Pour plus d’informations sur chaque étape, consultez le tableau suivant l’organigramme.

![Organigramme illustrant les étapes pour le débogage d’un connecteur](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Action | Description  | 
|---------|---------|---------|
|1 | Recherchez le groupe de connecteurs attribué à l’application | Vous avez probablement un connecteur est installé sur plusieurs serveurs, auquel cas les connecteurs doivent être [affectés à des groupes de connecteurs](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Pour en savoir plus sur les groupes de connecteurs, consultez [Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs](application-proxy-connector-groups.md). |
|2 | Installer le connecteur et affecter un groupe | Si vous n’avez pas un connecteur est installé, consultez [installer et inscrire un connecteur](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br>Si le connecteur n’est pas affecté à un groupe, consultez [affecter le connecteur à un groupe](application-proxy-connector-groups.md#create-connector-groups).<br></br>Si l’application n’est pas affectée à un groupe de connecteurs, consultez [affecter l’application à un groupe de connecteurs](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Exécuter un test de port sur le serveur de connecteur | Sur le serveur de connecteur, exécutez un test de port à l’aide de [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou autres tests de port de l’outil pour vérifier si les ports 443 et 80 sont ouverts.|
|4 | Configurer les domaines et ports | [Assurez-vous que vos domaines et les ports sont configurés correctement](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) pour le connecteur fonctionne correctement, il existe certains ports qui doivent être ouverts et les URL de votre serveur doit être en mesure d’accéder. |
|5. | Vérifiez si un proxy de serveur principal est en cours d’utilisation | Vérifier si les connecteurs sont à l’aide de serveurs proxy de serveur principal ou en ignorant les. Pour plus d’informations, consultez [résoudre les problèmes de proxy de connecteur et problèmes de connectivité de service](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6. | Mettre à jour le connecteur et la mise à jour pour utiliser le proxy principal | Si un proxy de serveur principal est en cours d’utilisation, vous souhaitez vous assurer que le connecteur utilise le même proxy. Pour plus d’informations sur le dépannage et la configuration des connecteurs pour travailler avec des serveurs proxy, consultez [travailler avec existant locaux serveurs proxy](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Charger l’URL interne de l’application sur le serveur de connecteur | Sur le serveur de connecteur, chargez URL interne de l’application. |
|8 | Vérifiez la connectivité de réseau interne | Il existe un problème de connectivité de votre réseau interne que ce flux de débogage ne peut pas diagnostiquer. L’application doit être accessible en interne pour les connecteurs fonctionner. Vous pouvez activer et afficher les journaux des événements de connecteur comme décrit dans [connecteurs de Proxy d’Application](application-proxy-connectors.md#under-the-hood). |
|9 | Augmenter la valeur de délai d’attente sur le serveur principal | Dans le **des paramètres supplémentaires** pour votre application, vous devez modifier le **expiration de l’Application back-end** à **Long**. Consultez [ajouter une application en local à Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Si les problèmes persistent, cibler les problèmes de flux spécifique, révision application et l’authentification unique de débogage de flux | Utilisez le [problèmes de Proxy d’Application déboguer des applications](application-proxy-debug-apps.md) dépannage du flux. |

## <a name="next-steps"></a>Étapes suivantes


* [Publier des applications sur des réseaux et emplacements distincts à l’aide de groupes de connecteurs](application-proxy-connector-groups.md)
* [Travailler avec des serveurs proxy locaux existants](application-proxy-configure-connectors-with-proxy-servers.md)
* [Résoudre les erreurs du proxy d’application et du connecteur](application-proxy-troubleshoot.md)
* [Comment installer silencieusement le connecteur du Proxy d'application Azure AD](application-proxy-register-connector-powershell.md)
