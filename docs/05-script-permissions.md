# Script `dirtyfix-readynas-os6-treestructure.sh`

## Conclusion

Le nom du script peut faire penser à un montage réseau, mais son contenu
inspecté consiste en une très longue série de `cd`, `chmod` et `chown` sur
l'arborescence de `/`. Il **ne monte pas de partage réseau**. Il modifie
directement modes et propriétaires de nombreux fichiers système et applicatifs.

Il n'a pas été exécuté pendant la rédaction de cette documentation.

## Risques observés

- La première séquence part de `/` et applique des changements à des chemins
  sensibles (`/etc`, `/usr`, `/var`, `/apps`, etc.).
- Certaines permissions sont très ouvertes : le script contient notamment
  `chmod 777 apps`, `chmod 777 sync.conf` et des fichiers avec le mode `666`.
  Ces modes permettent à tout utilisateur local d'écrire selon le chemin et
  peuvent exposer des données ou configurations.
- Il impose des propriétaires comme `root:root` ou `admin:admin`, qui ne sont
  pas nécessairement adaptés à l'état réel du NAS ou aux applications
  installées.
- Le script est volumineux et ne semble pas fournir de prévisualisation,
  sauvegarde automatique ni restauration transactionnelle. Une erreur ou une
  interruption peut laisser un état partiellement modifié.
- Les permissions d'un montage réseau dépendent également du protocole et de
  ses options ; changer les modes du système ne crée pas un lecteur réseau
  Windows et ne donne pas légitimement accès au firmware depuis le réseau.

## Recommandation

**Ne pas lancer ce script tel quel sur un NAS utilisé.** Si un accès à un
fichier est refusé :

1. Déterminer le chemin exact et le service qui doit y accéder.
2. Vérifier le propriétaire, le groupe, les ACL et le point de montage
   (`ls -ld`, `getfacl`, `mount`) avant toute modification.
3. Sauvegarder les métadonnées et le fichier, puis appliquer uniquement les
   droits minimaux requis au chemin concerné.
4. Tester avec le compte/service concerné et confirmer que l'administration
   et les partages existants fonctionnent toujours.

Pour lire ou copier les fichiers du système, utiliser SSH/SFTP avec un compte
autorisé plutôt que d'ouvrir globalement les permissions. Pour connecter un
lecteur Windows, utiliser un partage SMB explicitement publié, par exemple
`\\NAS\NomDuPartage`. Un partage SMB global de `/` serait une exposition très
dangereuse et n'est pas recommandé.

