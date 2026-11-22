# OSIRIS : plateforme SIEM/XDR Wazuh sur Azure

**OSIRIS** (Open Source SIEM for Incident Response and Infrastructure Security) est un projet académique réalisé en équipe de 4 à l'Efrei Paris (2026). Il consiste à concevoir, comparer puis déployer progressivement une plateforme de supervision de sécurité open source, du poste de travail jusqu'au cloud.

> Preuve de concept (POC) académique, pas une architecture de production.

## Objectif

Centraliser les journaux de sécurité d'une infrastructure hétérogène (Windows, Linux, web, réseau), détecter les événements suspects, produire des alertes exploitables et automatiser une réponse sur un cas d'usage précis.

## Démarche en 3 phases

| | Phase 1, All-in-One | Phase 2, Multi-node | Phase 3, Azure + Docker |
|---|---|---|---|
| Environnement | VMware local | Machines virtuelles locales | Microsoft Azure |
| Composants Wazuh | Sur une seule machine | Séparés sur trois nœuds | Conteneurs Docker Compose |
| Détection | Règles de base | Règles personnalisées, corrélation | MITRE ATT&CK, threat intelligence |
| Réponse | Détection seule | Alerting par criticité | Active Response (blocage automatique) |

Un état de l'art préalable a comparé Wazuh, Splunk, QRadar, Sentinel, Graylog et la stack Elastic. Wazuh a été retenu pour son coût nul, ses agents Windows et Linux et ses modules intégrés (FIM, conformité, MITRE).

## Architecture de la phase 3

- **Azure** : groupe de ressources dédié, réseau virtuel privé, groupe de sécurité réseau limitant l'administration (SSH, RDP, HTTPS) à l'adresse de l'administrateur.
- **3 machines virtuelles** :
  - serveur Ubuntu : Wazuh 4.13.1 (Manager, Indexer, Dashboard) en conteneurs Docker Compose ;
  - serveur Linux : Apache, IDS Suricata, source Syslog simulée, agent Wazuh ;
  - Windows Server 2022 : contrôleur de domaine Active Directory, agent Wazuh.
- **Chaîne de traitement** : source, agent ou Syslog, Manager (analyse par règles), Indexer (stockage), Dashboard (visualisation).

## Cas d'usage démontrés

- **Brute force SSH** : détection, association à MITRE ATT&CK, puis blocage temporaire de l'adresse source par Active Response (`firewall-drop`).
- **File Integrity Monitoring** : alerte de niveau élevé sur la modification d'un fichier sensible.
- **Windows et Active Directory** : remontée des événements du contrôleur de domaine.
- **Réseau** : alertes Suricata (règles Emerging Threats) et logs Syslog sans agent.
- **Conformité** : tableaux de bord Wazuh PCI DSS, RGPD, NIST 800-53, HIPAA (mapping d'alertes, pas une certification).

## Limites assumées

- Le cœur Wazuh est en **single-node** containerisé : l'infrastructure est multi-machines côté sources, mais ce n'est pas un cluster haute disponibilité. Le quota Azure for Students (6 vCPU) a imposé ce choix.
- La source firewall/switch est **simulée** via Syslog.
- Les tableaux de conformité n'équivalent pas à une certification.
- Mots de passe par défaut conservés pour les démonstrations : à remplacer avant tout usage réel.

## Perspectives

Cluster Wazuh avec haute disponibilité, playbooks SOAR, sources de threat intelligence externes (VirusTotal, IOC), automatisation du déploiement (Terraform, CI/CD).

## Compétences mises en œuvre

Wazuh · Docker Compose · Azure · Suricata · Active Directory · Syslog · MITRE ATT&CK · FIM · Active Response · documentation technique
