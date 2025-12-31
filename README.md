Projet Papers Please - Infrastructure Linux Securisee

1. Presentation du projet
Ce projet met en oeuvre un systeme complet de controle dematerialise permettant la gestion securisee de dossiers sous forme d'archives ZIP. La solution automatise la chaine de traitement depuis le depot initial en zone publique jusqu'a l'archivage et la prise de decision en zone interne.

Le flux de travail se decompose en quatre etapes cles :

Depot des archives en zone DMZ.
Verification automatique de conformite (clean-up).
Transfert securise vers le serveur interne.
Traitement administratif par les inspecteurs (acceptation, rejet, recherche).

2. Architecture reseau et securite

L'infrastructure est segmentee en trois zones distinctes pour garantir l'isolation des donnees:
DMZ (192.168.10.0/24) : Zone dediee au depot externe via SFTP.
Reseau Interne (192.168.20.0/24) : Zone protegee pour le stockage RAID et le poste de travail inspecteur.
Le serveur GW-FW constitue le point de passage unique assurant les fonctions suivantes :

Routage inter-reseaux et filtrage par pare-feu.
Service DNS interne (BIND9) pour le domaine gov.local.
Service DHCP (isc-dhcp-server) pour l'attribution automatique des configurations reseau.

3. Services et serveurs deployes
   
EXT-FILES (Serveur de Fichiers Externe - DMZ)
Dedie au depot des archives par les utilisateurs externes.
Acces securise via SFTP avec environnement chroot pour limiter l'exposition du systeme.
Execution locale du script de nettoyage pour isoler les fichiers non conformes.

INT-FILES (Serveur de Fichiers Interne - Reseau Interne)
Hebergement de la base de dossiers valides.
Mise en place d'un RAID 1 logiciel pour assurer la tolerance de panne et la continuite de service.
Service de notification mail base sur Postfix pour alerter les inspecteurs a l'arrivee de nouveaux dossiers.

4. Automatisation et scripting
   
La logique metier est portee par une suite de scripts Bash et Python permettant de reduire la latence operationnelle:
cleanup.sh : Analyse la conformite des archives ZIP (format, fichiers requis, identifiants) et les classe dans les repertoires "validated" ou "rejected".
transfer_cases.sh : Transfere uniquement les archives validees vers la zone interne via le protocole SCP.
pipeline.sh : Ordonnance l'execution successive du nettoyage et du transfert via des taches cron.
accept.sh / reject.sh : Outils utilises par les inspecteurs pour classer les dossiers entrants sur INT-FILES.
search.sh : Script permettant de rechercher un identifiant specifique et d'afficher son statut actuel (Accepte, Rejete, En attente ou Inconnu).


Ahmadou DIALLO 
Etudiant à SUPINFO.
