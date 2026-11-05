# Dépôts Debian Jessie et APT

> [!WARNING]
> ReadyNAS OS 6.9.6 s'appuie sur une base Debian ancienne. Debian 8 « Jessie »
> a perdu son support de sécurité régulier en juin 2018 et son support LTS en
> juin 2020. Les dépôts d'archive servent à retrouver d'anciens paquets ; ils
> ne rendent pas le NAS à jour ni sûr sur Internet.

## Ce que contient `ssh/UpdDebianRepo.txt`

Le fichier note d'abord les miroirs Jessie historiques, puis propose de les
remplacer par des dépôts d'archives et d'ajouter dans `/etc/apt/apt.conf` :

```text
Get::AllowUnauthenticated true;
Acquire::Check-Valid-Until false;
```

Le guide DOCX de migration ajoute en outre `[trusted=yes]` sur des lignes APT.
**Ne copiez pas ces options comme réglage permanent.** `AllowUnauthenticated`
et `[trusted=yes]` désactivent des protections importantes contre les paquets
non authentifiés ; `Check-Valid-Until false` désactive le contrôle de fraîcheur
des métadonnées. Une archive peut être correcte historiquement tout en
contenant des logiciels vulnérables et ne plus recevoir de correctifs.

## Référence historique relevée dans les notes

Cette configuration est reproduite uniquement pour identifier ce qui a été
fait sur le NAS ; ce n'est pas une configuration recommandée :

```text
deb https://apt.readynas.com/packages/readynasos 6.9.6 updates apps main
deb [trusted=yes] http://archive.kernel.org/debian-archive/debian jessie main contrib non-free
deb [trusted=yes] http://archive.kernel.org/debian-archive/debian jessie-backports main contrib non-free
deb [trusted=yes] http://archive.debian.org/debian-security jessie/updates main non-free contrib
```

Les URL, métadonnées signées, clés et paquets peuvent évoluer ou disparaître.
Ne pas présumer que le dépôt NETGEAR mentionné pour la branche 6.9.6 est encore
disponible ou maintenu.

## Recommandation d'administration

- Si le NAS fonctionne, ne pas modifier APT uniquement pour « mettre Debian à
  jour ». Ne pas exécuter `apt upgrade` ou `apt dist-upgrade` sur le système
  ReadyNAS pour une maintenance ordinaire : cela peut remplacer des versions
  attendues par le micrologiciel.
- Si un paquet ancien doit absolument être récupéré, identifier d'abord son
  besoin, son architecture et ses dépendances. Préférer un paquet conservé
  localement et vérifié par une source indépendante.
- Faire une sauvegarde de la configuration avant toute modification. Garder
  une copie des fichiers originaux `/etc/apt/sources.list` et
  `/etc/apt/apt.conf` et documenter chaque ligne modifiée.
- Travailler hors d'Internet ou dans un segment isolé, sans partage de données
  sensibles. Les archives HTTP ne protègent pas le transport.
- Ne désactiver la vérification cryptographique des paquets que si le risque
  est compris et accepté pour une opération ponctuelle en environnement
  isolé ; restaurer ensuite les protections initiales. Une réussite de
  `apt-get update` ne prouve ni l'authenticité des paquets ni l'absence de
  vulnérabilités.

## Modifier les sources (uniquement si une opération contrôlée l'exige)

La note locale utilise `vi` et la connexion root. Avant édition :

1. Se connecter sur le réseau d'administration seulement et sauvegarder les
   fichiers APT originaux.
2. Vérifier que les URL et signatures sont encore valides depuis une source
   Debian de confiance. Ne pas ajouter `trusted=yes` pour faire disparaître
   une erreur de signature.
3. Modifier une seule entrée à la fois, relire la configuration, puis vérifier
   précisément l'origine et la signature des index.
4. Ne pas lancer d'installation ou de mise à niveau globale en réponse à un
   avertissement de dépôt.
5. Restaurer la configuration initiale si la tâche ne nécessite plus l'accès
   aux archives.

La documentation Debian confirme le statut archivé de Jessie et la disponibilité
des anciennes distributions dans son archive officielle. Voir
[Sources](07-sources.md).

