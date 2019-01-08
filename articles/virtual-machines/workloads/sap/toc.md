# Vue d’ensemble
## [Prise en main](get-started.md)
## [Certifications](sap-certifications.md)
# SAP HANA sur Azure (grandes instances)
## Vue d’ensemble
### [Qu’est-ce que SAP HANA sur Azure (grandes instances) ?](hana-overview-architecture.md)
### [Connaître les conditions](hana-know-terms.md)
### [Certification](hana-certification.md)
### [Références (SKU) disponibles pour HLI](hana-available-skus.md)
### [Dimensionnement](hana-sizing.md)
### [Conditions d’intégration](hana-onboarding-requirements.md)
### [Hiérarchisation des données et des nœuds d’extension SAP HANA](hana-data-tiering-extension-nodes.md)
### [Responsabilités et modèle opérationnel](hana-operations-model.md)
## Architecture
### [Architecture générale](hana-architecture.md)
### [Architecture réseau](hana-network-architecture.md)
### [Architecture de stockage](hana-storage-architecture.md)
### [Scénarios HLI pris en charge](hana-supported-scenario.md)
## Infrastructure et connectivité
### [Déploiement HLI](hana-overview-infrastructure-connectivity.md)
### [Connexion de machines virtuelles Azure à de grandes instances HANA](hana-connect-azure-vm-large-instances.md)
### [Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute](hana-connect-vnet-express-route.md)
### [Conditions requises supplémentaires pour le réseau](hana-additional-network-requirements.md)
## Installer SAP HANA
### [Valider la configuration](hana-installation.md)
### [Exemple d’installation HANA](hana-example-installation.md)
## Haute disponibilité et récupération d’urgence
### [Options et considérations](hana-overview-high-availability-disaster-recovery.md)
### [Sauvegarde et restauration](hana-backup-restore.md)
### [Principes et préparation](hana-concept-preparation.md)
### [Procédure de basculement en cas de récupération d’urgence](hana-failover-procedure.md)
## Résolution des problèmes et surveillance
### [Surveillance HLI](troubleshooting-monitoring.md)
### [Surveillance et dépannage à partir de HANA](hana-monitor-troubleshoot.md)
## Procédures
### [Configuration de la haute disponibilité avec STONITH](ha-setup-with-stonith.md)
### [Sauvegarde du système d’exploitation pour références SKU de type II](os-backup-type-ii-skus.md)
### [Mise à niveau du système d’exploitation pour les grandes instances HANA](os-upgrade-hana-large-instance.md)
### [Configuration de serveur SMT pour SUSE Linux](hana-setup-smt.md)
# SAP HANA sur des machines virtuelles Azure
## [Installation d’une instance SAP HANA unique](hana-get-started.md)
## [Guide de déploiement de S/4 HANA ou BW/4 HANA SAP CAL](cal-s4h.md)
## [Configurations et opérations de l’infrastructure SAP HANA sur Azure](hana-vm-operations.md)
## Disponibilité de SAP HANA sur les machines virtuelles Azure
### [Présentation de la disponibilité de SAP HANA sur Azure](sap-hana-availability-overview.md)
### [Disponibilité de SAP HANA sur Azure au sein d’une région Azure](sap-hana-availability-one-region.md)
### [Disponibilité de SAP HANA sur Azure dans l’ensemble des régions Azure](sap-hana-availability-across-regions.md)
### [Installer la réplication de système SAP HANA sur SLES](sap-hana-high-availability.md)
### [Installer la réplication de système SAP HANA sur RHEL](sap-hana-high-availability-rhel.md)
### [Résoudre les problèmes de scale-out de SAP HANA et Pacemaker sur SLES](hana-vm-troubleshoot-scale-out-ha-on-sles.md)
## [Vue d’ensemble de la sauvegarde SAP HANA](sap-hana-backup-guide.md)
## [Sauvegarde SAP HANA au niveau des fichiers](sap-hana-backup-file-level.md)
## [Capture instantanée de stockage SAP HANA](sap-hana-backup-storage-snapshots.md)
# SAP NetWeaver et Business One sur les machines virtuelles Azure
## [Liste de contrôle relative à la planification et au déploiement de la charge de travail SAP](sap-deployment-checklist.md)
## [Planifier l’implémentation de SAP NetWeaver dans Azure](planning-guide.md)
## [Guide de déploiement NetWeaver SAP](deployment-guide.md)
## Guides de déploiement SGBD pour charge de travail SAP
### [Déploiement SGBD de machines virtuelles générales Azure pour charge de travail SAP](dbms_guide_general.md)
### [Déploiement SGBD de machines virtuelles SQL Server Azure pour charge de travail SAP](dbms_guide_sqlserver.md)
### [Déploiement SGBD de machines virtuelles Oracle Azure pour charge de travail SAP](dbms_guide_oracle.md)
### [Déploiement SGBD de machines virtuelles IBM DB2 Azure pour charge de travail SAP](dbms_guide_ibm.md)
### [Déploiement SGBD de machines virtuelles SAP ASE Azure pour charge de travail SAP](dbms_guide_sapase.md)
### [Déploiement Content Server, liveCache et SAP MaxDB sur Azure](dbms_guide_maxdb.md)
### Disponibilité de SAP HANA sur les machines virtuelles Azure
### [Présentation de la disponibilité de SAP HANA sur Azure](sap-hana-availability-overview.md)
### [Disponibilité de SAP HANA sur Azure au sein d’une région Azure](sap-hana-availability-one-region.md)
### [Disponibilité de SAP HANA sur Azure dans l’ensemble des régions Azure](sap-hana-availability-across-regions.md)
## [SAP Business One sur les machines virtuelles Azure](business-one-azure.md)
## [Guide de déploiement SAP IDES sur Windows/SQL Server SAP CAL](cal-ides-erp6-erp7-sp3-sql.md)
## [SAP NetWeaver sur les machines virtuelles Azure Linux](suse-quickstart.md)
## [Connecteur SAP LaMa pour Azure](lama-installation.md)
## Haute disponibilité (HA) sur Windows et Linux
### [Vue d'ensemble](sap-high-availability-guide-start.md)
### Architecture haute disponibilité
#### [Architecture haute disponibilité et scénarios](sap-high-availability-architecture-scenarios.md)
#### [Architecture à plus haute disponibilité et scénarios](sap-higher-availability-architecture-scenarios.md)
#### [Haute disponibilité sur Windows avec disque partagé pour l’instance (A)SCS](sap-high-availability-guide-wsfc-shared-disk.md)
#### [Haute disponibilité sur Windows avec partage de fichiers SOFS pour l’instance (A)SCS](sap-high-availability-guide-wsfc-file-share.md)
#### [Haute disponibilité sur SUSE Linux pour l’instance (A)SCS](high-availability-guide-suse.md)
#### [HA sur Red Hat Enterprise Linux pour les instances (A)SCS](high-availability-guide-rhel.md)
### Préparation de l’infrastructure Azure
#### [Windows avec disque partagé pour l’instance (A)SCS](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [Windows avec partage de fichiers SOFS pour l’instance (A)SCS](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [Haute disponibilité pour NFS sur des machines virtuelles Azure sur SLES](high-availability-guide-suse-nfs.md)
#### [GlusterFS sur les machines virtuelles Azure sur Red Hat Enterprise Linux pour SAP NetWeaver](high-availability-guide-rhel-glusterfs.md)
#### [Pacemaker sur SLES](high-availability-guide-suse-pacemaker.md)
#### [Pacemaker sur RHEL](high-availability-guide-rhel-pacemaker.md)
### Installation de SAP
#### [Windows avec disque partagé pour l’instance (A)SCS](sap-high-availability-installation-wsfc-shared-disk.md)
#### [Windows avec partage de fichiers SOFS pour l’instance (A)SCS](sap-high-availability-installation-wsfc-file-share.md)
#### [SUSE Linux avec NFS pour l’instance (A)SCS](high-availability-guide-suse.md)
#### [Haute disponibilité pour SAP NetWeaver sur Red Hat Enterprise Linux](high-availability-guide-rhel.md)
### Multi-SID SAP
#### [Windows avec disque partagé pour l’instance (A)SCS](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [Windows avec partage de fichiers SOFS pour l’instance (A)SCS](sap-ascs-ha-multi-sid-wsfc-file-share.md)
##  [Récupération d’urgence de SAP avec Azure Site Recovery](../../../site-recovery/site-recovery-workload.md#protect-sap)
# Intégration d’identité d’AAD SAP et authentification unique
## [Intégration avec SAP Cloud](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Intégration d’AAD avec la plateforme cloud d’identification SAP](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Configurer l’authentification unique avec la plateforme cloud SAP](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Intégration d’AAD avec SAP Netweaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Intégration d’AAD avec SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Intégration d’AAD avec le gestionnaire de base de données SAP HANA](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[Authentification unique SAP Fiori Launchpad SAML avec Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# Intégration des services Azure dans SAP
## [Utiliser SAP HANA dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
## [DirectQuery et SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
## [Utiliser le connecteur SAP BW dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector)
## [Azure Data Factory offre l’intégration des données SAP HANA et Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)
# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/)

