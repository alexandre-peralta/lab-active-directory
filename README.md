Laboratoire Active Directory

Infrastructure Windows Server 2022 redondante pour une organisation fictive, le domaine alecrim.local, montée sur VMware Fusion dans un réseau local isolé 192.168.10.0/24.

Objectif

Construire, tester et documenter de bout en bout une infrastructure Active Directory redondante: deux contrôleurs de domaine, DNS et DHCP redondants, serveur de fichiers, stratégies de groupe et accès distant. Chaque service est vérifié par des tests avant de passer au suivant.

Architecture

Machine	Adresse	Rôles
SRV-DC1	192.168.10.10	AD DS, DNS, DHCP
SRV-DC2	192.168.10.11	AD DS, DNS, catalogue global
SRV-FILE1	192.168.10.20	Serveur de fichiers, DFS, VPN SSTP
PC-ALECRIM-01	DHCP (192.168.10.100 réservée)	Poste client joint au domaine

Les serveurs occupent les adresses basses en IP statique, les postes clients sont servis par DHCP dans une plage séparée.

Ce qui a été réalisé

	•	Deux contrôleurs de domaine avec réplication active et vérifiée
	•	DNS intégré à l'annuaire, zones directe et inversée, redondance côté serveurs et côté clients
	•	DHCP redondant par relation de basculement (équilibrage 50/50), validé par une panne simulée puis une reprise complète
	•	Unités d'organisation, comptes utilisateurs et groupes de sécurité, création de comptes en masse par script PowerShell et fichier CSV
	•	Serveur de fichiers avec modèle AGDLP et permissions NTFS différenciées par service
	•	Redondance DFS: espace de noms multi-serveurs et réplication DFSR testée entre SRV-FILE1 et SRV-DC2
	•	Stratégies de groupe: politique de mot de passe granulaire, lecteur réseau déployé vers l'espace de noms DFS, verrouillage automatique de session, application vérifiée avec gpresult
	•	Accès distant VPN SSTP de bout en bout: rôle, étendue d'adresses, certificat auto-signé approuvé côté client, autorisation du compte, connexion testée et accès à un partage

Chiffres clés

	•	2 contrôleurs de domaine redondants
	•	97 captures d'écran
	•	21 anomalies diagnostiquées et résolues
	•	1 panne réelle simulée et surmontée

Méthode

Chaque machine virtuelle est d'abord installée avec une carte réseau en NAT pour disposer d'Internet pendant l'installation, puis basculée sur le réseau isolé avec une adresse fixe une fois les téléchargements terminés. Le DHCP de VMware est désactivé sur le réseau isolé pour que le serveur Windows soit seul à distribuer les adresses.

Difficultés rencontrées

Les 21 anomalies sont décrites avec leur diagnostic et leur solution dans le PDF, par exemple des échecs de dcdiag, une zone de recherche inversée absente, un enregistrement PTR en double, un écart d'horloge entre le poste client et les contrôleurs, ou une connexion VPN bloquée par le changement de mot de passe obligatoire.

Limites assumées

Le laboratoire n'est pas un environnement de production: versions d'évaluation, mises à jour non appliquées, 2 Go de RAM par serveur, aucune sauvegarde, site AD unique, certificat VPN auto-signé, et test VPN réalisé depuis le réseau interne. Ces écarts sont documentés en regard de la pratique en production.

Compétences démontrées

Active Directory, DNS, DHCP Failover, PowerShell, DFS et réplication, stratégies de groupe, AGDLP, VPN SSTP, Kerberos, diagnostic réseau et d'annuaire, documentation technique.

Documentation

Le PDF complet de 86 pages, avec toutes les étapes, les captures d'écran et les tests de validation, se trouve dans ce repository.