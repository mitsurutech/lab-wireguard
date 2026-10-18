# Lab WireGuard : un tunnel VPN entre deux sites, dans une seule VM Ubuntu

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml)

Un **lab WireGuard** prêt à l’emploi pour la classe, issu de la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les établissements donnent à chaque élève ses propres machines et un terminal dans le navigateur. Chaque élève reçoit sa propre VM Ubuntu, dans laquelle sont construits deux petits sites, chacun avec une passerelle et un hôte, reliés par un lien « internet » qui ne connaît ni l’un ni l’autre des réseaux privés. L’élève génère une **paire de clés WireGuard** sur chaque passerelle, écrit `wg0.conf`, met le **tunnel VPN** en service et fait passer les deux réseaux par ce tunnel, jusqu’à ce qu’un hôte d’un site puisse envoyer un ping à un hôte de l’autre. Rien n’est installé sur l’ordinateur de l’élève.

Une passerelle a besoin de sa propre machine et un élève n’a qu’une VM : chaque hôte et chaque passerelle est donc un **espace de noms réseau** (network namespace) et chaque lien est une paire de câbles virtuels. Les vraies commandes fonctionnent, et c’est tout l’intérêt : `wg genkey`, `wg pubkey`, `wg show`, `wg-quick`, `ip route`, `tcpdump`.

## Le lab

| Lab | Fichier | Ce que fait l’élève | Page d’aide |
| --- | --- | --- | --- |
| Tunnel WireGuard entre deux sites | [`wireguard.lab.yaml`](wireguard.lab.yaml) | Créer une paire de clés sur chaque passerelle, garder les clés privées privées, écrire la configuration, mettre le tunnel en service, faire passer les deux LAN par ce tunnel, observer les paquets chiffrés sur le lien internet et faire en sorte que tout survive à un redémarrage | [Lab WireGuard](https://mitsurutech.co.uk/fr/help/lab-wireguard) |

Le lab laisse un README dans le dossier personnel de l’élève, qui explique quoi faire sans donner aucune réponse. Il comporte une **vérification**, un script qui note une VM rendue sans que l’enseignant ait à la lire : elle s’exécute sur une copie de la machine enregistrée après un démarrage à neuf, et ne voit donc que ce que l’élève a enregistré dans `wg0.conf`. Elle n’est réussie que si l’état réel est bon : un échange de clés (handshake) récent sur les deux passerelles, le trafic d’un ping qui passe par `wg0`, rien d’autre que de l’UDP sur le lien internet et aucune adresse privée visible à cet endroit, les clés privées lisibles par personne d’autre, et aucune route qui envoie un réseau privé par le lien internet. Une simple route statique entre les deux réseaux ne passe pas.

## Ce que contient le fichier

`wireguard.lab.yaml` est une **définition de lab VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 1 CPU, 1024 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : aucun, le lab, c’est le terminal |
| `setup` | un script exécuté une fois, en tant que root, au premier démarrage de la VM de chaque élève : il installe `wireguard-tools` et `tcpdump` depuis l’archive d’Ubuntu, et écrit la commande `wglab` ainsi que le README du lab |
| `check` | le script de notation, exécuté en tant que root sur une copie du disque enregistré après un démarrage à neuf ; son code de sortie est le résultat et `0` signifie réussi |

La commande `wglab` qu’il installe : `wglab status` liste les machines, `wglab shell gwA` ouvre un shell dans une passerelle, et `wglab down` puis `wglab up` reconstruit le lab et remet le tunnel en service à partir des fichiers enregistrés.

## L’utiliser sur la plateforme

**Pour le donner comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab VM Ubuntu**.
2. Appuyez sur l’exemple, ou collez le contenu du fichier ci-dessus. Utiliser un exemple remplace le texte, et Annuler rapporte le vôtre.
3. Publiez et essayez d’abord votre propre copie. Chaque élève a sa propre VM, conservée d’une session à l’autre.
4. Distribuez-la comme n’importe quelle tâche. Une fois rendue, lancez la vérification sur la ligne d’un élève dans la liste de la classe : elle démarre une copie de sa machine enregistrée, qui n’est pas réenregistrée, et indique si elle a réussi et ce qu’elle a affiché.

Le [guide des labs VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) du manuel et sa [page des labs d’exemple](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste.

Pas encore de compte pour votre établissement ? [Votre première année est gratuite](https://mitsurutech.co.uk/fr/pricing) jusqu’à 50 élèves, et [créer un compte](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérification du fichier

À chaque push, `.github/workflows/check.yml` vérifie que le fichier est un YAML valide avec les cinq clés ci-dessus et que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme. Il ne démarre pas de VM : cela se fait sur les machines de la plateforme.

## Ce qui ne fonctionne pas dans un lab

La VM accède à l’archive de paquets d’Ubuntu, à PyPI, à npm et à GitHub, et à rien d’autre : un lab ne peut donc pas être dirigé vers un hôte sur internet. C’est une seule VM, donc les deux sites sont construits à l’intérieur et l’« internet » entre eux est un lien virtuel.

## À propos

Créé par [Mitsuru Technologies](https://mitsurutech.co.uk) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs de VM, des applications web déployées depuis GitHub et des quiz qui se corrigent seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même éditeur : [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin) et [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea).

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`.

[WireGuard](https://www.wireguard.com) est sous licence GPL-2.0 (les outils, et le module du noyau d’Ubuntu) et est installé depuis l’archive d’Ubuntu au démarrage d’une VM ; rien de lui n’est distribué ici.
