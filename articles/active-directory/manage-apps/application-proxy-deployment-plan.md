---
title: Planifier un déploiement de Proxy d’Application Azure Active Directory
description: Un guide de bout en bout pour la planification du déploiement du proxy d’Application au sein de votre organisation
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04-04-2019
ms.author: barbaraselden
ms.reviewer: ''
ms.openlocfilehash: d8686b9296c8b1d7c5232e2e46a0e66a9896656b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113023"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planifier un déploiement de Proxy d’Application Azure AD

Proxy d’Application Azure Active Directory (Azure AD) est une solution d’accès à distance sécurisé et rentable pour les applications locales. Il fournit un chemin d’accès de la transition immédiate pour les organisations de « Cloud première » gérer l’accès à hérité applications locales qui ne sont pas encore capable d’utiliser des protocoles modernes. Pour obtenir des informations supplémentaires, consultez [quel est le Proxy d’Application](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Le Proxy d’application est recommandé pour donner aux utilisateurs distants d’accès aux ressources internes. Le Proxy d’application remplace le besoin d’un VPN ou proxy inverse pour ces cas d’utilisation de l’accès à distance. Il n’est pas destiné aux utilisateurs qui se trouvent sur le réseau d’entreprise. Ces utilisateurs qui utilisent le Proxy d’Application pour l’accès intranet peuvent rencontrer des problèmes de performances indésirable.

Cet article inclut les ressources que vous avez besoin pour planifier, de fonctionner et de gérer le Proxy d’Application Azure AD. 

## <a name="plan-your-implementation"></a>Planifier l’implémentation

La section suivante fournit une vue générale de la planification des éléments qui effectueront une configuration pour une expérience de déploiement efficace de clé. 

### <a name="prerequisites"></a>Conditions préalables

Vous devez respecter les conditions préalables suivantes avant de commencer votre implémentation. Vous pouvez voir plus d’informations sur la configuration de votre environnement, y compris de ces conditions préalables, dans ce [didacticiel](application-proxy-add-on-premises-application.md).

* **Connecteurs**: Les connecteurs sont des agents légers que vous pouvez déployer sur :
   * Matériel physique en local
   * Une machine virtuelle hébergée dans n’importe quelle solution de l’hyperviseur
   * Une machine virtuelle hébergée dans Azure pour activer la connexion sortante vers le service de Proxy d’Application.

* Consultez [connecteurs de comprendre le Proxy d’application Azure AD](application-proxy-connectors.md) pour une présentation plus détaillée.

     * Connecteur machines doit [être activé pour TLS 1.2](application-proxy-add-on-premises-application.md) avant d’installer les connecteurs.

     * Si possible, déployer des connecteurs dans le [même réseau](application-proxy-network-topology.md) et segment que les serveurs d’applications web back-end. Il est préférable de déployer des connecteurs après avoir effectué une détection des applications.
     * Nous recommandons que chaque groupe de connecteurs ait au moins deux connecteurs pour permettre la mise à l’échelle et haute disponibilité. Avoir trois connecteurs est optimal au cas où vous devrez peut-être un ordinateur à n’importe quel point de service. Examinez le [table de capacité de connecteur](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) pour aider à décider quel type de machine pour installer des connecteurs sur. Plus l’ordinateur de la mémoire tampon plus et performante, le connecteur sera.

* **Paramètres d’accès réseau**: Les connecteurs de Proxy d’Application AD Azure [se connecter à Azure via HTTPS (Port TCP 443) et HTTP (Port TCP 80)](application-proxy-add-on-premises-application.md). 

   * Connecteur de terminaison du trafic TLS n’est pas pris en charge et empêchera les connecteurs à partir de l’établissement d’un canal sécurisé avec leurs points de terminaison Proxy d’application Azure respectifs.

   * Évitez de toutes les formes d’inspection inline sur les communications TLS sortantes entre les connecteurs et Azure. Inspection interne entre un connecteur et le serveur principal des applications est possible, mais pourrait en altérer l’expérience utilisateur et par conséquent, n’est pas recommandée.

   * L’équilibrage de charge des connecteurs eux-mêmes est également pas pris en charge, ou même nécessaires.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considérations importantes avant de configurer le Proxy d’Application Azure AD

Exigences principales suivantes doivent être remplies afin de configurer et implémenter le Proxy d’Application Azure AD.

*  **L’intégration d’Azure**: Avant de déployer le proxy d’application, les identités des utilisateurs doivent être synchronisées à partir d’un répertoire local ou créées directement dans vos locataires Azure AD. Synchronisation des identités permet à Azure AD pour la pré-authentification des utilisateurs avant de leur accorder l’accès au Proxy d’application des applications publiées et disposer des informations d’identificateur utilisateur nécessaires pour effectuer l’authentification unique (SSO).

* **Exigences d’accès conditionnel**: Nous vous déconseillons d’à l’aide de Proxy d’Application pour l’accès intranet, car cela ajoute une latence qui aura un impact sur les utilisateurs. Nous recommandons d’utiliser le Proxy d’Application avec les stratégies d’accès conditionnel et la pré-authentification pour l’accès à distance à partir d’internet.  Une approche pour fournir l’accès conditionnel pour une utilisation de l’intranet consiste à moderniser des applications afin qu’ils peuvent diretly s’authentifier auprès d’AAD. Reportez-vous à [ressources sur la migration d’applications vers AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) pour plus d’informations. 

* **Limites de service**: Pour vous protéger contre une consommation excessive des ressources par les locataires individuels il sont les limites de limitation défini par l’application et le client. Pour voir ces limites référencent [restrictions et limites de service Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Ces limites sont basées sur un banc d’essai présent au-dessus de volume d’utilisation classiques et fournit la mémoire tampon suffisante pour la plupart des déploiements.

* **Certificat public**: Si vous utilisez des noms de domaine personnalisé, vous devez procurez-vous un certificat public délivré par une autorité de certification de confiance non-Microsoft. En fonction des exigences de votre organisation, l’obtention d’un certificat peut prendre un certain temps et nous vous recommandons de commencer le processus aussi tôt que possible. Proxy d’Application Azure prend en charge la norme, [génériques](application-proxy-wildcard.md), ou les certificats basés sur le SAN.

* **Configuration requise du domaine**: L’authentification unique à vos applications publiées à l’aide de la délégation Kerberos (KCD), un hôte de connecteur doit être joint à un domaine au même domaine Active Directory que les applications en cours de publication. Pour plus d’informations sur le sujet, consultez [KCD pour l’authentification unique sur](application-proxy-configure-single-sign-on-with-kcd.md) avec le Proxy d’Application. Le service du connecteur s’exécute dans le contexte du système local et ne doit pas être configuré pour utiliser une identité personnalisée.

* **Enregistrements DNS pour les URL**

   * Avant d’utiliser des domaines personnalisés dans le Proxy d’Application, vous devez créer un enregistrement CNAME dans DNS public, ce qui permet aux clients de résoudre l’URL externe définie personnalisée à l’adresse de Proxy d’Application prédéfini. Échec de création d’un enregistrement CNAME pour une application qui utilise un domaine personnalisé empêchera les utilisateurs distants de se connecter à l’application. Étapes requises pour ajouter des enregistrements CNAME peuvent varier de DNS pour un fournisseur, donc apprendre comment [gérer des jeux d’enregistrements et des enregistrements DNS à l’aide du portail Azure](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * De même, les hôtes de connecteur doivent être en mesure de résoudre l’URL interne des applications en cours de publication.

* **Rôles et droits d’administration**

   * **Installation du connecteur** requiert des droits d’administrateur local sur le serveur Windows qui est en cours d’installation sur. Elle nécessite également un minimum d’un *administrateur d’Application* rôle pour authentifier et inscrire l’instance connector à votre locataire Azure AD. 

   * **Publication d’application et l’administration** nécessitent le *administrateur d’Application* rôle. Administrateurs de l’application peuvent gérer toutes les applications dans le répertoire, y compris les inscriptions, paramètres d’authentification unique, utilisateur et affectations de groupe et gestion des licences, les paramètres de Proxy d’Application et consentement. Elle ne donne pas la possibilité de gérer l’accès conditionnel. Le *administrateur d’Application Cloud* rôle dispose de toutes les capacités de l’administrateur d’Application, sauf qu’il n’autorise pas la gestion des paramètres de Proxy d’Application.

* **Licences**. Le Proxy d’application est disponible via l’abonnement Azure AD Basic. Reportez-vous à la [page Tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) pour une liste complète des options et fonctionnalités de gestion de licences.  

### <a name="application-discovery"></a>Détection des applications

Compiler un inventaire de toutes les applications dans la portée qui sont publiées via le Proxy d’Application en collectant les informations suivantes :

| Type d’informations| Informations à collecter |
|---|---|
| Type de service| Exemple : SharePoint, SAP, CRM, Application Web personnalisée, API |
| Plateforme d’application | Exemple : Windows IIS, Apache sur Linux, Tomcat, NGINX |
| Appartenance au domaine| Nom de domaine complet du serveur Web (FQDN) |
| Emplacement de l’application | Emplacement du serveur web ou une batterie de serveurs dans votre infrastructure |
| Accès interne | L’URL exacte utilisée lors de l’accès de l’application en interne. <br> Si une batterie de serveurs, quel type d’équilibrage de charge est en cours d’utilisation ? <br> Indique si l’application dessine le contenu à partir de sources autres que lui-même.<br> Déterminer si l’application fonctionne sur WebSockets. |
| Accès externe | La solution de fournisseur que l’application est déjà exposée à l’extérieur. <br> L’URL que vous souhaitez utiliser pour l’accès externe. Si SharePoint, vérifiez les mappages des accès de substitution sont configurés par [ce guide](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Si ce n’est pas le cas, vous devez définir les URL externes. |
| Certificat public | Si vous utilisez un domaine personnalisé, procurez-vous un certificat avec un nom d’objet correspondant. Si un certificat existe, notez le numéro de série et l’emplacement où il peut être obtenu. |
| Type d'authentification| Le type d’authentification pris en charge par la prise en charge de l’application telles que Basic, Windows l’authentification intégrée, basée sur les formulaires, basée sur l’en-tête et revendications. <br>Si l’application est configurée pour s’exécuter sous un compte de domaine spécifique, notez le nom de domaine complet (FQDN) du compte de service.<br> Si elle est SAML, les URL d’identificateur et de réponse. <br> Si basé sur l’en-tête, la solution de fournisseur et d’une exigence spécifique concernant la gestion de l’authentification tapez. |
| Nom de groupe de connecteur | Le nom logique pour le groupe de connecteurs qui sera désigné pour fournir le canal d’échange et l’authentification unique à cette application principale. |
| Accès des utilisateurs/groupes | Les utilisateurs ou les groupes d’utilisateurs qui auront accès externe à l’application. |
| Conditions supplémentaires | Notez les accès à distance supplémentaire ou les exigences de sécurité qui doivent être factorisés dans la publication de l’application. |

Vous pouvez télécharger cet [feuille de calcul application inventaire](https://aka.ms/appdiscovery) pour inventorier vos applications.

### <a name="define-organizational-requirements"></a>Définir les exigences d’organisation

Les éléments suivants sont des zones pour lesquelles vous devez définir les besoins de votre organisation. Chaque zone contient des exemples d’exigences

 **y accéder**

* Joint à un domaine aux utilisateurs distants ou les utilisateurs d’appareils joints à Azure AD peuvent accéder aux applications publiées en toute sécurité avec transparente-session unique (SSO).

* Les utilisateurs distants dotés d’appareils personnels approuvées peuvent accéder en toute sécurité applications publiées condition qu’ils sont inscrits dans l’authentification Multifacteur et que vous ont inscrit l’application Microsoft Authenticator sur leur téléphone mobile comme méthode d’authentification.

**Gouvernance** 

* Les administrateurs peuvent définir et surveiller le cycle de vie des affectations d’utilisateurs aux applications publiées via le Proxy d’Application.

**Sécurité**

* Seuls les utilisateurs attribués aux applications via l’appartenance au groupe ou individuellement peuvent accéder à ces applications.

**Performances**

* Il n’existe pas de dégradation des performances des applications par rapport à l’accès aux applications à partir du réseau interne.

**Expérience utilisateur**

* Les utilisateurs ne savent comment accéder à leurs applications à l’aide des URL de l’entreprise familiers sur n’importe quelle plateforme d’appareil.

**Audit**
* Les administrateurs sont en mesure d’auditer l’activité d’accès utilisateur.


### <a name="best-practices-for-a-pilot"></a>Meilleures pratiques pour un pilote

Déterminer la quantité de temps et les efforts nécessaires pour une application unique pour l’accès à distance avec authentification unique (SSO) de la commission entièrement. Faire en exécutant un programme pilote qui considère que sa détection initiale, la publication et le test général. À l’aide d’une application web basée sur IIS simple qui est déjà préconfigurée pour l’authentification de Windows intégrée (IWA) permet d’établir une ligne de base, comme cette installation requiert un minimum d’effort pour l’accès à distance avec succès de pilote et de l’authentification unique.

Les éléments suivants de la conception doivent augmenter la réussite de votre implémentation pilote directement dans un client de production.  

**Gestion du connecteur**:  

* Connecteurs jouent un rôle essentiel dans le canal d’échange sur site à vos applications. À l’aide de la **par défaut** groupe de connecteurs est adéquat pour pilote tests initiaux des applications publiées avant leur mise en service en production. Applications correctement testées peuvent ensuite être déplacées vers les groupes de connecteurs de production.

**Gestion des applications**:

* Votre personnel est plus susceptible de se rappeler qu'une URL externe est familières et mieux adaptées. Éviter de publier votre application à l’aide de notre suffixes msappproxy.net ou onmicrosoft.com prédéfinis. Au lieu de cela, fournissez un domaine vérifié niveau supérieur familier, tels que le préfixe avec un nom d’hôte logique *intranet. < customers_domain > .com*.

* Restreindre la visibilité de l’icône de l’application de pilote à un groupe pilote en masquant sa forme d’icône de lancement du portail MyApps d’Azure. Lorsque vous êtes prêt pour la production, vous pouvez étendre l’application à son public ciblé respectif, dans le même client de préproduction, soit en publiant également l’application dans votre client de production.

**Paramètres de l’authentification unique**: Certains paramètres de l’authentification unique ont des dépendances spécifiques qui peuvent prendre un temps pour configurer, par conséquent, évitez de modifier le contrôle des retards en vous assurant de dépendances sont adressés à l’avance. Cela inclut des hôtes de connecteur pour effectuer l’authentification unique à l’aide de la délégation Kerberos (KCD) et en prenant soin d’autres activités du temps de jonction de domaine. Par exemple, vous configurez une instance de la PING Access, si vous avez besoin de l’authentification unique basée sur l’en-tête.

**SSL entre l’hôte de connecteur et l’Application cible**: La sécurité est primordiale, TLS entre les applications d’hôte et cible de connecteur doit toujours être utilisé. En particulier si l’application web est configurée pour l’authentification basée sur des formulaires (FBA), comme les informations d’identification utilisateur sont transmises puis efficacement en texte clair.

**Implémenter de manière incrémentielle et de tester chaque étape**. Effectuez des tests fonctionnels base après la publication d’une application pour vous assurer que tous les utilisateurs et des entreprises sont satisfaites en procédant comme suit :

1. Tester et valider l’accès général à l’application web avec l’authentification préalable désactivée.
2. En cas de réussite activer l’authentification préalable et affecter des utilisateurs et groupes. Tester et valider l’accès.
3. Ensuite, ajoutez la méthode d’authentification unique pour votre application et testez à nouveau pour valider l’accès.
4. Appliquer l’accès conditionnel et les stratégies d’authentification Multifacteur en fonction des besoins. Tester et valider l’accès.

**Outils de dépannage**: Lors du dépannage, toujours commencer par la validation de l’accès à l’application publiée à partir du navigateur sur l’hôte de connecteur et vérifiez que l’application fonctionne comme prévu. Simple votre installation, plus il est facile de déterminer la cause racine, vous devez donc essayer de reproduire les problèmes avec une configuration minimale, telles que l’utilisation qu’un seul connecteur et sans l’authentification unique. Dans certains cas, des outils tels que Fiddler de Telerik de débogage web peut s’avérer indispensable pour résoudre les problèmes d’accès ou le contenu dans les applications accédées via un proxy. Fiddler peut également agir comme un proxy à l’aide de trace et de déboguer le trafic pour les plateformes mobiles tels qu’iOS et Android, et pratiquement tout ce qui peut être configuré pour le routage via un proxy. Consultez le [guide de dépannage](application-proxy-troubleshoot.md) pour plus d’informations.

## <a name="implement-your-solution"></a>Mettre en œuvre votre Solution

### <a name="deploy-application-proxy"></a>Déployer le Proxy d’Application

Les étapes pour déployer votre Proxy d’Application sont traitées dans ce [didacticiel pour l’ajout d’une application en local pour l’accès à distance](application-proxy-add-on-premises-application.md). Si l’installation n’est pas réussie, sélectionnez **dépanner le Proxy d’Application** dans le portail ou utilisez le guide de dépannage [pour les problèmes d’installation de l’Agent de connecteur de Proxy d’Application](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publier des applications via le Proxy d’Application

Publication d’applications part du principe que vous avez suivi toutes les conditions préalables et que vous avez plusieurs connecteurs montrant comme inscrit et actif dans la page Proxy d’Application.

Vous pouvez également publier des applications à l’aide de [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Voici quelques meilleures pratiques à suivre lors de la publication d’une application :

* **Utiliser des groupes de connecteurs**: Affecter un groupe de connecteurs qui a été désigné pour la publication de chaque application. Nous recommandons que chaque groupe de connecteurs ait au moins deux connecteurs pour permettre la mise à l’échelle et haute disponibilité. Avoir trois connecteurs est optimal au cas où vous devrez peut-être un ordinateur à n’importe quel point de service. En outre, consultez [publier des applications sur des réseaux distincts et les emplacements à l’aide de groupes de connecteurs](application-proxy-connector-groups.md) pour voir comment vous pouvez également utiliser des groupes de connecteurs pour segmenter vos connecteurs en réseau ou un emplacement.

* **Définir l’expiration de l’Application back-end**: Ce paramètre est utile dans les scénarios où l’application peut nécessiter plus de 75 secondes traiter une transaction du client. Par exemple lorsqu’un client envoie une requête à une application web qui agit comme un front-end pour une base de données. Le serveur frontal envoie cette requête à son serveur de base de données principale et attend une réponse, mais au moment où qu'il reçoit une réponse, du côté client de la conversation arrive à expiration. Définir le délai à Long fournit pour les transactions plus pendant 180 secondes.

* **Utiliser des Types de Cookie approprié**

   * **Cookie HTTP uniquement**: Fournit une sécurité supplémentaire en demandant le Proxy d’Application à inclure l’indicateur HTTPOnly dans les en-têtes de réponse HTTP set-cookie. Ce paramètre permet de limiter les attaques telles que cross site scripting (XSS). Laisser ce paramètre défini sur non pour les agents utilisateur ou les clients qui ne nécessitent pas l’accès au cookie de session. Par exemple, client RDP/MTSC se connectant à une passerelle des services Bureau à distance est publiée via le Proxy d’application.

   * **Secure Cookie**: Quand un cookie est défini avec l’attribut Secure, l’agent utilisateur (application côté Client) n’inclut que le cookie dans les requêtes HTTP si la demande est transmise via un canal sécurisé TLS. Cela permet d’atténuer le risque d’un cookie compromission sur des canaux de texte clair, et doit être activée.

   * **Cookie persistant**: Permet le cookie de session de Proxy d’Application de persister entre les fermetures de navigateur par restent valides jusqu'à ce qu’il expire ou est supprimé. Utilisé pour les scénarios où une application riche tels qu’office accède à un document dans une application web publiée, sans que l’utilisateur en cours nouveau invité à vous authentifier. Activer avec précaution, toutefois, en tant que cookies persistants peut finalement laisser un service risque d’accès non autorisé, si ne pas utilisé conjointement avec d’autres contrôles de compensation. Ce paramètre doit être utilisé uniquement pour les anciennes applications qui ne peuvent pas partager des cookies entre les processus. Il est préférable de mettre à jour votre application pour gérer le partage des cookies entre les processus au lieu d’utiliser ce paramètre.

* **Traduire les URL dans les en-têtes**: Vous activez cette option pour les scénarios où DNS interne ne peut pas être configuré pour correspondre à espace de noms de l’entreprise publique (appelées environnement DNS fractionné). Si votre application ne nécessite pas l’en-tête d’hôte d’origine dans la demande du client, laissez cette valeur est définie sur Oui. L’alternative consiste à avoir le connecteur à utiliser le nom de domaine complet dans l’URL interne pour le routage du trafic réel et le nom de domaine complet dans l’URL externe, comme l’en-tête d’hôte. Dans la plupart des cas cette alternative doit autoriser l’application de fonctionner normalement, lors de l’accès à distance, mais vos utilisateurs perdent les avantages d’avoir un correspondant à l’intérieur et en dehors de l’URL.

* **Traduisez les URL dans le corps de l’application** : Activer traduction de lien de corps de l’Application pour une application lorsque vous souhaitez que les liens à partir de cette application peut être traduit dans les réponses au client. Si activé, cette fonction fournit une meilleure tentative possible à traduire tous les liens internes que le Proxy d’application se trouve dans le code HTML et CSS réponses renvoyées aux clients. Il est utile lors de la publication d’applications qui contiennent soit absolue codée en dur ou NetBIOS nom court liens dans le contenu, ou avec du contenu avec des liens vers d’autres applications locales.

Pour les scénarios où les applications publiées dans un lien de l’application publiée à d’autres, activer la traduction de lien pour chaque application afin que vous contrôlez l’expérience utilisateur au niveau par application.

Par exemple, supposons que vous avez trois applications publiées via le proxy d’application et toutes liées entre elles : Avantages, dépenses et voyage, plus une quatrième application, des commentaires, ce qui n’est pas publiée via le Proxy d’Application.

![Image 1](media/App-proxy-deployment-plan/link-translation.png)

Lorsque vous activez la traduction de lien pour l’application profits, les liens vers dépenses et transports sont redirigés vers les URL externes pour ces applications, afin que les utilisateurs qui accèdent à des applications à partir de l’extérieur du réseau d’entreprise puissent y accéder. Des liens à partir de dépenses et transports de retour vers profits ne fonctionnent pas, car la traduction de lien n’a pas été activée pour ces deux applications. Le lien vers commentaires n’est pas redirigé, car il n’existe aucune URL externe, donc les utilisateurs à l’aide de l’application profits ne pourront pas accéder à l’application de commentaires à partir de l’extérieur du réseau d’entreprise. Afficher des informations détaillées sur [link translation et autres options de redirection](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Accéder à votre application

Il existe plusieurs options pour gérer l’accès au Proxy d’application les ressources publiées, choisissez donc le plus adapté à vos besoins de scénario et l’évolutivité donnés. Approches courantes incluent : à l’aide de groupes locaux qui sont synchronisés via Azure AD Connect, création de groupes dynamiques dans Azure AD en fonction des attributs, à l’aide de groupes en libre-service qui sont gérés par un propriétaire de la ressource, ou une combinaison de tous ces éléments. Consultez les ressources liées pour les avantages de chacun.

La façon la plus directe de l’affectation d’utilisateurs l’accès à une application va passer à la **utilisateurs et groupes** options dans le volet gauche de votre application publiée et assigner directement des groupes ou des individus.

![Image 24](media/App-proxy-deployment-plan/add-user.png)

Vous pouvez également autoriser les utilisateurs à l’accès en libre-service à votre application en affectant un groupe qui ne sont pas actuellement un membre d’et en configurant les options libre-service.

![Image 25](media/App-proxy-deployment-plan/allow-access.png)

Si activé, puis pourront se connecter à l’accès de demande et de portail MyApps et être automatiquement approuvée et ajouté aux groupes en libre-service déjà autorisé, doivent être approuvées à partir d’un approbateur désigné.

Utilisateurs invités peuvent également être [invités à accéder aux applications internes publiées via le Proxy d’Application via Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Pour les applications locales qui sont normalement accessibles de manière anonyme, aucune authentification, vous pouvez désactiver l’option située dans l’application **propriétés**.

![Image 26](media/App-proxy-deployment-plan/assignment-required.png)


Quitter cette option est définie sur non permet aux utilisateurs d’accéder à l’application en local via le Proxy d’application Azure AD sans autorisations, alors utilisez-les avec précaution.

Une fois que votre application est publiée, il doit être accessible en tapant son URL dans un navigateur externe ou par son icône à [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Activer l’authentification préalable

Vérifiez que votre application est accessible via le Proxy d’Application y accéder via l’URL externe. 

1. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications** et sélectionnez l’application que vous souhaitez gérer.

2. Sélectionnez **Proxy d’application**.

3. Dans le **pré-authentification** champ, utilisez la liste déroulante pour sélectionner **Azure Active Directory**, puis sélectionnez **enregistrer**.

Avec la pré-authentification est activée, Azure AD demande à tout d’abord les utilisateurs pour l’authentification et si l’authentification unique est Perform puis l’application principale sera également vérifier l’utilisateur avant de pouvoir accéder à l’application. Modifier le mode d’authentification préalable de relais à Azure AD configure également l’URL externe avec le protocole HTTPS, afin de n’importe quelle application initialement configurée pour le protocole HTTP est maintenant être sécurisée avec HTTPS.

### <a name="enable-single-sign-on"></a>activation de l'authentification unique

SSO fournit la meilleure expérience possible et la sécurité, car les utilisateurs doivent uniquement se connecter une seule fois lors de l’accès Azure AD. Une fois qu’un utilisateur est déjà authentifié, l’authentification unique est effectuée par l’authentification de connecteur de Proxy d’Application à l’application en local, pour le compte de l’utilisateur. L’application principale traite la connexion comme si elle était l’utilisateur eux-mêmes. 

En choisissant le **Passthrough** option permet aux utilisateurs d’accéder à l’application publiée sans jamais avoir à s’authentifier auprès d’Azure AD.

Exécution de l’authentification unique n’est possible que si Azure AD peut identifier l’utilisateur demande l’accès à une ressource, afin de votre application doit être configurée pour l’authentification préalable des utilisateurs avec Azure AD lors de l’accès pour l’authentification unique de la fonction, sinon les options de l’authentification unique seront désactivées.

Lecture [l’authentification unique aux applications dans Azure AD](what-is-single-sign-on.md) pour vous aider à choisir la méthode plus appropriée de l’authentification unique lors de la configuration de vos applications.

###  <a name="working-with-other-types-of-applications"></a>Utilisation des autres types d’applications

Proxy d’Application Azure AD peut prennent également en charge les applications qui ont été développées pour utiliser notre bibliothèque d’authentification Azure AD ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) ou bibliothèque d’authentification Microsoft ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Il prend en charge les applications clientes natives en consommant Azure AD émis des jetons reçus dans les informations d’en-tête de demande du client pour effectuer l’authentification préalable au profit des utilisateurs.

Lecture [publication d’applications clientes natives et mobiles](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) et [applications basées sur les revendications](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) pour en savoir plus sur les configurations disponibles du Proxy d’Application.

### <a name="use-conditional-access-to-strengthen-security"></a>Utilisez l’accès conditionnel pour renforcer la sécurité

Sécurité des applications nécessite un ensemble de fonctionnalités de sécurité qui peuvent empêcher et répondre à des menaces complexes en local et dans le cloud avancé. Les pirates plus souvent accèdent réseau d’entreprise via faibles, par défaut ou informations d’identification utilisateur volé.  Sécurité basée sur les identités de Microsoft réduit l’utilisation des informations d’identification volées en gérant et en protégeant les identités privilégiées et non privilégié.

Les fonctionnalités suivantes peuvent être utilisées pour prendre en charge de Proxy d’Application Azure AD :

* Utilisateur et accès conditionnel basé sur l’emplacement : Conserver les données sensibles protégées en limitant l’accès utilisateur basé sur l’emplacement géographique ou une adresse IP avec [stratégies d’accès conditionnel basé sur l’emplacement](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Accès conditionnel en fonction du périphérique : Garantir uniquement les appareils inscrits, approuvés et conformes peuvent accéder aux données d’entreprise avec [accès conditionnel basé sur le périphérique](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Accès conditionnel basé sur l’application : Travail ne doit pas arrêter quand un utilisateur n’est pas sur le réseau d’entreprise. [Sécuriser l’accès aux applications d’entreprise de cloud et locales](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) et garder le contrôle avec l’accès conditionnel.

* Accès conditionnel en fonction des risques : Protéger vos données contre les pirates malveillants avec un [stratégie d’accès conditionnel en fonction du risque](https://www.microsoft.com/cloud-platform/conditional-access) qui peuvent être appliquées à toutes les applications et tous les utilisateurs, si locale ou dans le cloud.

* Panneau d’accès Azure AD : Avec votre service de Proxy d’Application déployée et les applications publiées en toute sécurité, proposer aux utilisateurs un concentrateur simple pour découvrir et accéder à toutes leurs applications. Augmentez votre productivité avec les fonctionnalités de libre-service, telles que la possibilité de demander l’accès aux nouvelles applications et aux groupes ou gérer l’accès à ces ressources pour le compte d’autres personnes, via le [volet d’accès](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gérer votre implémentation

### <a name="required-roles"></a>Rôles requis

Microsoft met en œuvre le principe d’accorder le privilège le moins possible pour effectuer les tâches nécessaires à Azure AD. [Passez en revue les différents rôles Azure disponibles](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) et choisir celui qui convient pour répondre aux besoins de chaque personne. Certains rôles peuvent doivent être appliqués temporairement et supprimé une fois le déploiement terminé.

| Rôle d’entreprise| Tâches d’entreprise| Rôles Azure AD |
|---|---|---|
| Help desk admin | Généralement limitée à qualifier utilisateur final a signalé des problèmes et exécution des tâches limitées telles que la modification des mots de passe utilisateurs, invalider les jetons d’actualisation et surveiller l’intégrité du service. | Administrateur du support technique |
| Administrateur d’identité| Problèmes liés à l’authentification AD Azure de lecture dans les rapports et les journaux d’audit pour déboguer le Proxy d’application.| Lecteur de sécurité |
| Propriétaire de l’application| Créer et gérer tous les aspects des applications d’entreprise, les inscriptions d’application et les paramètres de proxy d’application.| Administrateur d’application |
| Administrateur d’infrastructure | Propriétaire de la substitution de certificat | Administrateur d’application |

En réduisant le nombre de personnes qui ont accès pour sécuriser les informations ou ressources vous aidera à réduire le risque d’obtention de tout accès non autorisé ou un utilisateur autorisé une ressource sensible soit accidentellement d’un acteur malveillant. 
 
Toutefois, les utilisateurs doivent toujours effectuer des opérations privilégiées au quotidien, par conséquent, l’application en fonction juste-à-temps (JIT) [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) stratégies à la demande de fournir un accès privilégié aux ressources Azure et Azure AD est notre recommandé approche vers la gestion de l’accès administratif efficacement et d’audit.

### <a name="reporting-and-monitoring"></a>Création de rapports et surveillance

Azure AD peut fournir des informations supplémentaires sur l’approvisionnement l’utilisation et l’intégrité opérationnelle via les journaux d’audit et rapports de l’utilisateur de votre organisation. 

#### <a name="application-audit-logs"></a>Journaux d’audit des applications

Ces journaux fournissent des informations détaillées sur les connexions aux applications configurées avec le Proxy d’Application et de l’appareil et de l’utilisateur l’accès à l’application. Journaux d’audit sont stockés dans le portail Azure et dans l’API d’Audit pour l’exportation.

#### <a name="windows-event-logs-and-performance-counters"></a>Les compteurs de performance et les journaux des événements Windows

Les connecteurs ont admin et session de journaux. Les journaux d’activité admin incluent les événements principaux et leurs erreurs. Les journaux d’activité de session incluent toutes les transactions et les détails de traitement. Journaux et les compteurs sont situés dans des journaux d’événements Windows, suivez ce [didacticiel pour configurer des sources de données de journal des événements dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Étapes et guide de dépannage

En savoir plus sur les problèmes courants et comment les résoudre avec notre guide [dépannage](application-proxy-troubleshoot.md) messages d’erreur. 

Les articles suivants couvrent des scénarios courants qui peuvent également être utilisés pour créer des guides de dépannage pour votre organisation prise en charge. 

* [Problème lors de l’affichage de la page de l’application](application-proxy-page-appearance-broken-problem.md)
* [Le chargement de l’application est trop long](application-proxy-page-load-speed-problem.md)
* [Les liens sur la page de l’application ne fonctionnent pas](application-proxy-page-links-broken-problem.md)
* [Quels ports dois-je ouvrir pour mon application ?](application-proxy-connectivity-ports-how-to.md)
* [Aucun connecteur ne fonctionne dans un groupe de connecteurs pour mon application](application-proxy-connectivity-no-working-connector.md)
* [Configurer dans le portail d’administration](application-proxy-config-how-to.md)
* [Configurer l’authentification unique vers mon application](application-proxy-config-sso-how-to.md)
* [Problème de création d’une application dans le portail d’administration](application-proxy-config-problem.md)
* [Configurer a délégation Kerberos contrainte](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurer avec PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Ne peut pas accéder à cette erreur de l’Application d’entreprise](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problèmes lors de l’installation du connecteur d’agent de proxy d’application](application-proxy-connector-installation-problem.md)
* [Problème de connexion](application-sign-in-problem-on-premises-application-proxy.md)
