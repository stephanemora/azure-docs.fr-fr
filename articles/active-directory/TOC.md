# [Documentation Azure Active Directory](index.md)

# Vue d’ensemble
## [Qu’est-ce qu’Azure Active Directory ?](fundamentals/active-directory-whatis.md)
## [À propos de la gestion des identités Azure](fundamentals/identity-fundamentals.md)
## [Comprendre les solutions d’identité Azure](fundamentals/understand-azure-identity-solutions.md)
## [Choisir une solution d’identité hybride](choose-hybrid-identity-solution.md)
## [Associer les abonnements Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Résidence et considérations relatives aux données](fundamentals/active-directory-data-storage-eu.md)
## [FAQ](fundamentals/active-directory-faq.md)
## [Nouveautés](fundamentals/whats-new.md)


# Prise en main
## [S’inscrire à Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Ajouter un nom de domaine personnalisé](fundamentals/add-custom-domain.md)
## [Configurer la marque de la société](fundamentals/customize-branding.md)
## [Ajouter des utilisateurs dans Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Attribuer des licences à des utilisateurs](fundamentals/license-users-groups.md)
## [Configurer la réinitialisation du mot de passe libre-service](authentication/quickstart-sspr.md)
## [Ajouter les informations de confidentialité de votre organisation dans Azure AD](active-directory-properties-area.md)
## [Accéder à Azure Active Directory pour créer un nouveau locataire](fundamentals/active-directory-access-create-new-tenant.md)


# Procédures
## Planifier et concevoir
### [Comprendre l’architecture Azure AD](fundamentals/active-directory-architecture.md)
### [Mappage des revendications dans Azure Active Directory](develop/active-directory-claims-mapping.md)
### [Déployer une solution d’identité hybride](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### Déterminer la configuration requise
##### [Identité](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [Synchronisation d’annuaires](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Serveurs MFA](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Stratégie votre cycle de vie des identités](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planifier la sécurité des données](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Protection des données](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gestion de contenu](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Contrôle d’accès](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Réponse aux incidents](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planifier votre cycle de vie des identités
##### [Tâches :](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Stratégie d’adoption](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Étapes suivantes](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparaison d’outils](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## Gestion des utilisateurs
### [Ajouter de nouveaux utilisateurs dans Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Gérer les profils utilisateur](fundamentals/active-directory-users-profile-azure-portal.md)
### [Réinitialiser les mots de passe utilisateur](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Partager des comptes](active-directory-sharing-accounts.md)
### [Affecter des utilisateurs aux rôles d’administration](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Ajouter des utilisateurs invités à partir d’un autre répertoire (B2B)](b2b/what-is-b2b.md)
#### [Ajout d’utilisateurs B2B par des administrateurs](b2b/add-users-administrator.md)
#### [Ajout d’utilisateurs B2B par des membres de l’équipe informatique](b2b/add-users-information-worker.md)
#### [API et personnalisation](b2b/customize-invitation-api.md)
#### [Fédération des services Google](b2b/google-federation.md)
#### [Code et exemples Azure PowerShell](b2b/code-samples.md)
#### [Exemple de portail de connexion libre-service](b2b/self-service-portal.md)
#### [E-mail d’invitation](b2b/invitation-email-elements.md)
#### [Échange d’invitation](b2b/redemption-experience.md)
#### [Ajouter des utilisateurs B2B sans invitation](b2b/add-user-without-invite.md)
#### [Autoriser ou bloquer des invitations](b2b/allow-deny-list.md)
#### [Accès conditionnel pour B2B](b2b/conditional-access.md)
#### [Stratégies de partage B2B](b2b/delegate-invitations.md)
#### [Ajouter un utilisateur B2B à un rôle](b2b/add-guest-to-role.md)
#### [Groupes dynamiques et utilisateurs B2B](b2b/use-dynamic-groups.md)
#### [Quitter une organisation](b2b/leave-the-organization.md)
#### [Audit et création de rapports](b2b/auditing-and-reporting.md)
#### [B2B pour les organisations hybrides](b2b/hybrid-organizations.md)
##### [Accorder l’accès aux applications locales pour les utilisateurs B2B](b2b/hybrid-cloud-to-on-premises.md)
##### [Accorder l’accès aux applications cloud pour les utilisateurs locaux](b2b/hybrid-on-premises-to-cloud.md)
#### [Partage externe d’Office 365 et de B2B](b2b/o365-external-user.md)
#### [Licences B2B](b2b/licensing-guidance.md)
#### [Limitations actuelles](b2b/current-limitations.md)
#### [FORUM AUX QUESTIONS](b2b/faq.md)
#### [Résolution de problèmes B2B](b2b/troubleshoot.md)
#### [Comprendre l’utilisateur B2B](b2b/user-properties.md)
#### [Jeton utilisateur B2B](b2b/user-token.md)
#### [B2B pour les applications intégrées Azure AD](b2b/configure-saas-apps.md)
#### [Mappage des revendications d’utilisateur B2B](b2b/claims-mapping.md)
#### [Comparer la collaboration B2B et B2C](b2b/compare-with-b2c.md)
#### [Obtenir une assistance pour B2B](b2b/get-support.md)

## [Gérer les groupes et les membres](fundamentals/active-directory-manage-groups.md)
### [Gestion des groupes](fundamentals/active-directory-groups-create-azure-portal.md)
### [Supprimer un groupe et ses membres](fundamentals/active-directory-groups-delete-group.md)
### [Gérer les paramètres de groupe](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Gérer les rapports](reports-monitoring/overview-reports.md)
### [Activité des connexions](reports-monitoring/concept-sign-ins.md)
### [Activité des audits](reports-monitoring/concept-audit-logs.md)
### [Utilisateurs à risque](reports-monitoring/concept-user-at-risk.md)
### [Connexions risquées](reports-monitoring/concept-risky-sign-ins.md)
### [Événements à risque](reports-monitoring/concept-risk-events.md)
### [Surveillance des journaux avec Azure Monitor](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [FORUM AUX QUESTIONS](reports-monitoring/reports-faq.md)

### Tâches
#### [Télécharger un rapport de connexion](reports-monitoring/quickstart-download-sign-in-report.md)
#### [Télécharger un rapport d’audit](reports-monitoring/quickstart-download-audit-report.md)
#### [Configurer des emplacements nommés](reports-monitoring/quickstart-configure-named-locations.md)
#### [Afficher les rapports d’activité](reports-monitoring/howto-find-activity-reports.md)
#### [Utiliser le pack de contenu Azure AD Power BI](reports-monitoring/howto-power-bi-content-pack.md)
#### [Corriger les problèmes d’utilisateurs avec indicateur de risque](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Acheminer les journaux d’activité dans un hub d’événements Azure](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Archiver les journaux d’activité dans un compte de stockage Azure](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Intégrer les journaux d’activité à Splunk à l’aide d’Azure Monitor](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Intégrer les journaux d’activité à SumoLogic à l’aide d’Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)
#### [Intégrer les journaux d’activité à Log Analytics à l’aide d’Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

### Informations de référence
#### [Rétention](reports-monitoring/reference-reports-data-retention.md)
#### [Latences](reports-monitoring/reference-reports-latencies.md)
#### [Référence d’activité d’audit](reports-monitoring/reference-audit-activities.md)
#### [Codes d’erreur de l’activité de connexion](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Interpréter le schéma de journal d'audit dans Azure Monitor](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Interpréter le schéma de journal de connexion dans Azure Monitor](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Résolution des problèmes
#### [Données manquantes dans les journaux d’activité Azure AD](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Données manquantes dans les téléchargements](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Erreurs de pack de contenu des journaux d’activité Azure AD](reports-monitoring/troubleshoot-content-pack.md)
#### [Erreurs dans l’API Génération de rapports Azure AD](reports-monitoring/troubleshoot-graph-api.md)

### [Accès par programme](reports-monitoring/concept-reporting-api.md)
#### [Composants requis](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Utilisation des certificats](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Gestion des mots de passe](authentication/concept-sspr-howitworks.md)

## Gérer des applications
### [Vue d’ensemble](manage-apps/what-is-application-management.md)
### [Prise en main](manage-apps/plan-an-application-integration.md)
### [Didacticiels d’intégration d’applications SaaS](saas-apps/tutorial-list.md)

### [Approvisionnement/déprovisionnement des utilisateurs pour les applications SaaS](manage-apps/user-provisioning.md) 
#### [Didacticiels d’intégration d’applications](saas-apps/tutorial-list.md) 
#### [Automatiser l’approvisionnement vers des applications prenant en charge le protocole SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Personnalisation des mappages d’attributs](manage-apps/customize-application-attributes.md) 
#### [Écrire des expressions pour les mappages d’attributs](manage-apps/functions-for-customizing-application-data.md) 
#### [Utiliser des filtres d’étendue](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Créer des rapports sur l’approvisionnement d’utilisateurs automatique](manage-apps/check-status-user-account-provisioning.md) 
#### [Résoudre les problèmes d’approvisionnement d’utilisateurs](active-directory-application-provisioning-content-map.md) 

### [Accéder aux applications à distance avec le proxy d’application](manage-apps/application-proxy.md)
#### Prise en main
##### [Activer le Proxy d’application](manage-apps/application-proxy-enable.md)
##### [Publiez des applications](manage-apps/application-proxy-publish-azure-portal.md)
##### [Domaines personnalisés](manage-apps/application-proxy-configure-custom-domain.md)
#### [Authentification unique](manage-apps/application-proxy-single-sign-on.md)
##### [Authentification unique avec KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [Authentification unique avec en-têtes](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [Authentification unique avec mise au coffre des mots de passe](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Concepts
##### [Connecteurs](manage-apps/application-proxy-connectors.md)
##### [Sécurité](manage-apps/application-proxy-security.md)
##### [Réseaux](manage-apps/application-proxy-network-topology.md)


##### [Mise à niveau à partir de TMG ou UAG](manage-apps/application-proxy-migration.md)

#### Configurations avancées
##### [Publier sur des réseaux distincts](manage-apps/application-proxy-connector-groups.md)
##### [Serveurs proxy](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Applications prenant en charge les revendications](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Applications de client natif](manage-apps/application-proxy-configure-native-client-application.md)
##### [Installation sans assistance](manage-apps/application-proxy-register-connector-powershell.md)
##### [Page d’accueil personnalisée](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Traduire les liens inclus](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Caractères génériques](manage-apps/application-proxy-wildcard.md)
##### [Supprimer des données personnelles](manage-apps/application-proxy-remove-personal-data.md)


#### Publication de procédures pas à pas
##### [Bureau à distance](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Résolution des problèmes](manage-apps/application-proxy-troubleshoot.md)

### Gérer les applications d’entreprise
#### [Ajouter une application](manage-apps/add-application-portal.md)
#### [Afficher les applications du client](manage-apps/view-applications-portal.md)
#### [Configurer l’authentification unique](manage-apps/configure-single-sign-on-portal.md)
#### [Affecter des utilisateurs](manage-apps/assign-user-or-group-access-portal.md)
#### [Personnaliser la marque](manage-apps/change-name-or-logo-portal.md)
#### [Comment désactiver les connexions des utilisateurs ?](manage-apps/disable-user-sign-in-portal.md)
#### [Supprimer des utilisateurs](manage-apps/remove-user-or-group-access-portal.md)

#### [Gérer l’approvisionnement de comptes d’utilisateurs](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Signature de certificat avancée pour les applications SAML](manage-apps/certificate-signing-options.md)
#### [Masquer une application à partir de l’expérience utilisateur](manage-apps/hide-application-from-user-portal.md)
### [Configurer l’accélération automatique de la connexion à l’aide de la stratégie HRD Policy](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrer des applications AD FS vers Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Gérer l’accès aux applications](manage-apps/what-is-access-management.md)
#### [Accès par authentification unique](manage-apps/what-is-single-sign-on.md)
#### [Certificats pour authentification unique](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Restrictions du client](manage-apps/tenant-restrictions.md)
#### [Utilisez SCIM approvisionner les utilisateurs](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Comprendre les expériences de consentement de l’application Azure AD](develop/application-consent-experience.md)

### Résolution des problèmes



#### Volet d'accès
##### [L’application n’apparaît pas](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Une application inattendue apparaît](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [Connexion impossible](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Erreur lors de l’installation de l’extension du navigateur](manage-apps/access-panel-extension-problem-installing.md)
##### [Guide pratique pour utiliser l’accès aux applications en libre-service](manage-apps/access-panel-manage-self-service-access.md)
##### [Erreur lors de l’utilisation de l’accès aux applications en libre-service](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Ajout d’une application
##### [Choisir le type d’application](manage-apps/choose-application-type.md)
##### [Problèmes courants - applications de la galerie](manage-apps/adding-gallery-app-common-problems.md)
##### [Problèmes courants - applications ne figurant pas dans la galerie](manage-apps/adding-non-gallery-app-common-problems.md)

#### Proxy d’application
##### [Problème lors de l’affichage de la page de l’application](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [Le chargement de l’application est trop long](manage-apps/application-proxy-page-load-speed-problem.md)
##### [Les liens sur la page de l’application ne fonctionnent pas](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Quels ports dois-je ouvrir pour mon application ?](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Aucun connecteur ne fonctionne dans un groupe de connecteurs pour mon application](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Configurer dans le portail d’administration](manage-apps/application-proxy-config-how-to.md)
##### [Configurer l’authentification unique vers mon application](manage-apps/application-proxy-config-sso-how-to.md)
##### [Problème de création d’une application dans le portail d’administration](manage-apps/application-proxy-config-problem.md)
##### [Configurer a délégation Kerberos contrainte](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Configurer avec PingAccess](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [Erreur « Impossible d’accéder à cette application d’entreprise »](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problèmes lors de l’installation du connecteur d’agent de proxy d’application](manage-apps/application-proxy-connector-installation-problem.md)


#### Inscription de l’application
##### [Entrer les champs pour l’objet d’application](develop/registration-config-specific-application-property-how-to.md)
##### [Modifier les valeurs par défaut de la durée de vie du jeton](develop/registration-config-change-token-lifetime-how-to.md)

#### Authentification
##### [Configuration des points de terminaison](develop/registration-config-how-to.md)

#### Accès conditionnel
##### [Le client n’a pas respecté les pré-requis d’inscription d’appareils](active-directory-conditional-access.md)
##### [Comment et quand les règles de réseau d’entreprise prennent-elles effet ?](https://aka.ms/calocation)
##### [Comment augmenter le nombre d’appareils que l’utilisateur est autorisé à inscrire dans Azure AD ?](active-directory-azureadjoin-setup.md)
##### [Comment configurer un accès conditionnel pour Exchange Online ?](https://aka.ms/csforexchange)
##### [Comment configurer un accès conditionnel pour les appareils Windows 7 ?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Quelles applications sont prises en charge avec un accès conditionnel ?](active-directory-conditional-access-supported-apps.md)

#### Rechercher une API
##### [Rechercher une API](develop/api-find-an-api-how-to.md)

#### Gestion de l’accès
##### [Affecter des utilisateurs et des groupes à une application](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Supprimer l’accès d’un utilisateur à une application](manage-apps/methods-for-removing-user-access.md)
##### [Configuration de l’accès aux applications en libre-service](manage-apps/manage-self-service-access.md)
##### [Utilisateur attribué inattendu](manage-apps/ways-users-get-assigned-to-applications.md)
##### [Application inattendue dans la liste des applications](manage-apps/application-types.md)

#### Applications multi-locataires
##### [Configurer une nouvelle application](develop/setup-multi-tenant-app.md)
##### [Ajouter à la galerie d’applications](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Autorisations
##### [Choisir des autorisations pour une API](develop/perms-for-given-api.md)
##### [Autorisations déléguées et d’application](develop/delegated-and-app-perms.md)
##### [Consentement d’application](develop/consent-framework.md)

#### Approvisionnement
##### [Temps nécessaire](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Approvisionnement très lent - application de galerie](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Configurer l’approvisionnement des utilisateurs - application de galerie](manage-apps/application-provisioning-config-how-to.md)
##### [Problème de configuration de l’approvisionnement des utilisateurs - application de galerie](manage-apps/application-provisioning-config-problem.md)
##### [Problème d’enregistrement des informations d’identification d’administrateur lors de la configuration de l’approvisionnement des utilisateurs pour une application de galerie](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [Les utilisateurs ne sont pas approvisionnés - application de galerie](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Les mauvais utilisateurs ont été provisionnés - application de galerie](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Authentification unique
##### [Choisir une méthode](manage-apps/single-sign-on-modes.md)
##### [Configurer](develop/registration-config-sso-how-to.md)
##### [Configurer l’authentification fédérée - applications de galerie](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Problèmes courants de configuration d’authentification fédérée - applications de galerie](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Configurer l’authentification fédérée - applications ne figurant pas dans la galerie](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Problèmes courants de configuration de l’authentification fédérée - applications ne figurant pas dans la galerie](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Configurer le mot de passe - applications de galerie](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Problèmes courants de configuration du mot de passe - applications de galerie](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Configurer le mot de passe - applications ne figurant pas dans la galerie](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Problèmes courants de configuration du mot de passe - applications ne figurant pas dans la galerie](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Problèmes de connexion utilisateur
##### [Invite de consentement inattendue](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Erreur de consentement de l’utilisateur](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Problèmes de connexion à partir du portail personnalisé](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Problèmes de connexion à partir du panneau d’accès](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Erreur sur la page de connexion de l’application](manage-apps/application-sign-in-problem-application-error.md)
##### [Problème avec le mot de passe de l’authentification unique - applications ne figurant pas dans la galerie](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Problème avec le mot de passe de l’authentification unique - applications de galerie](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Problème de connexion à une application Microsoft](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Problème avec le mot de passe de l’authentification unique - application ne figurant pas dans la galerie](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problème avec le mot de passe de l’authentification unique - application de galerie](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Problème avec l’application personnalisée](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Problème avec l’application locale - Proxy d’application](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)

### [Développer des applications](active-directory-applications-guiding-developers-for-lob-applications.md)


## Gérer votre répertoire
### [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### Noms de domaine personnalisés
#### [Démarrage rapide](fundamentals/add-custom-domain.md)
### [Administration de votre annuaire](fundamentals/active-directory-administer.md)
### [Intégrer des identités locales à l’aide d’Azure AD Connect](hybrid/whatis-hybrid-identity.md)

### [Durées de vie des jetons](develop/active-directory-configurable-token-lifetimes.md)

## Sécuriser vos identités

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## [Déployer AD FS dans Azure](hybrid/how-to-connect-fed-azure-adfs.md)
### [Haute disponibilité](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Algorithme de hachage de signature de modification](active-directory-federation-sha256-guidance.md)

## [Résolution des problèmes](fundamentals/active-directory-troubleshooting-support-howto.md)

## Déploiement de la preuve de concept (POC) d’Azure AD
### [Manuel POC : Introduction](active-directory-playbook-intro.md)
### [Manuel POC : Ingrédients](active-directory-playbook-ingredients.md)
### [Manuel POC : Mise en œuvre](active-directory-playbook-implementation.md)
### [Manuel POC : Blocs de construction](active-directory-playbook-building-blocks.md)

# Informations de référence
## [Exemples de code](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Applets de commande Azure PowerShell](/powershell/azure/overview)
## [Informations de référence sur l’API Java](/java/api)
## [API .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# Rubriques connexes
## [Azure Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
## [Azure pour développeurs](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# Ressources
## [Plans de déploiement d’Azure AD](./fundamentals/active-directory-deployment-plans.md)
## [Forum de commentaires Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Tarification](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=active-directory)
## [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
