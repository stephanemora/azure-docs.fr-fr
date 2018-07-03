# [Documentation Azure Active Directory](index.md)

# Vue d'ensemble
## [Qu’est-ce qu’Azure Active Directory ?](fundamentals/active-directory-whatis.md)
## [À propos de la gestion des identités Azure](fundamentals/identity-fundamentals.md)
## [Comprendre les solutions d’identité Azure](fundamentals/understand-azure-identity-solutions.md)
## [Choisir une solution d’identité hybride](choose-hybrid-identity-solution.md)
## [Associer les abonnements Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Résidence et considérations relatives aux données](fundamentals/active-directory-data-storage-eu.md)
## [FAQ](fundamentals/active-directory-faq.md)
## [Nouveautés](fundamentals/whats-new.md)


# Prise en main
## [Prise en main d'Azure AD](fundamentals/get-started-azure-ad.md)
## [S’inscrire à Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Ajouter un nom de domaine personnalisé](fundamentals/add-custom-domain.md)
## [Configurer la marque de la société](fundamentals/customize-branding.md)
## [Ajouter des utilisateurs dans Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Attribuer des licences à des utilisateurs](fundamentals/license-users-groups.md)
## [Configurer la réinitialisation du mot de passe libre-service](authentication/quickstart-sspr.md)
## [Ajouter les informations de confidentialité de votre organisation dans Azure AD](active-directory-properties-area.md)


# Procédures
## Planifier et concevoir
### [Comprendre l’architecture Azure AD](fundamentals/active-directory-architecture.md)
### [Mappage des revendications dans Azure Active Directory](active-directory-claims-mapping.md)
### [Déployer une solution d’identité hybride](active-directory-hybrid-identity-design-considerations-overview.md)
#### Déterminer la configuration requise
##### [Identité](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Synchronisation d’annuaires](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Serveurs MFA](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Stratégie votre cycle de vie des identités](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planifier la sécurité des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Protection des données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planifier votre cycle de vie des identités
##### [Tâches :](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Stratégie d’adoption](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Étapes suivantes](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparaison d’outils](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Gestion des utilisateurs
### [Ajouter de nouveaux utilisateurs dans Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Gérer les profils utilisateur](fundamentals/active-directory-users-profile-azure-portal.md)
### [Partager des comptes](active-directory-sharing-accounts.md)
### [Affecter des utilisateurs aux rôles d’administration](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Restaurer un utilisateur supprimé](fundamentals/active-directory-users-restore.md)
### [Ajouter des utilisateurs invités à partir d’un autre répertoire (B2B)](b2b/what-is-b2b.md)
#### [Ajout d’utilisateurs B2B par des administrateurs](b2b/add-users-administrator.md)
#### [Ajout d’utilisateurs B2B par des membres de l’équipe informatique](b2b/add-users-information-worker.md)
#### [API et personnalisation](b2b/customize-invitation-api.md)
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
### Gérer les groupes
#### [Portail Azure](fundamentals/active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell pour Graph (v2)](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](active-directory-accessmanagement-groups-settings-cmdlets.md)
### [Sélectionner les membres du groupe](fundamentals/active-directory-groups-members-azure-portal.md)
### [Gérer les propriétaires de groupe](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [Ajouter une appartenance à un groupe](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Attribuer des licences à l’aide de groupes](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [Attribuer des licences à un groupe](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identifier et résoudre les problèmes de licence dans un groupe](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrer des utilisateurs individuels titulaires d’une licence aux licences par groupe](active-directory-licensing-group-migration-azure-portal.md)
#### [Migrer des utilisateurs entre les licences de produits](active-directory-licensing-group-product-migration.md)
#### [Scénarios supplémentaires pour la gestion des licences par groupe](active-directory-licensing-group-advanced.md)
#### [Exemples Azure PowerShell pour les licences basées sur les groupes](active-directory-licensing-ps-examples.md)
#### [Référence pour les produits et les plans de service dans Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Configurer l’expiration des groupes Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Appliquer une stratégie d’affectation de noms pour les groupes](groups-naming-policy.md)
### [Afficher tous les groupes](fundamentals/active-directory-groups-view-azure-portal.md)
### [Ajouter l’accès de groupe aux applications SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Restaurer un groupe Office 365 supprimé](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Gérer les paramètres de groupe](fundamentals/active-directory-groups-settings-azure-portal.md) 
### Créer des règles avancées
#### [Portail Azure](active-directory-groups-dynamic-membership-azure-portal.md)
### [Configurer des groupes libre-service](active-directory-accessmanagement-self-service-group-management.md)
### [Résolution des problèmes](active-directory-accessmanagement-troubleshooting.md)

## [Gérer les rapports](active-directory-reporting-azure-portal.md)
### [Activité des connexions](active-directory-reporting-activity-sign-ins.md)
### [Activité des audits](active-directory-reporting-activity-audit-logs.md)
### [Utilisateurs à risque](active-directory-reporting-security-user-at-risk.md)
### [Connexions risquées](active-directory-reporting-security-risky-sign-ins.md)
### [Événements à risque](active-directory-reporting-risk-events.md)
### [FORUM AUX QUESTIONS](active-directory-reporting-faq.md)
### Tâches
#### [Configurer des emplacements nommés](active-directory-named-locations.md)
#### [Afficher les rapports d’activité](active-directory-reporting-migration.md)
#### [Utiliser le pack de contenu Power BI Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Corriger les problèmes d’utilisateurs avec indicateur de risque](active-directory-report-security-user-at-risk-remediation.md)
### Informations de référence
#### [Rétention](active-directory-reporting-retention.md)
#### [Latences](active-directory-reporting-latencies-azure-portal.md)
#### [Référence d’activité d’audit](active-directory-reporting-activity-audit-reference.md)
#### [Codes d’erreur de l’activité de connexion](active-directory-reporting-activity-sign-ins-errors.md)
#### [Authentification multifacteur](active-directory-reporting-activity-sign-ins-mfa.md)



### Résolution des problèmes
#### [Données d’audit manquantes](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Données manquantes dans les téléchargements](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Erreurs de pack de contenu des journaux d’activité Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Accès par programme](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Composants requis](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Exemples d’audit](active-directory-reporting-api-audit-samples.md)
#### [Exemples de connexion](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Utilisation des certificats](active-directory-reporting-api-with-certificates.md)

## Gestion des mots de passe
### [Vue d’ensemble des mots de passe](authentication/active-directory-passwords-overview.md)
### Documents utilisateur
#### [Réinitialiser ou modifier votre mot de passe](active-directory-passwords-update-your-own-password.md)
#### [Meilleures pratiques relatives aux mots de passe](active-directory-secure-passwords.md)
#### [S’inscrire pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md)
### [Fonctionnement SSPR](authentication/concept-sspr-howitworks.md)
### [Guide de déploiement SSPR](authentication/howto-sspr-deployment.md)
### [SSPR et Windows 10](authentication/tutorial-sspr-windows.md)
### [Stratégies SSPR](authentication/concept-sspr-policy.md)
### [Personnalisation SSPR](authentication/concept-sspr-customization.md)
### [Spécifications des données SSPR](authentication/howto-sspr-authenticationdata.md)
### [SSPR Reporting](authentication/howto-sspr-reporting.md)
### [Verrouillage intelligent](authentication/howto-password-smart-lockout.md)
### [Éliminer les mots de passe faibles](authentication/concept-password-ban-bad.md)
### [Configurer la liste des mots de passe interdits](authentication/howto-password-ban-bad.md)
### [Intégration locale](authentication/concept-password-ban-bad-on-premises.md)
### [Déployer la protection par mot de passe d’Azure AD](authentication/howto-password-ban-bad-on-premises.md)
### [Configurer la protection par mot de passe d’Azure AD](authentication/howto-password-ban-bad-on-premises-operations.md)
### [Surveiller la protection par mot de passe d’Azure AD](authentication/howto-password-ban-bad-on-premises-troubleshoot.md)
### Administrateurs informatiques : réinitialiser les mots de passe
#### [Portail Azure](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Licence SSPR](authentication/concept-sspr-licensing.md)
### [Réécriture du mot de passe](authentication/howto-sspr-writeback.md)
### [Résolution des problèmes](authentication/active-directory-passwords-troubleshoot.md)
### [FORUM AUX QUESTIONS](authentication/active-directory-passwords-faq.md)


## Gérer des appareils
### [Introduction](device-management-introduction.md)
### [À l’aide du portail Azure](device-management-azure-portal.md)
### [Planifier l’exécution d’Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [FAQ](device-management-faq.md)
### Tâches
#### [Configurer les appareils Windows 10 inscrits dans Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Configurer les appareils joints Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Configurer les appareils joints Azure AD hybrides](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Déployer en local](active-directory-device-registration-on-premises-setup.md)
#### [Enregistrement dans Azure AD lors de la première exécution de Windows 10](device-management-azuread-joined-devices-frx.md)
### Résolution des problèmes
#### [Appareils Windows 10 et Windows Server 2016 joints Azure AD hybrides](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Appareils Windows hérités joints Azure AD](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Gérer des applications
### [Vue d’ensemble](manage-apps/what-is-application-management.md)
### [Prise en main](manage-apps/plan-an-application-integration.md)
### [Didacticiels d’intégration d’applications SaaS](saas-apps/tutorial-list.md)

### [Approvisionnement/déprovisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md) 
#### [Didacticiels d’intégration d’applications](saas-apps/tutorial-list.md) 
#### [Automatiser l’approvisionnement vers des applications prenant en charge le protocole SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Personnalisation des mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md) 
#### [Écrire des expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Utiliser des filtres d’étendue](active-directory-saas-scoping-filters.md) 
#### [Créer des rapports sur l’approvisionnement d’utilisateurs automatique](active-directory-saas-provisioning-reporting.md) 
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
##### [Caractères génériques](active-directory-application-proxy-wildcard.md)
##### [Supprimer des données personnelles](manage-apps/application-proxy-remove-personal-data.md)


#### Publication de procédures pas à pas
##### [Bureau à distance](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Résolution des problèmes](manage-apps/application-proxy-troubleshoot.md)

### Gérer les applications d’entreprise
#### [Affecter des utilisateurs](manage-apps/assign-user-or-group-access-portal.md)
#### [Personnaliser la marque](manage-apps/change-name-or-logo-portal.md)
#### [Comment désactiver les connexions des utilisateurs ?](manage-apps/disable-user-sign-in-portal.md)
#### [Supprimer des utilisateurs](manage-apps/remove-user-or-group-access-portal.md)
#### [Afficher toutes mes applications](manage-apps/view-applications-portal.md)
#### [Gérer l’approvisionnement de comptes d’utilisateurs](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Gérer l’authentification unique pour les applications d’entreprise](manage-apps/configure-single-sign-on-portal.md)
#### [Signature de certificat avancée pour les applications SAML](manage-apps/certificate-signing-options.md)
#### [Masquer une application à partir de l’expérience utilisateur](manage-apps/hide-application-from-user-portal.md)
### [Configurer l’accélération automatique de la connexion à l’aide de la stratégie HRD Policy](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrer des applications AD FS vers Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Gérer l’accès aux applications](manage-apps/what-is-access-management.md)
#### [Accès par authentification unique](manage-apps/what-is-single-sign-on.md)
#### [Certificats pour authentification unique](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Restrictions du client](manage-apps/tenant-restrictions.md)
#### [Utilisez SCIM approvisionner les utilisateurs](manage-apps/use-scim-to-provision-users-and-groups.md)


### Résolution des problèmes



#### Volet d'accès
##### [L’application n’apparaît pas](application-access-panel-unexpected-application-not-appearing.md)
##### [Une application inattendue apparaît](application-access-panel-unexpected-application-appears.md)
##### [Connexion impossible](application-access-panel-web-sign-in-problem.md)
##### [Erreur lors de l’installation de l’extension du navigateur](application-access-panel-extension-problem-installing.md)
##### [Guide pratique pour utiliser l’accès aux applications en libre-service](application-access-panel-self-service-applications-how-to.md)
##### [Erreur lors de l’utilisation de l’accès aux applications en libre-service](application-access-panel-self-service-applications-problem.md)

#### Ajout d’une application
##### [Choisir le type d’application](application-config-add-app-problem-how-to-choose-application-type.md)
##### [Problèmes courants - applications de la galerie](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [Problèmes courants - applications ne figurant pas dans la galerie](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### Proxy d’application
##### [Problème lors de l’affichage de la page de l’application](application-proxy-page-appearance-broken-problem.md)
##### [Le chargement de l’application est trop long](application-proxy-page-load-speed-problem.md)
##### [Les liens sur la page de l’application ne fonctionnent pas](application-proxy-page-links-broken-problem.md)
##### [Quels ports dois-je ouvrir pour mon application ?](application-proxy-connectivity-ports-how-to.md)
##### [Aucun connecteur ne fonctionne dans un groupe de connecteurs pour mon application](application-proxy-connectivity-no-working-connector.md)
##### [Configurer dans le portail d’administration](application-proxy-config-how-to.md)
##### [Configurer l’authentification unique vers mon application](application-proxy-config-sso-how-to.md)
##### [Problème de création d’une application dans le portail d’administration](application-proxy-config-problem.md)
##### [Configurer a délégation Kerberos contrainte](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Configurer avec PingAccess](application-proxy-back-end-ping-access-how-to.md)
##### [Erreur « Impossible d’accéder à cette application d’entreprise »](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problèmes lors de l’installation du connecteur d’agent de proxy d’application](application-proxy-connector-installation-problem.md)


#### Inscription de l’application
##### [Entrer les champs pour l’objet d’application](application-dev-registration-config-specific-application-property-how-to.md)
##### [Modifier les valeurs par défaut de la durée de vie du jeton](application-dev-registration-config-change-token-lifetime-how-to.md)

#### Authentification
##### [Configuration des points de terminaison](application-dev-registration-config-how-to.md)

#### Accès conditionnel
##### [Le client n’a pas respecté les pré-requis d’inscription d’appareils](active-directory-conditional-access.md)
##### [Le client est bloqué en raison d’un réglage incorrect des stratégies d’accès conditionnel](active-directory-conditional-access-device-remediation.md)
##### [Comment et quand les règles de réseau d’entreprise prennent-elles effet ?](https://aka.ms/calocation)
##### [Comment augmenter le nombre d’appareils que l’utilisateur est autorisé à inscrire dans Azure AD ?](active-directory-azureadjoin-setup.md)
##### [Comment configurer un accès conditionnel pour Exchange Online ?](https://aka.ms/csforexchange)
##### [Comment configurer un accès conditionnel pour les appareils Windows 7 ?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Quelles applications sont prises en charge avec un accès conditionnel ?](active-directory-conditional-access-supported-apps.md)

#### Rechercher une API
##### [Rechercher une API](application-dev-api-find-an-api-how-to.md)

#### Gestion de l’accès
##### [Affecter des utilisateurs et des groupes à une application](application-access-assignment-how-to-add-assignment.md)
##### [Supprimer l’accès d’un utilisateur à une application](application-access-assignment-how-to-remove-assignment.md)
##### [Configuration de l’accès aux applications en libre-service](application-access-self-service-how-to.md)
##### [Utilisateur attribué inattendu](application-access-unexpected-user-assignment.md)
##### [Application inattendue dans la liste des applications](application-access-unexpected-application.md)

#### Applications multi-locataires
##### [Configurer une nouvelle application](application-dev-setup-multi-tenant-app.md)
##### [Ajouter à la galerie d’applications](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Autorisations
##### [Choisir des autorisations pour une API](application-dev-perms-for-given-api.md)
##### [Accorder des autorisations à mon application](application-dev-registration-config-grant-permissions-how-to.md)
##### [Autorisations déléguées et d’application](application-dev-delegated-and-app-perms.md)
##### [Consentement d’application](application-dev-consent-framework.md)

#### Approvisionnement
##### [Temps nécessaire](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Approvisionnement très lent - application de galerie](application-provisioning-when-will-provisioning-finish.md)
##### [Configurer l’approvisionnement des utilisateurs - application de galerie](application-provisioning-config-how-to.md)
##### [Problème de configuration de l’approvisionnement des utilisateurs - application de galerie](application-provisioning-config-problem.md)
##### [Problème d’enregistrement des informations d’identification d’administrateur lors de la configuration de l’approvisionnement des utilisateurs pour une application de galerie](application-provisioning-config-problem-storage-limit.md)
##### [Les utilisateurs ne sont pas approvisionnés - application de galerie](application-provisioning-config-problem-no-users-provisioned.md)
##### [Les mauvais utilisateurs ont été provisionnés - application de galerie](application-provisioning-config-problem-wrong-users-provisioned.md)

#### Authentification unique
##### [Choisir une méthode](application-config-sso-how-to-choose-sign-on-method.md)
##### [Configurer](application-dev-registration-config-sso-how-to.md)
##### [Configurer l’authentification fédérée - applications de galerie](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [Problèmes courants de configuration d’authentification fédérée - applications de galerie](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [Configurer l’authentification fédérée - applications ne figurant pas dans la galerie](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [Problèmes courants de configuration de l’authentification fédérée - applications ne figurant pas dans la galerie](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [Configurer le mot de passe - applications de galerie](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [Problèmes courants de configuration du mot de passe - applications de galerie](application-config-sso-problem-configure-password-sso-gallery.md)
##### [Configurer le mot de passe - applications ne figurant pas dans la galerie](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [Problèmes courants de configuration du mot de passe - applications ne figurant pas dans la galerie](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### Problèmes de connexion utilisateur
##### [Invite de consentement inattendue](application-sign-in-unexpected-user-consent-prompt.md)
##### [Erreur de consentement de l’utilisateur](application-sign-in-unexpected-user-consent-error.md)
##### [Problèmes de connexion à partir du portail personnalisé](application-sign-in-other-problem-deeplink.md)
##### [Problèmes de connexion à partir du panneau d’accès](application-sign-in-other-problem-access-panel.md)
##### [Erreur sur la page de connexion de l’application](application-sign-in-problem-application-error.md)
##### [Problème avec le mot de passe de l’authentification unique - applications ne figurant pas dans la galerie](application-sign-in-problem-password-sso-non-gallery.md)
##### [Problème avec le mot de passe de l’authentification unique - applications de galerie](application-sign-in-problem-password-sso-gallery.md)
##### [Problème de connexion à une application Microsoft](application-sign-in-problem-first-party-microsoft.md)
##### [Problème avec le mot de passe de l’authentification unique - application ne figurant pas dans la galerie](application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problème avec le mot de passe de l’authentification unique - application de galerie](application-sign-in-problem-federated-sso-gallery.md)
##### [Problème avec l’application personnalisée](application-sign-in-problem-custom-dev.md)
##### [Problème avec l’application locale - Proxy d’application](application-sign-in-problem-on-premises-application-proxy.md)

### [Développer des applications](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Bibliothèque de documents ](active-directory-apps-index.md)

## Gérer votre répertoire
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Noms de domaine personnalisés
#### [Démarrage rapide](fundamentals/add-custom-domain.md)
#### [Ajouter des noms de domaines personnalisés](active-directory-domains-manage-azure-portal.md)
### [Administration de votre annuaire](fundamentals/active-directory-administer.md)
### [Supprimer un répertoire](directory-delete-howto.md)
### [Plusieurs répertoires](active-directory-licensing-directory-independence.md)
### [Inscription libre-service](active-directory-self-service-signup.md)
### [Reprendre un répertoire non managé](domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Activer](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Paramètres de stratégie de groupe](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Paramètres Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Résolution des problèmes](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Intégrer des identités locales à l’aide d’Azure AD Connect](./connect/active-directory-aadconnect.md)

## Déléguer l’accès aux ressources
### [Afficher les membres d’un rôle d’administrateur](directory-manage-roles-portal.md)
### [Rôles d’administrateur](active-directory-assign-admin-roles-azure-portal.md)
#### [Affecter un rôle d’administrateur à un utilisateur](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [Comparer les autorisations des invités et des membres](fundamentals/users-default-permissions.md)
### [Renforcer la sécurité du rôle administrateur](admin-roles-best-practices.md)  
#### [Créer les comptes d’administrateur d’accès d’urgence](active-directory-admin-manage-emergency-access-accounts.md)
### [Unités administratives](active-directory-administrative-units-management.md)
### [Durées de vie des jetons](active-directory-configurable-token-lifetimes.md)

## Révisions d’accès
### [Vue d’ensemble des révisions d’accès](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Effectuer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md)
### [Créer une révision d’accès](active-directory-azure-ad-controls-create-access-review.md)
### [Exécution d’une révision de l’accès](active-directory-azure-ad-controls-perform-access-review.md)
### [Exécuter une révision d’accès](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Accès invité avec révisions d’accès](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Gestion de l’accès utilisateur avec les révisions](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Gestion des programmes et des contrôles](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Récupérer les résultats de la révision d’accès](active-directory-azure-ad-controls-retrieve-access-review.md)

## Sécuriser vos identités
### [Accès conditionnel](active-directory-conditional-access-azure-portal.md)
#### [Prise en main](active-directory-conditional-access-azure-portal-get-started.md)
#### Démarrages rapides
##### [Configurer une MFA par application cloud](active-directory-conditional-access-app-based-mfa.md)
##### [Exiger l’acceptation des conditions d’utilisation](active-directory-conditional-access-tou.md)
#### Didacticiels
##### [Migrer une stratégie MFA classique](active-directory-conditional-access-migration-mfa.md)
#### Concepts
##### [Protection de la ligne de base](active-directory-conditional-access-baseline-protection.md)
##### [Conditions](active-directory-conditional-access-conditions.md)
##### [Conditions d’emplacement](active-directory-conditional-access-locations.md)
##### [Contrôles](active-directory-conditional-access-controls.md)
##### [Outil de simulation](active-directory-conditional-access-whatif.md)
##### [Comprendre les stratégies d’appareil pour les services Office 365](active-directory-conditional-access-device-policies.md)
#### Procédures
##### [meilleures pratiques](active-directory-conditional-access-best-practices.md)
##### [Configurer des stratégies d’accès conditionnel pour les tentatives d’accès à partir de réseaux non approuvés](active-directory-conditional-access-untrusted-networks.md)
##### [Configurer l’accès conditionnel basé sur les appareils](active-directory-conditional-access-policy-connected-applications.md)
##### [Configurer l’accès conditionnel basé sur les applications](active-directory-conditional-access-mam.md)
##### [Fournir des conditions d’utilisation pour les utilisateurs et les applications](active-directory-tou.md)
##### [Migrer les stratégies classiques](active-directory-conditional-access-migration.md)
##### [Configurer la connectivité VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Configurer SharePoint et Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Correction](active-directory-conditional-access-device-remediation.md)
#### [Référence technique](active-directory-conditional-access-technical-reference.md)
#### [FAQ](active-directory-conditional-faqs.md)

### Authentification par certificat
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Prise en main](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Activer](active-directory-identityprotection-enable.md)
#### [Détecter les vulnérabilités](active-directory-identityprotection-vulnerabilities.md)
#### [Événements à risque](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [Expérience de connexion](active-directory-identityprotection-flows.md)
#### [Simuler des événements à risque](active-directory-identityprotection-playbook.md)
#### [Débloquer des utilisateurs](active-directory-identityprotection-unblock-howto.md)
#### [FAQ](active-directory-identity-protection-faqs.md)
#### [Glossaire](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Intégrer d’autres services avec Azure AD 
### [Intégrer LinkedIn avec Azure AD](linkedin-integration.md)

## [Déployer AD FS dans Azure](active-directory-aadconnect-azure-adfs.md)
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
## [Restrictions et limites du service](active-directory-service-limits-restrictions.md)

# Rubriques connexes
## [Azure Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure pour développeurs](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Ressources
## [Forum de commentaires Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Tarification](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=active-directory)
## [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
